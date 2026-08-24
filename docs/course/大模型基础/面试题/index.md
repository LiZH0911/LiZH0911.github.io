# 面试题

相关原文：

- [JavaGuide AI 应用开发](https://javaguide.cn/ai/ai-core-concepts.html)

## 一、大模型基础

**1.1 LLM 运行机制**

* Token 是什么？为什么中文、英文、代码消耗的 Token 不一样？
* 上下文窗口是什么？上下文窗口越大，效果一定越好吗？
* 什么是 Lost in the Middle 问题？长上下文场景下怎么缓解？
* Temperature、Top-P、Top-K 分别控制什么？生产环境怎么设置更稳？
* 为什么 Temperature 设置为 0，模型输出仍然可能不完全一致？
* 大模型为什么会产生幻觉？常见缓解方案有哪些？
* Token 预算怎么估算？输入、输出、历史消息、RAG 证据如何取舍？
* 长上下文窗口会不会取代 RAG？二者分别适合什么场景？

**1.2 API 调用工程**

模型 API 的响应时间、计费方式和错误类型都与普通业务接口有所不同。面试中通常会把 Streaming、重试、幂等、限流和模型网关放在一条调用链里考察。

常见面试题：

* 大模型 API 调用的完整链路是什么？
* Streaming 为什么能改善用户体验？它能减少总耗时和 Token 成本吗？
* SSE、WebSocket、HTTP Chunked 在流式输出场景下怎么选？
* 哪些大模型 API 错误可以重试？哪些错误不能重试？
* 为什么大模型调用必须做幂等？
* 大模型限流为什么不能只按 QPS 做？
* 模型网关通常要承担哪些能力？
* AI 应用的调用日志里至少要记录哪些字段？

**1.3 结构化输出与工具调用**

模型输出只要进入业务系统，就要处理格式校验、字段约束和执行权限。这里容易混淆 JSON Mode、Structured Outputs、Function Calling、MCP Tool 与普通 HTTP API。

常见面试题：

* 为什么只写“请返回 JSON”不可靠？
* JSON Mode 和 Structured Outputs 有什么区别？
* JSON Schema 在大模型应用里解决什么问题？
* Function Calling 的完整链路是什么？
* Function Calling 和 MCP 有什么区别？
* MCP Tool 和普通 HTTP API 有什么关系？
* Agent Skill 和 Function Calling 是一回事吗？
* 结构化输出失败后怎么处理？
* 工具调用为什么必须做安全治理？
* 面试里怎么一句话概括结构化输出？

**1.4 AI 应用评测**

评测题关注如何证明一次模型、Prompt 或系统改动确实带来了改善。Golden Set、LLM-as-Judge、Trace 回放和线上灰度分别覆盖不同阶段，不能只看公开榜单或少量演示样例。

常见面试题：

* 为什么不能只靠公开 benchmark 评估 AI 应用质量？
* Golden Set 应该怎么构建？冷启动阶段没有生产日志怎么办？
* LLM-as-Judge 有哪些主要偏差？怎么缓解？
* RAG 评测为什么必须分检索和生成两段？
* Agent 评测为什么比普通问答和 RAG 更复杂？
* 离线评测、Trace 回放、线上灰度分别解决什么问题？
* CI 里的 AI 评测如何平衡速度和覆盖度？
* 如果 LLM-as-Judge 和人工评测结果不一致，应该怎么处理？

**1.5 综合场景题**

* 客服机器人历史会话持续增长时，如何分配 Token 预算并保留关键业务状态？
* 流式响应中途断开后，服务端如何处理重试、续传和重复计费问题？
* 上游模型触发 RPM 或 TPM 限制时，模型网关如何排队、降级或切换模型？
* 模型生成退款工具的调用参数后，业务系统还需要执行哪些校验？
* 更换模型或修改 Prompt 后，如何用离线评测、Trace 回放和线上灰度验证效果？

