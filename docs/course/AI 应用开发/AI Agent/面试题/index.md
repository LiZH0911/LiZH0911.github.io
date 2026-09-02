# AI Agent 面试题

相关原文：

- [JavaGuide AI 应用开发](https://javaguide.cn/ai/ai-core-concepts.html)



## 二、AI Agent

**2.1 Agent 基础**

这部分通常从 Agent 的定义开始，随后追问运行循环和编排方式。准备时要能分清 Chatbot、Workflow 与 Agent 在任务路径、状态和工具使用上的差别。

常见面试题：

* AI Agent 和 LLM 有什么区别？

LLM 本质上是一个文本生成模型，它只能根据输入生成输出文本。而 AI Agent 是以 LLM 为核心构建的自主决策系统

* Agent = LLM + Planning + Memory + Tools 这条公式怎么理解？

Agent 在 LLM 之上增加了三个关键能力：第一是 Planning，能自主分解和规划任务；第二是 Memory，有独立的记忆系统来持久化信息；第三是 Tool Use，能调用外部工具来执行真实操作。简单来说，LLM 是大脑，Agent 是一个有大脑、有记忆、有手脚的完整系统。"

* Agent Loop 的完整流程是什么？

思考、行动、观察的循环

* Agent 和传统编程、Workflow 的核心区别是什么？

* ReAct、Plan-and-Execute、Reflection、Multi-Agent 分别适合什么场景？


* Tools 注册时，工具 description 为什么很关键？
* 什么时候用纯 Agent，什么时候用 Workflow 或 Agentic Workflow？
* Multi-Agent 协作的主要问题是什么？为什么生产里不能盲目上多 Agent？

**2.2 Agent Memory**

Memory 题会追到信息从哪里来、保存多久、什么时候读取，以及出现过期或冲突后怎么处理。聊天记录、当前任务状态和跨会话记忆需要分别讨论。

常见面试题：

Agent 的短期记忆和长期记忆有什么区别？
Agent 记忆系统要解决哪些核心问题？
向量记忆和 Markdown 记忆分别适合什么场景？
Auto Memory 是什么？它为什么不能无限自动写入？
哪些团队共享记忆适合走 Git 和 Code Review，哪些更适合数据库？
记忆压缩、记忆过期、记忆冲突应该怎么处理？
如何避免长期记忆污染上下文？
面试里怎么讲“有记忆”不是简单保存聊天记录？