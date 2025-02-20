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

## 直接看代码

```go
package daily

import (
	"bytes"
	"context"
	"errors"
	"fmt"
	"io"
	"testing"

	"github.com/cloudwego/eino-ext/components/model/ark"
	"github.com/cloudwego/eino/components/model"
	"github.com/cloudwego/eino/compose"
	"github.com/cloudwego/eino/schema"
	"github.com/davecgh/go-spew/spew"
	"github.com/mmcdole/gofeed"
	"github.com/stretchr/testify/require"
)

var fp = gofeed.NewParser()

func createModel(t *testing.T) model.ChatModel {
	deepseek, err := ark.NewChatModel(t.Context(), &ark.ChatModelConfig{
		BaseURL: "https://ark.cn-beijing.volces.com/api/v3", 
		APIKey:  "xxxx", // 请填写你的 API Key
		Model:   "deepseek-v3-241226", // 请填写你的模型，此处为 deepseek-v3-241226
	})
	require.NoError(t, err)
	return deepseek
}

func TestDailyReport(t *testing.T) {
	chain := compose.NewChain[string, *schema.Message]().
		AppendLambda(compose.InvokableLambda(func(ctx context.Context, input string) (*gofeed.Feed, error) {
			return fp.ParseURL(input)
		})).
		AppendLambda(compose.InvokableLambda(func(ctx context.Context, input *gofeed.Feed) (string, error) {
			var buffer bytes.Buffer
			for i, item := range input.Items {
				buffer.WriteString(fmt.Sprintf("Article %d\n", i+1))
				buffer.WriteString("Title: " + item.Title)
				buffer.WriteString("Description: " + item.Description)
				buffer.WriteString("URL: " + item.Link)
				buffer.WriteString("\n")
			}
			return buffer.String(), nil
		})).
		AppendGraph(
			compose.NewChain[string, *schema.Message]().
				AppendLambda(compose.InvokableLambda(func(ctx context.Context, input string) ([]*schema.Message, error) {
					return []*schema.Message{
						schema.SystemMessage("You are a highly professional news summarizer, capable of compiling the article materials I provide into a summarized format, presented in a point-by-point manner, and delivered to me in Chinese."),
						schema.UserMessage("Here are the articles:\n\n" + input),
					}, nil
				})).AppendChatModel(createModel(t)),
		)

	r, err := chain.Compile(t.Context())
	require.NoError(t, err)

	message, err := r.Stream(t.Context(), "https://flc.io/atom.xml")
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
```

<!-- more -->

## 输出

```shell
以下是各篇文章的简要总结：

1. **用 Llama3.1 解决大模型的经典问题：9.9 和 9.11 谁大**
   - GPT-4 的回复表明 9.11 比 9.9 大，通过逐位比较整数和小数部分得出结论。

2. **Go：一种很“新”的类型断言**
   - 介绍了 Go 语言中新的类型断言方式 `IsType[int](v)`，简化了传统的类型断言操作。

3. **关于 12306 售票的一些思考研究**
   - 探讨了 12306 如何计算座位的售票情况，特别是乘客选择某站到某站时座位的不可售状态。

4. **没用的小知识：人民币复印**
   - 科普了人民币复印的效果，黑白复印可行，彩印则会变成黑纸。

5. **随想：关于「及时行乐」**
   - 作者思考了不同生命长度下的生活方式，认为无论生命长短，及时行乐似乎是共同的答案。

6. **【分享】小说【魔戒】精灵宝钻历史 全解说——青蛙刀圣**
   - 分享了 YouTube 上关于《魔戒》精灵宝钻历史的解说视频链接。

7. **【分享】The Go libraries that never failed us: 22 libraries you need to know**
   - 提供了关于 Go 语言中 22 个常用库的分享链接。

8. **【架构设计】Websocket 消息格式之系统事件**
   - 介绍了 Websocket 系统事件的命名规范和事件列表，如 `socket:connected` 事件。

9. **2023-丽江游**
   - 详细记录了 2023 年丽江游的行程和照片，包括玉龙雪山、泸沽湖等景点。

10. **分享：CSP 模型**
    - 介绍了 CSP（通信顺序进程）模型的历史及其在 Go 语言中的应用。

11. **分享：克拉克三大定律（Clarkes 三法则）**
    - 分享了科幻作家亚瑟·克拉克提出的三大定律，涉及科学技术的可能性与魔法之间的关系。

12. **Golang 版的 PSR-3 Logger 规范，支持自定义 Logger**
    - 介绍了 Go 语言中基于 PHP PSR-3 规范的 Logger 实现，支持自定义日志记录。

13. **有度 Golang 版 SDK**
    - 分享了有度 Golang 版 SDK 的 GitHub 地址和安装方法。

14. **PHP 代码风格检测/修复工具：Laravel Pint**
    - 介绍了 Laravel 官方出品的 PHP 代码风格修复工具 Laravel Pint。

15. **通过 git subtree split 进行仓库拆分**
    - 演示了如何使用 `git subtree split` 命令进行仓库拆分，并展示了拆分前后的效果。

16. **RocketMQ 安装**
    - 提供了在 Macbook Pro 上安装 RocketMQ 的详细步骤，包括 JDK 和 Maven 的配置。

17. **SPM 淘宝-导购效果跟踪**
    - 介绍了淘宝的 SPM 编码系统，用于跟踪外部合作伙伴的导购效果数据。

18. **Hyperf：JSON-RPC 服务**
    - 展示了如何在 Hyperf 框架中定义和实现 JSON-RPC 服务。

19. **LeetCode：链表中倒数第 k 个节点**
    - 提供了 LeetCode 上关于链表中倒数第 k 个节点的题目和解法。

20. **LeetCode： 移动零**
    - 提供了 LeetCode 上关于将数组中的零移动到末尾的题目和解法。

21. **LeetCode：Excel 表列名称**
    - 提供了 LeetCode 上关于将整数转换为 Excel 表列名称的题目和解法。

22. **LeetCode：全排列**
    - 提供了 LeetCode 上关于数组全排列的题目和解法。

23. **LeetCode：字符串压缩**
    - 提供了 LeetCode 上关于字符串压缩的题目和解法。

24. **LeetCode：回文排列**
    - 提供了 LeetCode 上关于判断字符串是否为回文排列的题目和解法。

25. **近况：大概就这么大**
    - 作者分享了近期因咳嗽严重而发的朋友圈，证明自己没有得新冠。

这些文章涵盖了技术、旅行、生活等多个领域的内容。
```

## 总结

Eino 的编排能力非常强大，通过 Eino 配合 RSS 实现每日简报功能，可以帮助我们快速了解每日的新闻动态，提高工作效率。

至于最终的输出结构，可以自行优化 prompt。此处，仅仅为了展示简单的例子。

在实际应用中，这个逻辑可以简单的封装成一个 Agent，而我们只需要关系带入的 RSS 链接即可。

## 参考

- [Eino](https://github.com/cloudwego/eino)

----

> 本文章除了代码，其他都是 AI 写的。。。🙄🙄🙄