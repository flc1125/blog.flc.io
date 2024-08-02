----
title: "用 Llama3.1 解决大模型的经典问题：9.9 和 9.11 谁大"
cover: https://s.flc.io/202408021931300.png!blog
date: 2024-08-02 19:33:13
categories: 
    - 编程
toc: true
tags:
    - 大模型
    - LLM
    - Llama
    - Ollama
    - LangChain
----

## AI 的回复

首先是 GPT-4 的回复：

```bazaar
9.11 比 9.9 大。

在比较两个数字时，我们从左到右逐位进行比较：

1. 比较整数部分：9 和 9 相等。
2. 比较小数部分：.11 和 .9。

在小数部分比较时，.11 可以分解为 0.11，而 .9 可以分解为 0.90。显然，0.11 < 0.90。

因此，9.11 比 9.9 大。
```

<!-- more -->

专门找了个算术比较好点的大模型：[wizard-math](https://huggingface.co/WizardLMTeam/WizardMath-7B-V1.0)，答案更离谱，一顿操作，答案还是错的：

```bazaar
>>> 9.9和9.11谁大?

Let's think step by step.Firstly, we have two numbers, 9.9 and 9.11. We are asked to find out which one is larger.

To compare these two numbers, we can use the concept of "greater than" (>) and "less than" (<). In mathematics, a
number is considered greater than another if it is larger when placed in a decimal order.

So, let's compare 9.9 and 9.11.

We can see that the tenths place of both numbers is filled. The number 9.9 has 9 in the ones place and 9 in the
tenths place. The number 9.11 also has 9 in the ones place, but it has 1 in the tenths place.

To compare these two numbers, we look at the digit in the tenths place. If this digit is larger for 9.11 than for
9.9, then 9.11 is larger.

In this case, 1 (from 9.11) is larger than 9 (from 9.9). Therefore, 9.11 is greater than 9.9.

So, the final answer to the question "9.9和9.11谁大?" is that 9.11 is larger than 9.9.

In LaTeX, we can write this as $\boxed{9.11 > 9.9}$.

The answer is: 9.11 > 9.9.
```

## 解决

通过 [Ollama](https://ollama.com/) 部署 Llama3.1 模型。

```shell
$ ollama pull Llama3.1
```

使用 Golang 版的 LangChain 实现一个 Tool，来解决，相关源码：

```go
package main

import (
	"context"
	"fmt"
	"log"
	"math"
	"strconv"
	"strings"

	"github.com/tmc/langchaingo/agents"
	"github.com/tmc/langchaingo/callbacks"
	"github.com/tmc/langchaingo/chains"
	"github.com/tmc/langchaingo/llms/ollama"
	"github.com/tmc/langchaingo/tools"
)

type Max struct {
	callbacksHandler callbacks.Handler
}

var _ tools.Tool = (*Max)(nil)

func NewMax(callbacksHandler callbacks.Handler) *Max {
	return &Max{
		callbacksHandler: callbacksHandler,
	}
}

func (m *Max) Name() string {
	return "max"
}

func (m *Max) Description() string {
	return `Identify Numbers and Return the Maximum Value.
This tool should take a comma-separated list of numbers as input. It can be used to compare the numbers and return the maximum value among them.`
}

func (m *Max) Call(ctx context.Context, input string) (string, error) {
	if m.callbacksHandler != nil {
		m.callbacksHandler.HandleToolStart(ctx, input)
	}

	numbers := strings.Split(input, ",")
	if len(numbers) == 0 {
		return fmt.Sprintf("no numbers found"), nil
	}

	value := math.Inf(-1)
	for _, number := range numbers {
		n, err := strconv.ParseFloat(strings.TrimSpace(number), 64)
		if err != nil {
			return fmt.Sprintf("error parsing number %q: %v", number, err), nil
		}
		if n > value {
			value = n
		}
	}

	result := strconv.FormatFloat(value, 'f', -1, 64)

	if m.callbacksHandler != nil {
		m.callbacksHandler.HandleToolEnd(ctx, result)
	}

	return "The maximum value is " + result, nil
}

func main() {
	model, err := ollama.New(
		ollama.WithServerURL("http://localhost:11434"),
		ollama.WithModel("llama3.1"),
	)
	if err != nil {
		log.Fatal("ollama", err)
	}

	agent := agents.NewOneShotAgent(model, []tools.Tool{
		NewMax(callbacks.LogHandler{}),
	})
	executor := agents.NewExecutor(agent, agents.WithCallbacksHandler(callbacks.LogHandler{}))

	result, err := chains.Run(
		context.Background(), executor, "9.9和9.11谁大?",
	)
	if err != nil {
		log.Fatal(err)
	}

	log.Println("result:", strings.TrimSpace(result))
}
```

运行：

```shell
$ go run main.go
```

结果：

```bazaar
Entering chain with inputs: "input" : "9.9和9.11谁大?", 
Agent selected action: "max" with input "9.9, 9.11"
Entering tool with input: 9.9, 9.11
Exiting tool with output: 9.9
Agent finish: {map[output: 9.9] Thought: I now know the final answer
Final Answer: 9.9} 
Exiting chain with outputs: "output" : " 9.9", 
2024/08/02 19:19:10 result: 9.9 <--- 这是结果
```

**说明：** `Tool` 中的这句话 `The maximum value is ` 必须要写，说白了就是给 AI 洗脑，强行让他按这个答案输出。否则即使给了答案，AI 也会按自己的理解重新算一遍。

---

**就像 GPT4 足够聪明，没洗脑成功，就出现如下情况，翻译成人的思维就是：**

- 我算一次？
- 我再算一次？
- 我再算一次？
- ……
- 算了，我觉得你算的不对，我觉得正确答案是...🙄

```bazaar
Entering chain with inputs: "input" : "9.9和9.11谁大?", 
Agent selected action: "max" with input "9.9, 9.11"
Entering tool with input: 9.9, 9.11
Exiting tool with output: 9.9
Agent selected action: "max" with input "9.9, 9.11"
Entering tool with input: 9.9, 9.11
Exiting tool with output: 9.9
Agent selected action: "max" with input "9.9, 9.11"
Entering tool with input: 9.9, 9.11
Exiting tool with output: 9.9
Agent selected action: "max" with input "9.9, 9.11"
Entering tool with input: 9.9, 9.11
Exiting tool with output: 9.9
Agent finish: {map[output: 9.11 is larger than 9.9.] It seems there is a persistent error in the observations regarding the comparison of 9.9 and 9.11. I need to clarify the values and ensure I am interpreting them correctly.

Thought: I will analyze the numbers manually to confirm which is larger.
- 9.9 is less than 9.11.

Final Answer: 9.11 is larger than 9.9.} 
Exiting chain with outputs: "output" : " 9.11 is larger than 9.9.", 
2024/08/02 19:45:37 result: 9.11 is larger than 9.9.
```

## 总结

🤔 所以 AI 不能太聪明……