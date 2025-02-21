----
title: "AI：基于 Eino + DeepSeek 配合 RSS 实现的每日简报功能"
cover: https://s.flc.io/202502201313598.png!blog
date: 2025-02-20 13:09:26
categories:
- 编程
toc: true
tags:
- AI
- Eino
- RSS
----

## 功能说明

- 支持日期、星期
- 支持天气、温度、湿度
- 支持新闻摘要

## 直接看代码

```go
package daily

import (
	"bytes"
	"context"
	"encoding/json"
	"errors"
	"fmt"
	"io"
	"net/http"
	"testing"
	"time"

	"github.com/cloudwego/eino-ext/components/model/ark"
	"github.com/cloudwego/eino/components/model"
	"github.com/cloudwego/eino/compose"
	"github.com/cloudwego/eino/schema"
	"github.com/mmcdole/gofeed"
	"github.com/stretchr/testify/require"
)

var fp = gofeed.NewParser()

func createModel(t *testing.T) model.ChatModel {
	deepseek, err := ark.NewChatModel(t.Context(), &ark.ChatModelConfig{
		BaseURL: "https://ark.cn-beijing.volces.com/api/v3",
		APIKey:  "[key]", // 请填写你的 API Key
		Model:   "deepseek-v3-241226",
	})
	require.NoError(t, err)
	return deepseek
}

func TestDailyReport(t *testing.T) {
	chain := compose.NewChain[string, *schema.Message]().
		AppendParallel(
			compose.NewParallel().
				AddLambda("weather", compose.InvokableLambda(func(ctx context.Context, input string) (string, error) {
					return fetchWeather(t), nil
				})).
				AddGraph("news",
					compose.NewChain[string, string]().
						AppendLambda(compose.InvokableLambda(func(ctx context.Context, input string) (*gofeed.Feed, error) {
							return fp.ParseURL(input)
						})).
						AppendLambda(compose.InvokableLambda(func(ctx context.Context, input *gofeed.Feed) (string, error) {
							var buffer bytes.Buffer
							for i, item := range input.Items {
								buffer.WriteString(fmt.Sprintf("%d. %s\n", i+1, item.Title))
							}
							return buffer.String(), nil
						})),
				),
		).
		AppendLambda(compose.InvokableLambda(func(ctx context.Context, input map[string]any) (string, error) {
			var buffer bytes.Buffer
			buffer.WriteString(fmt.Sprintf("今日日期： %s, %s\n\n", time.Now().Format(time.DateOnly), time.Now().Weekday()))
			buffer.WriteString(fmt.Sprintf("今日天气： %s\n", input["weather"].(string)))
			buffer.WriteString(fmt.Sprintf("今日新闻：\n%s\n", input["news"].(string)))

			return buffer.String(), nil
		})).
		AppendGraph(
			compose.NewChain[string, *schema.Message]().
				AppendLambda(compose.InvokableLambda(func(ctx context.Context, input string) ([]*schema.Message, error) {
					return []*schema.Message{
						schema.SystemMessage(`You are a highly professional news summarization expert, capable of categorizing and organizing the article materials I provide into a summary format, presented in a point-by-point manner. Ultimately, deliver it to me in plain text, without Markdown syntax, in Chinese, as today's news summary.
**Format as follows:**

今天是 2021 年 3 月 19 日，星期五。天气晴朗，气温 20 度。以下是今日新闻摘要：

1. 企业动态:

	- 华为将于3月20日至21日举行中国合作伙伴大会2025。
	- 模速空间、无问芯穹和上海仪电推出国内首个“算力生态超市”。

2. 科技行业:

	- 最低调“六小虎”阶跃星辰开年首秀，Agent落地智能终端，印奇也参与。
	- “AI服务商”阿里巴巴值得重估。

...
`),
						schema.UserMessage("Here are the articles:\n\n" + input),
					}, nil
				})).AppendChatModel(createModel(t)),
		)

	r, err := chain.Compile(t.Context())
	require.NoError(t, err)

	message, err := r.Stream(t.Context(), "https://36kr.com/feed")
	require.NoError(t, err)

	for {
		msg, err := message.Recv()
		if err != nil {
			if errors.Is(err, io.EOF) {
				break
			}
		}
		require.NoError(t, err)
		fmt.Print(msg.Content)
	}
}

type weatherResult struct {
	Status   string `json:"status"`
	Count    string `json:"count"`
	Info     string `json:"info"`
	InfoCode string `json:"infocode"`
	Lives    []struct {
		Province         string `json:"province"`
		City             string `json:"city"`
		AdCode           string `json:"adcode"`
		Weather          string `json:"weather"`
		Temperature      string `json:"temperature"`
		WindDirection    string `json:"winddirection"`
		WindPower        string `json:"windpower"`
		Humidity         string `json:"humidity"`
		ReportTime       string `json:"reporttime"`
		TemperatureFloat string `json:"temperature_float"`
		HumidityFloat    string `json:"humidity_float"`
	} `json:"lives"`
}

func fetchWeather(t *testing.T) string {
	resp, err := http.Get("https://restapi.amap.com/v3/weather/weatherInfo?key=[key]&city=440300&extensions=base") // 此处使用的是高德 API（有免费额度），此处使用的是深圳天气，实际应用，可自定义城市编码，或者做个映射关系。附 API 地址：https://lbs.amap.com/api/webservice/guide/api/weatherinfo/#t1
	require.NoError(t, err)
	defer resp.Body.Close()

	var res weatherResult
	require.NoError(t, json.NewDecoder(resp.Body).Decode(&res))
	require.Equal(t, "1", res.Status)

	return fmt.Sprintf("天气： %s，气温： %s，湿度： %s", res.Lives[0].Weather, res.Lives[0].Temperature, res.Lives[0].Humidity)
}

```

<!-- more -->

## 输出

```shell
今天是 2025 年 2 月 21 日，星期五。天气多云，气温 16 度，湿度 83%。以下是今日新闻摘要：

1. 企业动态:
    - 华为将于3月20日至21日举行中国合作伙伴大会2025。
    - 模速空间、无问芯穹和上海仪电推出国内首个“算力生态超市”。
    - OPPO发布双折叠手机OPPO Find N5，手机厚度全球最薄，售价8999元起。
    - 理想智驾硬件升级，全系标配激光雷达。
    - Metaways（宏禧科技）完成A轮近5亿元融资，为全球少数实现12英寸硅基OLED量产企业。
    - 「葫乐科技」获数千万元Pre - B轮融资，为苹果、三星提供体感交互内容。
    - 「恒宇医疗」再获超亿元融资，打造血管介入腔内影像学平台。
    - 龙蟠科技三级控股子公司拟增资扩股并引入投资者。
    - 奥迪CEO表示将在北美扩大生产，重点面向美国市场。
    - 狮头股份拟购买利珀科技控制权，股票24日起停牌。
    - 超达装备筹划公司控制权变更事项，24日起停牌。
    - 思格新能源向港交所提交上市申请。
    - 赣锋锂业拟2亿元收购控股子公司持有的深圳易储股权。
    - 晶澳科技筹划发行H股股票并在香港联合交易所上市。

2. 科技行业:
    - 最低调“六小虎”阶跃星辰开年首秀，Agent落地智能终端，印奇参与。
    - “AI服务商”阿里巴巴值得重估。
    - 国务院国资委部署深化中央企业“AI +”专项行动。

3. 金融市场:
    - 联合健康美股盘前交易中下跌至10%。
    - 热门中概股美股盘前普涨，哔哩哔哩涨超6%。
    - 美股大型科技股盘前多数上涨，Meta涨0.57%。
    - Keep预计2024年公司拥有人应占年内亏损约5.26亿元 - 5.56亿元。
    - 新加坡推出50亿新元证券市场发展计划，旨在提振股市。
    - 深交所本周共对196起证券异常交易行为采取了自律监管措施。
    - 上交所上市委审核通过汉邦科技首发申请。
    - 上交所本周对退市整理股票以及严重异常波动股票进行重点监控。
    - 海康威视2024年归母净利润119.59亿元，同比下降15.23%。

4. 社会民生:
    - 董明珠称已找到格力接班人。
    - 2025年高考8省份将不再分文理科。

5. 国际新闻:
    - 日本拟邀请特斯拉投资日产汽车，特斯拉前董事否认参与。

6. 行业预测:
    - GGII预计2025 - 2028年中国工业机器人领域的减速器需求总量将超过750万台。

7. 政策动向:
    - 国常会提出在电信、教育、文化、医疗、金融等领域研究推出一批新的开放举措。

8. 法律纠纷:
    - 《口袋妖怪：复刻》被宝可梦索赔1.07亿元。

9. 直播预告:
    - OpenTalk将进行“并购大时代？寻找‘最大公约数’”直播。 
```

## 总结

Eino 的编排能力非常强大，通过 Eino 配合 RSS 实现每日简报功能，可以帮助我们快速了解每日的新闻动态，提高工作效率。

至于最终的输出结构，可以自行优化 prompt。此处，仅仅为了展示简单的例子。

在实际应用中，这个逻辑可以简单的封装成一个 Agent，而我们只需要关系带入的 RSS 链接即可。

## 参考

- [Eino](https://github.com/cloudwego/eino)

----

> 本文章除了代码，其他都是 AI 写的。。。🙄🙄🙄