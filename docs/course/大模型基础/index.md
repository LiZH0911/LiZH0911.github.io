# 大模型基础

相关原文：

- [JavaGuide AI 应用开发](https://javaguide.cn/ai/ai-core-concepts.html)

## 一、LLM 运行机制

**1.1 LLM 基本概念**

- **自回归生成（Autoregressive Generation）**：大模型根据已有上下文预测下一个 Token（文本碎片），把新 Token 加进上下文后继续预测，直到生成结束。这个过程叫做自回归生成。
- **Token**：模型每一步“补”的文本碎片。
- **上下文窗口**：一次调用里模型可处理的总 Token 上限。系统提示词、历史消息、当前输入和输出预算都会占用。
- **Temperature / Top-p**：模型选哪个候选碎片的策略。
- **Max Tokens**：允许模型最多“补”多少步。

**1.2 Token**

你可以把 Token 理解为“模型的阅读单位”。我们人类读中文是一个字一个字地看，读英文是一个词一个词地看。

但模型既不按字、也不按词——它用一套自己的“拆字规则”（叫 Tokenizer）把文本切成大小不等的碎片，每个碎片就是一个 Token。

为什么不直接按字或按词切？因为模型需要在“词表大小”和“序列长度”之间取平衡：

- 每个汉字都是一个 Token：词表小、但序列长（模型要“补”更多步）。
- 每个词都是一个 Token：序列短、但词表会爆炸（中文词组太多了）。

所以实际用的是折中方案——子词切分算法（如 BPE、Unigram），高频词保留为整体，低频词拆成更小片段。

- 英文可能一个单词被拆成多个 Token。
- 中文可能一个词被拆成多个 Token，也可能多个字合并成一个 Token（取决于词频与词表）。

工程上通常用经验估算做容量规划，用实际 API 返回的 **usage** 做精确计费与监控。

**Token 化过程示例**：

![Token 化过程.png](images%2FToken%20%E5%8C%96%E8%BF%87%E7%A8%8B.png)

**1.3 上下文窗口**

上下文窗口是 LLM 的“工作记忆”（Working Memory）。它决定了模型在任何时刻可以处理或“记住”的文本量（以 Token 为单位）。

- **对话连续性**：决定模型能进行多长的多轮对话而不遗忘早期细节。
- **单次处理能力**：决定模型一次性能够处理的最大文档、代码库或数据样本。

“模型支持 128K/200K/1M”指的是一次调用里能放进模型的总 Token 上限。大多数模型的上下文窗口包含输入与输出的总和，但部分供应商（如 Google Gemini）对输入和输出分别设限，使用前请查阅具体 API 文档。

上下文窗口往往被隐形成本占用：

![上下文窗口.png](images%2F%E4%B8%8A%E4%B8%8B%E6%96%87%E7%AA%97%E5%8F%A3.png)

* System Prompt：调节模型行为的系统指令（对用户隐藏，但占用窗口）。
* User Prompt：业务数据与指令。
* 多轮对话历史：过往的消息记录。
* RAG 检索片段：从外部知识库检索到的补充信息。
* 工具调用 Schema：函数定义与参数结构。
* 格式开销：特殊字符、换行符、Markdown 标记等。
* 模型生成的输出 Token：**输出也占用上下文窗口**。

因此，你真正能塞进 Prompt 的“有效业务内容”往往远小于标称上限

**1.4 采样参数**

模型每一步会给词表中每个候选 Token 打一个分数（内部叫 logits），分数越高说明模型越觉得这个词应该出现在这里。

原始分数不是概率，需要经过 **softmax** 才能得到概率分布。

最后，模型按这个概率分布“抽签”（采样），决定输出哪个 Token。

**解码参数**（Temperature、Top-p、Top-k 等）就是在这个“打分 &rarr; 概率 &rarr; 抽签”的过程中施加控制：

* Temperature：调整概率分布的“形状”，让高分选项更突出，或者让各选项更均匀。
* Top-p / Top-k：直接砍掉不靠谱的候选项，缩小“抽签池”。
* Penalty 系列：对已经出现过的词降分，防止“复读机”。

![Temperature参数.png](images%2FTemperature%E5%8F%82%E6%95%B0.png)

**1.5 Prompt**

简单来说，Prompt 就是我们输入给大语言模型（LLM）的指令。

从生成机制看，LLM 会基于上下文生成后续 Token；从应用效果看，它能表现出一定的语义理解和指令跟随能力。但这种能力依赖输入上下文，边界不清时就容易偏题或编造。

Prompt 要做的事，就是缩小模型的搜索范围。

指令越模糊，模型越容易乱猜。指令越结构化，输出就越容易被控制。

Prompt 写得好不好，不看长度，看它有没有把任务说清楚。

一个合格的 Prompt，通常要交代四件事：Role、Task、Context、Format。

![prompt-four-element-framework.svg](images%2Fprompt-four-element-framework.svg)

**1.6 Prompt**

先看一个非常常见的 Prompt：

```text
请判断下面用户反馈属于哪类工单，返回 JSON。

用户反馈：我付款成功了，但是订单一直显示待支付。
```

模型可能返回：

```json
{
  "category": "payment",
  "priority": "high",
  "reason": "用户付款成功但订单状态未更新"
}
```

看起来没问题。但这只是“看起来”。

当你把它接进后端系统，真正需要的是一份可以被程序稳定消费的契约。比如：

* `category` 只能是 `PAYMENT`、`LOGISTICS`、`AFTER_SALE`、`ACCOUNT`。
* `priority` 只能是 `LOW`、`MEDIUM`、`HIGH`。
* `confidence` 必须是 `0` 到 `1` 之间的小数。
* `reason` 可以为空吗？最大长度是多少？
* 如果用户输入缺少信息，应该返回 `NEED_MORE_INFO`，还是继续猜？

很多人把 JSON Mode、JSON Schema、Structured Outputs 混着说，面试时也容易答散。但它们其实不在同一层：

![三层约束体系分层图.png](images%2F%E4%B8%89%E5%B1%82%E7%BA%A6%E6%9D%9F%E4%BD%93%E7%B3%BB%E5%88%86%E5%B1%82%E5%9B%BE.png)

**1.7 Function Calling / Tool Calling**

Function Calling 这个名字很容易误导新人。很多人以为“模型调用函数”，好像模型真的执行了你的 Java 方法 —— 不是的。


其实，模型没有直接执行你的后端代码。它做的是：根据用户问题和工具描述，生成一个结构化的工具调用意图。

真正执行工具的是你的业务服务、Agent Runtime、MCP Host 或供应商托管环境。

一个典型工具调用链路如下：

![Function Calling完整调用链路.png](images%2FFunction%20Calling%E5%AE%8C%E6%95%B4%E8%B0%83%E7%94%A8%E9%93%BE%E8%B7%AF.png)

拆成工程步骤就是：

* 服务端注册工具定义：包括工具名、用途描述、参数 `Schema`。
* 用户发起请求：比如“帮我查一下订单 1029384756 到哪了”。
* 模型选择工具：模型判断需要调用 `query_order`，并生成参数 `{"orderId": "1029384756"}`。
* 业务侧校验参数：校验类型、必填、权限、订单归属、幂等键等。
* 业务侧执行工具：调用订单系统、数据库或 HTTP API。
* 工具结果回填模型：把查询结果连同 `tool_use_id` 原样发回模型。Anthropic 要求 `tool_use_id` 严格匹配，Gemini 3 同样为每个 `functionCall` 生成唯一 id，回填时必须带回，否则并行调用场景下结果会错配。
* 模型生成最终回答：模型把结构化结果转成人类能理解的回复。

## 二、大模型 API 调用