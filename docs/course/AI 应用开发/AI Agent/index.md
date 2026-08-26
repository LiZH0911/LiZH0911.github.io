# AI Agent

相关原文：

- [JavaGuide AI 应用开发](https://javaguide.cn/ai/ai-core-concepts.html)

## 一、Agent

如果你看过 LangChain 的 Agent 源码，会发现它的核心并不神秘，很多时候就是一个 while 循环。

AI Agent 可以理解为一个能感知环境、做决策、执行动作的软件系统。LLM 负责理解和决策，工具负责执行，记忆负责保存上下文和历史经验。

它和普通聊天机器人的差别在于：Agent 不只是回复消息，它会在动态环境里持续观察、判断、执行，直到任务结束。

一般可以用这个公式概括：Agent = LLM + Planning + Memory + Tools

![AI Agent核心架构.png](images%2FAI%20Agent%E6%A0%B8%E5%BF%83%E6%9E%B6%E6%9E%84.png)