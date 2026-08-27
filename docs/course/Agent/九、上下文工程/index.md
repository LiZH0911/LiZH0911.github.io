# **九、上下文工程**

在前面的章节中，我们已经为智能体引入了记忆系统与 RAG。然而，要让智能体在真实复杂场景中稳定地“思考”与“行动”，仅有记忆与检索还不够。

我们需要一套工程化方法，持续、系统地为模型构造恰当的“上下文”。这就是本章的主题：上下文工程（Context Engineering）。

上下文工程关注的是“在每一次模型调用前，如何以可复用、可度量、可演进的方式，拼装并优化输入上下文”，从而提升正确性、鲁棒性与效率。

安装的本章对应版本的 hello_agents 包：

```bash
pip install "hello-agents[all]==0.2.8"
```

本章主要介绍上下文工程的核心概念与实践，并在HelloAgents框架中新增了上下文构建器和两个配套工具：

* **ContextBuilder** (`hello_agents/context/builder.py`)：上下文构建器，实现 GSSC (Gather-Select-Structure-Compress) 流水线，提供统一的上下文管理接口
* **NoteTool** (`hello_agents/tools/builtin/note_tool.py`)：结构化笔记工具，支持智能体进行持久化记忆管理
* **TerminalTool** (`hello_agents/tools/builtin/terminal_tool.py`)：终端工具，支持智能体进行文件系统操作和即时上下文检索

这些组件共同构成了完整的上下文工程解决方案，是实现长时程任务管理和智能体式搜索的关键，将在后续章节中详细介绍。

除了安装框架外，还需要在`.env`配置 LLM 的 API。本章示例主要使用大语言模型进行上下文管理和智能决策。

### 9.1 **什么是上下文工程**

在经历了数年提示工程（Prompt Engineering）成为应用型 AI 的焦点之后，一个新的术语开始走到台前：上下文工程（Context Engineering）。

如今，用语言模型构建系统不再只是找对提示词里的句式和措辞，而是要回答一个更宏观的问题：**什么样的上下文配置，最有可能让模型产出我们期望的行为？**

所谓“上下文”，是指在对大语言模型（LLM）进行采样时所包含的那组 tokens。手头的工程问题，是在 LLM 的固有约束之下，优化这些 tokens 的效用，以便稳定地得到预期结果。

想要有效驾驭 LLM，往往需要“在上下文中思考”——也就是说：在任何一次调用时，都要审视 LLM 可见的整体状态，并预判这种状态可能诱发的行为。

![图 9.1 Prompt engineering vs Context engineering.png](..%2Fimages%2F%E5%9B%BE%209.1%20Prompt%20engineering%20vs%20Context%20engineering.png)

本节将探讨正在兴起的上下文工程，并给出一个用于构建可调控、有效智能体的精炼心智模型。

**上下文工程 vs. 提示工程**

在现在前沿模型厂商的视角中，上下文工程是提示工程的自然演进.

* 提示工程：关注如何编写与组织 LLM 的指令以获得更优结果（例如系统提示的写法与结构化策略）；
* 上下文工程：**在推理阶段，如何策划与维护“最优的信息集合（tokens）”**，其中不仅包含提示本身，还包含其他会进入上下文窗口的一切信息。

在 LLM 工程的早期阶段，提示往往是主要工作，因为大多数用例（除日常聊天外）都需要针对单轮分类或文本生成做精调式的提示优化。

顾名思义，提示工程的核心是“如何写出有效提示”，尤其是系统提示。

然而，随着我们开始工程化地构建更强的智能体，它们在更长的时间范围内、跨多次推理轮次地工作，我们就需要能管理整个上下文状态的策略——其中包括系统指令、工具、MCP（Model Context Protocol）、外部数据、消息历史等。

### 9.2 **为什么上下文工程重要**

尽管模型的速度越来越快、可处理的数据规模越来越大，但我们观察到：LLM 和人类一样，在一定点上会“走神”或“混乱”。

针堆找针（needle-in-a-haystack）类基准揭示了一个现象：**上下文腐蚀（context rot）**——随着上下文窗口中的 tokens 增加，模型从上下文中准确回忆信息的能力反而下降。

上下文必须被视作一种有限资源，且具有**边际收益递减**。

因此我们更需要谨慎地筛选哪些 tokens 应该被提供给 LLM。

这种稀缺并非偶然，而是源自 LLM 的**架构约束**。

Transformer 让每个 token 能够与上下文中的所有 token 建立关联，理论上形成 (n^2) 级别的两两注意力关系。随着上下文长度增长，模型对这些两两关系的建模能力会被“拉薄”，从而自然地产生“上下文规模”与“注意力集中度”的张力。此外，模型的注意力模式来源于训练数据分布——短序列通常比长序列更常见，因此模型对“全上下文依赖”的经验更少、专门参数也更少。

诸如位置编码插值（position encoding interpolation）等技术可以让模型在推理时“适配”比训练期更长的序列，但会牺牲部分对 token 位置的精确理解。总体上，这些因素共同形成的是一个性能梯度，而非“悬崖式”崩溃：模型在长上下文下依旧强大，但相较短上下文，在信息检索与长程推理上的精度会有所下降。

基于上述现实，**有意识的上下文工程**就成为构建强健智能体的必需品。

**9.2.1 有效上下文的“解剖学”**

优秀的上下文工程目标是：**用尽可能少、但高信号密度的 tokens，最大化获得期望结果的概率**。落实到实践中，我们建议围绕以下组件开展工程化建设：

* 系统提示（System Prompt）：语言清晰、直白，信息层级把握在“刚刚好”的高度。常见两极误区：
    * 过度硬编码：在提示中写入复杂、脆弱的 if-else 逻辑，长期维护成本高、易碎。
    * 过于空泛：只给出宏观目标与泛化指引，缺少对期望输出的**具体信号**或假定了错误的“共享上下文”。建议将提示分区组织（如 <background_information>、、工具指引、输出描述等），用 XML/Markdown 分隔。无论格式如何，追求的是**能完整勾勒期望行为的“最小必要信息集”**（“最小”并不等于“最短”）。先用最好的模型在最小提示上试跑，再依据失败模式增补清晰的指令与示例。
* 工具（Tools）：工具定义了智能体与信息/行动空间的契约，必须促进效率：既要返回 **token 友好**的信息，又要鼓励高效的智能体行为。工具应当：
    * 职责单一、相互低重叠，接口语义清晰；
    * 对错误鲁棒；
    * 入参描述明确、无歧义，充分发挥模型擅长的表达与推理能力。 常见失败模式是“臃肿工具集”：功能边界模糊，导致“选哪个工具”这一决策本身就含混不清。**如果人类工程师都说不准用哪个工具，别指望智能体做得更好**。精心甄别一个“最小可行工具集（MVTS）”往往能显著提升长期交互中的稳定性与可维护性。
* 示例（Few-shot）：始终推荐提供示例，但不建议把“所有边界条件”的罗列一股脑塞进提示。请精挑细选一组多样且典型的示例，直接画像“期望行为”。对 LLM 而言，**好的示例胜过千言万语**。

总的指导思想是：信息充分但紧致。

**9.2.2 上下文检索与智能体式搜索**

一个简洁的定义：**智能体 = 在循环中自主调用工具的 LLM**。随着底层模型能力增强，智能体的自治水平便可提升：更能独立探索复杂问题空间，并从错误中恢复。

除了存储效率，**引用的元数据**本身也能帮助精化行为：目录层级、命名约定、时间戳等都在隐含地传达“目的与时效”。例如，tests/test_utils.py 与 src/core/test_utils.py 的语义暗示就不同。

允许智能体自主导航与检索还能实现**渐进式披露（progressive disclosure）**：每一步交互都会产生新的上下文，反过来指导下一步决策——文件大小暗示复杂度、命名暗示用途、时间戳暗示相关性。智能体得以按层构建理解，只在工作记忆中保留“当前必要子集”，并用“记笔记”的方式做补充持久化，从而维持聚焦而非“被大而全拖垮”。

需要权衡的是：运行时探索往往比预计算检索更慢，并且需要有“主见”的工程设计来确保模型拥有正确的工具与启发式。如果缺少引导，智能体可能会误用工具、追逐死胡同或错过关键信息，造成上下文浪费。

在不少场景中，混合策略更有效：前置加载少量“高价值”上下文以保证速度，然后允许智能体按需继续自主探索。

边界的选择取决于任务动态性与时效要求。

在工程上，可以预先放入类似“项目约定说明（如 README/指南）”的文件，同时提供 **glob**、**grep** 等原语，让智能体即时检索具体文件，从而绕开过时索引与复杂语法树的沉没成本。

**9.2.3 面向长时程任务的上下文工程**

长时程任务要求智能体在超出上下文窗口的长序列行动中，仍能保持连贯性、上下文一致与目标导向。例如大型代码库迁移、跨数小时的系统性研究。指望无限增大上下文窗口并不能根治“上下文污染”与相关性退化的问题，因此需要直接面向这些约束的工程手段：

* 压缩整合（Compaction）
    * 定义：当对话接近上下文上限时，对其进行高保真总结，并用该摘要重启一个新的上下文窗口，以维持长程连贯性。
    * 实践：让模型压缩并保留架构性决策、未解决缺陷、实现细节，丢弃重复的工具输出与噪声；新窗口携带压缩摘要 + 最近少量高相关工件（如“最近访问的若干文件”）。
    * 调参建议：先优化**召回**（确保不遗漏关键信息），再优化**精确度**（剔除冗余内容）；一种安全的“轻触式”压缩是对“深历史中的工具调用与结果”进行清理。
* 结构化笔记（Structured note-taking）
    * 定义：也称“智能体记忆”。智能体以固定频率将关键信息写入上下文外的**持久化存储**，在后续阶段按需拉回。
    * 价值：以极低的上下文开销维持持久状态与依赖关系。例如维护 TODO 列表、项目 NOTES.md、关键结论/依赖/阻塞项的索引，跨数十次工具调用与多轮上下文重置仍能保持进度与一致性。
    * 说明：在非编码场景中同样有效（如长期策略性任务、游戏/仿真中的目标管理与统计计数）。结合第八章的 MemoryTool，可轻松实现文件式/向量式的外部记忆并在运行时检索。
* 子代理架构（Sub-agent architectures）
    * 思想：由主代理负责高层规划与综合，多个专长子代理在“干净的上下文窗口”中各自深挖、调用工具并探索，最后仅回传**凝练摘要**（常见 1,000–2,000 tokens）。
    * 好处：实现关注点分离。庞杂的搜索上下文留在子代理内部，主代理专注于整合与推理；适合需要并行探索的复杂研究/分析任务。
    * 经验：公开的多智能体研究系统显示，该模式在复杂研究任务上相较单代理基线具有显著优势。

* 方法取舍可以遵循以下经验法则：
    * 压缩整合：适合需要长对话连续性的任务，强调上下文的“接力”。
    * 结构化笔记：适合有里程碑/阶段性成果的迭代式开发与研究。
    * 子代理架构：适合复杂研究与分析，能从并行探索中获益。

即便模型能力持续提升，“在长交互中维持连贯性与聚焦”仍是构建强健智能体的核心挑战。谨慎而系统的上下文工程将长期保持其关键价值。


### 9.3 **在 Hello-Agents 中的实践：ContextBuilder**

本节将详细介绍 HelloAgents 框架中的上下文工程实践。

将从设计动机、核心数据结构、实现细节到完整案例，逐步展示如何构建一个生产级的上下文管理系统。

ContextBuilder 的设计理念是"简单高效"，去除不必要的复杂性，统一以"相关性+新近性"的分数进行选择，符合 Agent 模块化与可维护性的工程取向。

**9.3.1 设计动机与目标**

在构建 ContextBuilder 之前，我们首先需要明确其设计目标和核心价值。一个优秀的上下文管理系统应该解决以下几个关键问题：

1. **统一入口**：将"获取(Gather)- 选择(Select)- 结构化(Structure)- 压缩(Compress)"抽象为可复用流水线，减少在 Agent 实现中的重复模板代码。这种统一的接口设计让开发者无需在每个 Agent 中重复编写上下文管理逻辑。
2. **稳定形态**：输出固定骨架的上下文模板，便于调试、A/B 测试与评估。我们采用了分区组织的模板结构：
    * `[Role & Policies]`：明确 Agent 的角色定位和行为准则
    * `[Task]`：当前需要完成的具体任务
    * `[State]`：Agent 的当前状态和上下文信息
    * `[Evidence]`：从外部知识库检索的证据信息
    * `[Context]`：历史对话和相关记忆
    * `[Output]`：期望的输出格式和要求
3. 预算守护：在 token 预算内尽量保留高价值信息，对超限上下文提供兜底压缩策略。这确保了即使在信息量巨大的场景下，系统也能稳定运行。
4. 最小规则：不引入来源/优先级等分类维度，避免复杂度增长。实践表明，基于相关性和新近性的简单评分机制，在大多数场景下已经足够有效。

**9.3.2 核心数据结构**

ContextBuilder 的实现依赖两个核心数据结构，它们定义了系统的配置和信息单元。

（1）ContextPacket：候选信息包

<details>
<summary>hello_agents/context/builder.py 中的 ContextPacket 数据类</summary>

```python
from typing import Dict, Any, List, Optional, Tuple
from dataclasses import dataclass, field
from datetime import datetime

@dataclass
class ContextPacket:
    """上下文信息包"""
    content: str
    timestamp: datetime = field(default_factory=datetime.now)
    metadata: Dict[str, Any] = field(default_factory=dict)
    token_count: int = 0
    relevance_score: float = 0.0  # 0.0-1.0
    
    def __post_init__(self):
        """自动计算token数"""
        if self.token_count == 0:
            self.token_count = count_tokens(self.content)
```

</details>

`ContextPacket` 是系统中信息的基本单元。每个候选信息都会被封装为一个 ContextPacket，包含内容、时间戳、token 数量和相关性分数等核心属性。这种统一的数据结构简化了后续的选择和排序逻辑。

（2）ContextConfig：配置管理

<details>
<summary>hello_agents/context/builder.py 中的 ContextConfig 数据类</summary>

```python
@dataclass
class ContextConfig:
    """上下文构建配置"""
    max_tokens: int = 8000  # 总预算
    reserve_ratio: float = 0.15  # 生成余量（10-20%）
    min_relevance: float = 0.3  # 最小相关性阈值
    enable_mmr: bool = True  # 启用最大边际相关性（多样性）
    mmr_lambda: float = 0.7  # MMR平衡参数（0=纯多样性, 1=纯相关性）
    system_prompt_template: str = ""  # 系统提示模板
    enable_compression: bool = True  # 启用压缩
    
    def get_available_tokens(self) -> int:
        """获取可用token预算（扣除余量）"""
        return int(self.max_tokens * (1 - self.reserve_ratio))
```

</details>

`ContextConfig` 封装了所有可配置的参数，使得系统行为可以灵活调整。特别值得注意的是 `reserve_ratio` 参数，它确保系统指令等关键信息始终有足够的空间，不会被其他信息挤占。

**9.3.3 GSSC 流水线详解**

ContextBuilder 的核心是 GSSC(Gather-Select-Structure-Compress)流水线，它将上下文构建过程分解为四个清晰的阶段。让我们深入了解每个阶段的实现细节。

（1）Gather：多源信息汇集

第一阶段是从多个来源汇集候选信息。这个阶段的关键在于容错性和灵活性。

<details>
<summary>hello_agents/context/builder.py 中 ContextBuilder 类的私有函数 _gather</summary>

```python
    def _gather(
        self,
        user_query: str,
        conversation_history: List[Message],
        system_instructions: Optional[str],
        additional_packets: List[ContextPacket]
    ) -> List[ContextPacket]:
        """Gather: 收集候选信息"""
        packets = []
        
        # P0: 系统指令（强约束）
        if system_instructions:
            packets.append(ContextPacket(
                content=system_instructions,
                metadata={"type": "instructions"}
            ))
        
        # P1: 从记忆中获取任务状态与关键结论
        if self.memory_tool:
            try:
                # 搜索任务状态相关记忆
                state_results = self.memory_tool.execute(
                    "search",
                    query="(任务状态 OR 子目标 OR 结论 OR 阻塞)",
                    min_importance=0.7,
                    limit=5
                )
                if state_results and "未找到" not in state_results:
                    packets.append(ContextPacket(
                        content=state_results,
                        metadata={"type": "task_state", "importance": "high"}
                    ))
                
                # 搜索与当前查询相关的记忆
                related_results = self.memory_tool.execute(
                    "search",
                    query=user_query,
                    limit=5
                )
                if related_results and "未找到" not in related_results:
                    packets.append(ContextPacket(
                        content=related_results,
                        metadata={"type": "related_memory"}
                    ))
            except Exception as e:
                print(f"⚠️ 记忆检索失败: {e}")
        
        # P2: 从RAG中获取事实证据
        if self.rag_tool:
            try:
                rag_results = self.rag_tool.run({
                    "action": "search",
                    "query": user_query,
                    "top_k": 5
                })
                if rag_results and "未找到" not in rag_results and "错误" not in rag_results:
                    packets.append(ContextPacket(
                        content=rag_results,
                        metadata={"type": "knowledge_base"}
                    ))
            except Exception as e:
                print(f"⚠️ RAG检索失败: {e}")
        
        # P3: 对话历史（辅助材料）
        if conversation_history:
            # 只保留最近N条
            recent_history = conversation_history[-10:]
            history_text = "\n".join([
                f"[{msg.role}] {msg.content}"
                for msg in recent_history
            ])
            packets.append(ContextPacket(
                content=history_text,
                metadata={"type": "history", "count": len(recent_history)}
            ))
        
        # 添加额外包
        packets.extend(additional_packets)
        
        return packets
```

</details>

这个实现展示了几个重要的设计考虑：

* **容错机制**：每个外部数据源的调用都被 try-except 包裹，确保单个源的失败不会影响整体流程
* **优先级处理**：系统指令被标记为高优先级，确保始终被保留
* **历史限制**：对话历史只保留最近的几条，避免上下文窗口被历史信息占据

（2）Select：智能信息选择

第二阶段是根据相关性和新近性对候选信息进行评分和选择。这是整个流水线的核心，直接决定了最终上下文的质量。

<details>
<summary>hello_agents/context/builder.py 中 ContextBuilder 类的私有函数 _select</summary>

```python
    def _select(
        self,
        packets: List[ContextPacket],
        user_query: str
    ) -> List[ContextPacket]:
        """Select: 基于分数与预算的筛选"""
        # 1) 计算相关性（关键词重叠）
        query_tokens = set(user_query.lower().split())
        for packet in packets:
            content_tokens = set(packet.content.lower().split())
            if len(query_tokens) > 0:
                overlap = len(query_tokens & content_tokens)
                packet.relevance_score = overlap / len(query_tokens)
            else:
                packet.relevance_score = 0.0
        
        # 2) 计算新近性（指数衰减）
        def recency_score(ts: datetime) -> float:
            delta = max((datetime.now() - ts).total_seconds(), 0)
            tau = 3600  # 1小时时间尺度，可暴露到配置
            return math.exp(-delta / tau)
        
        # 3) 计算复合分：0.7*相关性 + 0.3*新近性
        scored_packets: List[Tuple[float, ContextPacket]] = []
        for p in packets:
            rec = recency_score(p.timestamp)
            score = 0.7 * p.relevance_score + 0.3 * rec
            scored_packets.append((score, p))
        
        # 4) 系统指令单独拿出，固定纳入
        system_packets = [p for (_, p) in scored_packets if p.metadata.get("type") == "instructions"]
        remaining = [p for (s, p) in sorted(scored_packets, key=lambda x: x[0], reverse=True)
                     if p.metadata.get("type") != "instructions"]
        
        # 5) 依据 min_relevance 过滤（对非系统包）
        filtered = [p for p in remaining if p.relevance_score >= self.config.min_relevance]
        
        # 6) 按预算填充
        available_tokens = self.config.get_available_tokens()
        selected: List[ContextPacket] = []
        used_tokens = 0
        
        # 先放入系统指令（不排序）
        for p in system_packets:
            if used_tokens + p.token_count <= available_tokens:
                selected.append(p)
                used_tokens += p.token_count
        
        # 再按分数加入其余
        for p in filtered:
            if used_tokens + p.token_count > available_tokens:
                continue
            selected.append(p)
            used_tokens += p.token_count
        
        return selected
```

</details>

选择阶段的核心算法体现了几个重要的工程考量：

* **评分机制**：采用相关性和新近性的加权组合，权重可配置
* **贪心算法**：按分数从高到低填充，确保在有限预算内选择最有价值的信息
* **过滤机制**：通过 min_relevance 参数过滤低质量信息

（3）Structure：结构化输出

第三阶段是将选中的信息组织成结构化的上下文模板。

<details>
<summary>hello_agents/context/builder.py 中 ContextBuilder 类的私有函数 _structure</summary>

```python
    def _structure(
        self,
        selected_packets: List[ContextPacket],
        user_query: str,
        system_instructions: Optional[str]
    ) -> str:
        """Structure: 组织成结构化上下文模板"""
        sections = []
        
        # [Role & Policies] - 系统指令
        p0_packets = [p for p in selected_packets if p.metadata.get("type") == "instructions"]
        if p0_packets:
            role_section = "[Role & Policies]\n"
            role_section += "\n".join([p.content for p in p0_packets])
            sections.append(role_section)
        
        # [Task] - 当前任务
        sections.append(f"[Task]\n用户问题：{user_query}")
        
        # [State] - 任务状态
        p1_packets = [p for p in selected_packets if p.metadata.get("type") == "task_state"]
        if p1_packets:
            state_section = "[State]\n关键进展与未决问题：\n"
            state_section += "\n".join([p.content for p in p1_packets])
            sections.append(state_section)
        
        # [Evidence] - 事实证据
        p2_packets = [
            p for p in selected_packets
            if p.metadata.get("type") in {"related_memory", "knowledge_base", "retrieval", "tool_result"}
        ]
        if p2_packets:
            evidence_section = "[Evidence]\n事实与引用：\n"
            for p in p2_packets:
                evidence_section += f"\n{p.content}\n"
            sections.append(evidence_section)
        
        # [Context] - 辅助材料（历史等）
        p3_packets = [p for p in selected_packets if p.metadata.get("type") == "history"]
        if p3_packets:
            context_section = "[Context]\n对话历史与背景：\n"
            context_section += "\n".join([p.content for p in p3_packets])
            sections.append(context_section)
        
        # [Output] - 输出约束
        output_section = """[Output]
                            请按以下格式回答：
                            1. 结论（简洁明确）
                            2. 依据（列出支撑证据及来源）
                            3. 风险与假设（如有）
                            4. 下一步行动建议（如适用）"""
        sections.append(output_section)
        
        return "\n\n".join(sections)
```

</details>

结构化阶段将散乱的信息包组织成清晰的分区，这种设计有几个优势：

* **可读性**：清晰的分区让人类和模型都更容易理解上下文结构
* **可调试性**：问题定位更容易，可以快速识别哪个区域的信息有问题
* **可扩展性**：添加新的信息源只需要创建新的分区

（4）Compress：兜底压缩

第四阶段是对超限上下文进行压缩处理。

<details>
<summary>hello_agents/context/builder.py 中 ContextBuilder 类的私有函数 _compress</summary>

```python
    def _compress(self, context: str) -> str:
        """Compress: 压缩与规范化"""
        if not self.config.enable_compression:
            return context
        
        current_tokens = count_tokens(context)
        available_tokens = self.config.get_available_tokens()
        
        if current_tokens <= available_tokens:
            return context
        
        # 简单截断策略（保留前N个token）
        # 实际应用中可用LLM做高保真摘要
        print(f"⚠️ 上下文超预算 ({current_tokens} > {available_tokens})，执行截断")
        
        # 按段落截断，保留结构
        lines = context.split("\n")
        compressed_lines = []
        used_tokens = 0
        
        for line in lines:
            line_tokens = count_tokens(line)
            if used_tokens + line_tokens > available_tokens:
                break
            compressed_lines.append(line)
            used_tokens += line_tokens
        
        return "\n".join(compressed_lines)


def count_tokens(text: str) -> int:
    """计算文本token数（使用tiktoken）"""
    try:
        encoding = tiktoken.get_encoding("cl100k_base")
        return len(encoding.encode(text))
    except Exception:
        # 降级方案：粗略估算（1 token ≈ 4 字符）
        return len(text) // 4
```

</details>

压缩阶段的设计体现了"保持结构完整性"的原则，即使在 token 预算紧张的情况下，也要尽量保留每个分区的关键信息。

**9.3.4 完整使用示例**

现在让我们通过一个完整的示例，展示如何在实际项目中使用 ContextBuilder。

（1）基础使用

<details>
<summary>chapter9/01_context_builder_basic.py</summary>

```python
"""
ContextBuilder 基础使用示例

展示如何使用 ContextBuilder 构建优化的上下文，包括：
1. 初始化 ContextBuilder
2. 准备对话历史
3. 添加记忆
4. 构建结构化上下文
"""
from dotenv import load_dotenv
load_dotenv()
from hello_agents.context import ContextBuilder, ContextConfig
from hello_agents.tools import MemoryTool, RAGTool
from hello_agents.core.message import Message
from datetime import datetime


def main():
    print("=" * 80)
    print("ContextBuilder 基础使用示例")
    print("=" * 80 + "\n")

    # 1. 初始化工具（Optional）
    print("1. 初始化工具...")
    # memory_tool = MemoryTool(user_id="user123")
    # rag_tool = RAGTool(knowledge_base_path="./knowledge_base")

    # 2. 创建 ContextBuilder
    print("2. 创建 ContextBuilder...")
    config = ContextConfig(
        max_tokens=3000,
        reserve_ratio=0.2,
        min_relevance=0,#最小相关性阈值，0代表所有历史信息会被保留,
        enable_compression=True
    )

    builder = ContextBuilder(
        # memory_tool=memory_tool,
        # rag_tool=rag_tool,
        config=config
    )

    # 3. 准备对话历史
    print("3. 准备对话历史...")
    conversation_history = [
        Message(content="我正在开发一个数据分析工具", role="user", timestamp=datetime.now()),
        Message(content="很好!数据分析工具通常需要处理大量数据。您计划使用什么技术栈?", role="assistant", timestamp=datetime.now()),
        Message(content="我打算使用Python和Pandas,已经完成了CSV读取模块", role="user", timestamp=datetime.now()),
        Message(content="不错的选择!Pandas在数据处理方面非常强大。接下来您可能需要考虑数据清洗和转换。", role="assistant", timestamp=datetime.now()),
    ]

    # 4. 添加一些记忆
    print("4. 添加记忆...")
    # memory_tool.run({
    #     "action": "add",
    #     "content": "用户正在开发数据分析工具,使用Python和Pandas",
    #     "memory_type": "semantic",
    #     "importance": 0.8
    # })

    # memory_tool.run({
    #     "action": "add",
    #     "content": "已完成CSV读取模块的开发",
    #     "memory_type": "episodic",
    #     "importance": 0.7
    # })

    # 5. 构建上下文
    print("5. 构建上下文...\n")
    context_str = builder.build(
        user_query="如何优化Pandas的内存占用?",
        conversation_history=conversation_history,
        system_instructions="你是一位资深的Python数据工程顾问。你的回答需要:1) 提供具体可行的建议 2) 解释技术原理 3) 给出代码示例"
    )

    print("=" * 80)
    print("构建的上下文 (结构化字符串):")
    print("=" * 80)
    print(context_str)
    print("=" * 80)
    print()

    # 6. 将上下文字符串转换为消息格式供 LLM 使用
    print("6. 将上下文传给 LLM...")
    messages = [
        {"role": "system", "content": context_str},
        {"role": "user", "content": "请回答"}

    ]

    from hello_agents.core.llm import HelloAgentsLLM
    llm = HelloAgentsLLM()
    # 注意: 实际使用时需要配置 LLM
    response = llm.invoke(messages)
    print(f"LLM 回答: {response}")

    print("✅ ContextBuilder 演示完成!")
    print("\n提示: ContextBuilder 返回的是结构化的上下文字符串,")
    print("      可以直接作为 system message 传给 LLM。")


if __name__ == "__main__":
    main()
```

</details>

（2）运行效果展示

<details>
<summary>chapter9/01_context_builder_basic.py 的运行结果</summary>

```
================================================================================
ContextBuilder 基础使用示例
================================================================================

1. 初始化工具...
2. 创建 ContextBuilder...
3. 准备对话历史...
4. 添加记忆...
5. 构建上下文...

================================================================================
构建的上下文 (结构化字符串):
================================================================================
[Role & Policies]
你是一位资深的Python数据工程顾问。你的回答需要:1) 提供具体可行的建议 2) 解释技术原理 3) 给出代码示例

[Task]
用户问题：如何优化Pandas的内存占用?

[Context]
对话历史与背景：
[user] 我正在开发一个数据分析工具
[assistant] 很好!数据分析工具通常需要处理大量数据。您计划使用什么技术栈?
[user] 我打算使用Python和Pandas,已经完成了CSV读取模块
[assistant] 不错的选择!Pandas在数据处理方面非常强大。接下来您可能需要考虑数据清洗和转换。

[Output]
                            请按以下格式回答：
                            1. 结论（简洁明确）
                            2. 依据（列出支撑证据及来源）
                            3. 风险与假设（如有）
                            4. 下一步行动建议（如适用）
================================================================================

6. 将上下文传给 LLM...
LLM 回答: # Pandas 内存优化方案

## 1. 结论（简洁明确）

**核心优化手段是“避免默认的宽类型”和“避免一次性全量加载”**。具体来说：优先在 `pd.read_csv()` 阶段通过 `dtype` 参数显式指定更窄的数据类型（如 `int32`、`float32`、`category`），以实现 50%-70% 的内存削减；对于超大文件，使用 `chunksize` 分块处理，彻底规避峰值内存压力。

## 2. 依据（列出支撑证据及来源）

**依据一：Pandas 默认类型过于“奢侈”**  
Pandas 在读取 CSV 时默认将整数归为 `int64`，浮点数归为 `float64`。但在实际数据中，多数列的值域远小于 64 位范围。使用 `pd.to_numeric(df[col], downcast='integer'/'float')` 或直接声明 `dtype={'col': 'int16'}`,可显著降低内存。  
*来源：Pandas官方文档 [Pandas Scale and Performance](https://pandas.pydata.org/pandas-docs/stable/user_guide/scale.html)*

**依据二：字符串列的 `object` 类型是内存黑洞**  
Pandas 使用 Python 对象保存字符串，每个字符串都包含指向内存的引用、内存大小统计信息等维护开销。将其转换为 Pandas 的 `category` 类型（或使用 PyArrow 引擎的 `string[pyarrow]` 类型）可压缩重复类别。当某列的唯一值数量小于 50% 时，`category` 通常能大幅节省内存。  
*来源：Pandas官方文档关于 `Categorical` 的说明，以及实际基准测试（如 Jake VanderPlas 的 Python Data Science Handbook）*

**依据三：`read_csv` 的预处理参数是性能关键**  
根据内存消耗理论，数据在未载入 DataFrame 之前，通过 `usecols` 仅读取必要列，可避免无谓的内存分配；通过 `parse_dates` 将日期字符串转换为 `datetime64`（仅 8 字节/条）比保持字符串对象高效得多。  
*来源：Pandas 早期性能优化论文与社区广泛实践*

**依据四：Parquet 格式具有天然压缩优势**  
Parquet 采用列式存储与压缩算法（如 Snappy、ZSTD），直接读取磁盘上的压缩数据时，Pandas 只需在内存中解压所需列。相比 CSV 的纯文本，Parquet 通常能减少 10-20 倍的磁盘占用，并利用 Arrow 内存格式减少副本开销。  
*来源：Apache Parquet 官方文档及 PyArrow 集成测试*

## 3. 风险与假设（如有）

- **数值精度损失**：`float64` 降为 `float32` 可能导致第 6 位小数后出现偏差；`int64` 降为 `int16` 若超出范围（-32768 ~ 32767）会造成 OverflowError。**必须**先检查列的最大最小值。
- **`category` 的性能双刃剑**：虽然 `category` 节省内存，但在某些过滤、排序操作中，底层查找逻辑可能比 `object` 类型稍慢（取决于唯一值基数）。高基数（如用户 ID）列不适合用 `category`。
- **分块处理牺牲了全局操作的直观性**：若使用 `chunksize`，跨 chunk 的聚合（如总平均值、全局排序）需要手动编写 reduce 逻辑，代码复杂度上升。
- **假设**：分析工具的目标环境大概率是单机（非 Spark 集群），因此内存限制是首要瓶颈，而非计算吞吐。

## 4. 下一步行动建议（如适用）

根据你当前的阶段（已构建 CSV 读取模块），建议按以下顺序快速落地：

1. **立即对现有 CSV 模块做内存剖析**：  
   使用 `df.info(memory_usage='deep')` 找出内存大户列，确认是否存在 `object` 类型的数值列或日期列。

2. **重构 `read_csv` 调用，加入预定义 `dtype` 字典**：  
   ```python
   dtype_map = {
       'user_id': 'int32',
       'age': 'int8',
       'salary': 'float32',
       'gender': 'category',
       'email': 'string'
   }
   df = pd.read_csv('data.csv', dtype=dtype_map, parse_dates=['signup_date'], usecols=['user_id', 'age', ...])
   ```
   若发现某列存在 `NaN` 且声明了 `int` 类型，需使用 `Int16`（扩展类型，可容纳 NaN）或先填充再转换。

3. **对于超大 CSV（>1GB），改为分块流式处理**：  
   ```python
   chunk_list = []
   for chunk in pd.read_csv('data.csv', chunksize=100_000, dtype=dtype_map):
       # 你的清洗逻辑,如去重、缺失值填充,尽量在 chunk 内完成
       clean_chunk = clean(chunk)
       chunk_list.append(clean_chunk)
   final_df = pd.concat(chunk_list, ignore_index=True)
   ```

4. **考虑将清洗后的中间数据落盘为 Parquet**：  
   在完成一次 CSV 清洗后，立即写回 `data.parquet`。后续迭代直接读取 Parquet，充分利用列式压缩，避免重复解析 CSV 的高额开销：
   ```python
   df.to_parquet('clean_data.parquet', engine='pyarrow', compression='zstd')
   ```

如果你愿意，我可以基于你当前的数据结构，帮你生成一份具体的 `dtype` 映射检测脚本。请提供你 CSV 中代表性几列的样例数据即可。
✅ ContextBuilder 演示完成!

提示: ContextBuilder 返回的是结构化的上下文字符串,
      可以直接作为 system message 传给 LLM。
```

</details>


运行上述代码后，您将看到如下结构化的上下文输出：

这个结构化的上下文包含了所有必要的信息：

* `[Role & Policies]`：明确了 AI 的角色和回答要求
* `[Task]`：清晰地表达了用户的问题
* `[Evidence]`：从 RAG 系统检索的相关知识
* `[Context]`：对话历史和相关记忆，提供了充分的背景信息
* `[Output]`：指导 LLM 如何组织回答

（3）与 Agent 集成

最后，让我们展示如何将 ContextBuilder 集成到 Agent 中：

<details>
<summary>点击展开完整代码</summary>

```python
from hello_agents import SimpleAgent, HelloAgentsLLM, ToolRegistry
from hello_agents.context import ContextBuilder, ContextConfig
from hello_agents.tools import MemoryTool, RAGTool

class ContextAwareAgent(SimpleAgent):
    """具有上下文感知能力的 Agent"""

    def __init__(self, name: str, llm: HelloAgentsLLM, **kwargs):
        super().__init__(name=name, llm=llm, system_prompt=kwargs.get("system_prompt", ""))

        # 初始化上下文构建器
        self.memory_tool = MemoryTool(user_id=kwargs.get("user_id", "default"))
        self.rag_tool = RAGTool(knowledge_base_path=kwargs.get("knowledge_base_path", "./kb"))

        self.context_builder = ContextBuilder(
            memory_tool=self.memory_tool,
            rag_tool=self.rag_tool,
            config=ContextConfig(max_tokens=4000)
        )

        self.conversation_history = []

    def run(self, user_input: str) -> str:
        """运行 Agent,自动构建优化的上下文"""

        # 1. 使用 ContextBuilder 构建优化的上下文
        optimized_context = self.context_builder.build(
            user_query=user_input,
            conversation_history=self.conversation_history,
            system_instructions=self.system_prompt
        )

        # 2. 使用优化后的上下文调用 LLM
        messages = [
            {"role": "system", "content": optimized_context},
            {"role": "user", "content": user_input}
        ]
        response = self.llm.invoke(messages)

        # 3. 更新对话历史
        from hello_agents.core.message import Message
        from datetime import datetime

        self.conversation_history.append(
            Message(content=user_input, role="user", timestamp=datetime.now())
        )
        self.conversation_history.append(
            Message(content=response, role="assistant", timestamp=datetime.now())
        )

        # 4. 将重要交互记录到记忆系统
        self.memory_tool.run({
            "action": "add",
            "content": f"Q: {user_input}\nA: {response[:200]}...",  # 摘要
            "memory_type": "episodic",
            "importance": 0.6
        })

        return response

# 使用示例
agent = ContextAwareAgent(
    name="数据分析顾问",
    llm=HelloAgentsLLM(),
    system_prompt="你是一位资深的Python数据工程顾问。",
    user_id="user123",
    knowledge_base_path="./data_science_kb"
)

response = agent.run("如何优化Pandas的内存占用?")
print(response)
```

</details>

这个结构化的上下文包含了所有必要的信息：

* **[Role & Policies]**：明确了 AI 的角色和回答要求
* **[Task]**：清晰地表达了用户的问题
* **[Evidence]**：从 RAG 系统检索的相关知识
* **[Context]**：对话历史和相关记忆，提供了充分的背景信息
* **[Output]**：指导 LLM 如何组织回答

（3）与 Agent 集成

最后，让我们展示如何将 ContextBuilder 集成到 Agent 中：

<details>
<summary>chapter9/02_context_builder_with_agent.py</summary>

```python
"""
ContextBuilder 与 Agent 集成示例

展示如何将 ContextBuilder 集成到 Agent 中，实现：
1. 上下文感知的 Agent
2. 自动构建优化的上下文
3. 记忆管理与上下文构建的协同
"""
from dotenv import load_dotenv
load_dotenv()
from hello_agents import SimpleAgent, HelloAgentsLLM, ToolRegistry
from hello_agents.context import ContextBuilder, ContextConfig
#from hello_agents.tools import MemoryTool, RAGTool
from hello_agents.core.message import Message
from datetime import datetime


class ContextAwareAgent(SimpleAgent):
    """具有上下文感知能力的 Agent"""

    def __init__(self, name: str, llm: HelloAgentsLLM, **kwargs):
        super().__init__(name=name, llm=llm, **kwargs)

        
        #（Optional）
        # self.memory_tool = MemoryTool(user_id=kwargs.get("user_id", "default")) 
        # self.rag_tool = RAGTool(knowledge_base_path=kwargs.get("knowledge_base_path", "./kb"))

        # 初始化上下文构建器
        self.context_builder = ContextBuilder(
            # memory_tool=self.memory_tool,
            # rag_tool=self.rag_tool,
            config=ContextConfig(max_tokens=4000)
        )

        self.conversation_history = []

    def run(self, user_input: str) -> str:
        """运行 Agent,自动构建优化的上下文"""

        # 1. 使用 ContextBuilder 构建优化的上下文
        optimized_context = self.context_builder.build(
            user_query=user_input,
            conversation_history=self.conversation_history,
            system_instructions=self.system_prompt
        )

        # 2. 使用优化后的上下文调用 LLM
        messages = [
            {"role": "system", "content": optimized_context},
            {"role": "user", "content": user_input}
        ]
        response = self.llm.invoke(messages).content

        # 3. 更新对话历史
        self.conversation_history.append(
            Message(content=user_input, role="user", timestamp=datetime.now())
        )
        self.conversation_history.append(
            Message(content=response, role="assistant", timestamp=datetime.now())
        )

        # 4. 将重要交互记录到记忆系统
        # self.memory_tool.run({
        #     "action": "add",
        #     "content": f"Q: {user_input}\nA: {response[:200]}...",  # 摘要
        #     "memory_type": "episodic",
        #     "importance": 0.6
        # })

        return response


def main():
    print("=" * 80)
    print("ContextBuilder 与 Agent 集成示例")
    print("=" * 80 + "\n")

    # 配置 LLM
    from hello_agents.core.llm import HelloAgentsLLM
    llm = HelloAgentsLLM()

    # 使用示例
    agent = ContextAwareAgent(
        name="数据分析顾问",
        llm=llm,
        system_prompt="你是一位资深的Python数据工程顾问。"
    )

    # 进行对话
    response = agent.run("如何优化Pandas的内存占用?")
    print(f"助手回答:\n{response}\n")

    # 继续对话
    response = agent.run("能给出具体的代码示例吗?")
    print(f"助手回答:\n{response}\n")

    print("=" * 80)


if __name__ == "__main__":
    main()
```

</details>

通过这种方式，ContextBuilder 成为了 Agent 的"上下文管理大脑"，自动处理信息的收集、筛选和组织，让 Agent 始终能够在最优的上下文下进行推理和生成。

**9.3.5 最佳实践与优化建议**

在实际应用 ContextBuilder 时，以下几点最佳实践值得注意：

1. **动态调整 token 预算**：根据任务复杂度动态调整 **max_tokens**，简单任务使用较小预算，复杂任务增加预算。
2. **相关性计算优化**：在生产环境中，将简单的关键词重叠替换为向量相似度计算，提升检索质量。
3. **缓存机制**：对于不变的系统指令和知识库内容，可以实现缓存机制，避免重复计算。
4. **监控与日志**：记录每次上下文构建的统计信息(选中信息数量、token 使用率等)，便于后续优化。
5. **A/B 测试**：对于关键参数(如相关性权重、新近性权重)，通过 A/B 测试找到最优配置。


### 9.4 **NoteTool：结构化笔记**

NoteTool 是为"长时程任务"提供的**结构化外部记忆组件**。它以 Markdown 文件作为载体，头部使用 YAML 前置元数据记录关键信息，正文用于记录状态、结论、阻塞与行动项等内容。

这种设计结合了人类可读性、版本控制友好性和易于回注上下文的特性，是构建长时程智能体的重要工具。

**9.4.1 设计理念与应用场景**

在深入实现细节之前，让我们首先理解 NoteTool 的设计理念和典型应用场景。

（1）为什么需要 NoteTool?

在第八章中，我们介绍了 MemoryTool，它提供了强大的记忆管理能力。然而，MemoryTool 主要关注**对话式记忆**——短期工作记忆、情景记忆和语义记忆。

对于需要长期追踪、结构化管理的**项目式任务**，我们需要一种更轻量、更人类友好的记录方式。

NoteTool 填补了这个gap，它提供了：

* **结构化记录**：使用 Markdown + YAML 格式，既适合机器解析，也方便人类阅读和编辑
* **版本友好**：纯文本格式，天然支持 Git 等版本控制系统
* **低开销**：无需复杂的数据库操作，适合轻量级的状态追踪
* **灵活分类**：通过 **type** 和 **tags** 灵活组织笔记，支持多维度检索

（2）典型应用场景

NoteTool 特别适合以下场景：

**场景1：长期项目追踪**

想象一个智能体正在协助完成一个大型代码库的重构任务，这可能需要几天甚至几周。NoteTool 可以记录：

* `task_state`：当前阶段的任务状态和进度
* `conclusion`：每个阶段结束后的关键结论
* `blocker`：遇到的问题和阻塞点
* `action`：下一步的行动计划

```python
# 记录任务状态
notes.run({
    "action": "create",
    "title": "重构项目 - 第一阶段",
    "content": "已完成数据模型层的重构,测试覆盖率达到85%。下一步将重构业务逻辑层。",
    "note_type": "task_state",
    "tags": ["refactoring", "phase1"]
})

# 记录阻塞点
notes.run({
    "action": "create",
    "title": "依赖冲突问题",
    "content": "发现某些第三方库版本不兼容,需要解决。影响范围:业务逻辑层的3个模块。",
    "note_type": "blocker",
    "tags": ["dependency", "urgent"]
})
```

**场景2：研究任务管理**

一个智能研究助手在进行文献综述时，可以使用 NoteTool 记录：

* 每篇论文的核心观点(`conclusion`)
* 待深入调研的主题(`action`)
* 重要的参考文献(`reference`)

**场景3：与 ContextBuilder 配合**

在每轮对话前，Agent 可以通过 **search** 或 **list** 操作检索相关笔记，并将其注入到上下文中：

```python
# 在 Agent 的 run 方法中
def run(self, user_input: str) -> str:
    # 1. 检索相关笔记
    relevant_notes = self.note_tool.run({
        "action": "search",
        "query": user_input,
        "limit": 3
    })

    # 2. 将笔记内容转换为 ContextPacket
    note_packets = []
    for note in relevant_notes:
        note_packets.append(ContextPacket(
            content=note['content'],
            timestamp=note['updated_at'],
            token_count=self._count_tokens(note['content']),
            relevance_score=0.7,
            metadata={"type": "note", "note_type": note['type']}
        ))

    # 3. 构建上下文时传入笔记
    context = self.context_builder.build(
        user_query=user_input,
        custom_packets=note_packets,
        ...
    )
```

**9.4.2 存储格式详解**

NoteTool 采用了 Markdown + YAML 的混合格式，这种设计兼顾了结构化和可读性。

（1）笔记文件格式

每个笔记都是一个独立的 **.md** 文件，格式如下：

```markdown
---
id: note_20250119_153000_0
title: 项目进展 - 第一阶段
type: task_state
tags: [refactoring, phase1, backend]
created_at: 2025-01-19T15:30:00
updated_at: 2025-01-19T15:30:00
---

# 项目进展 - 第一阶段

## 完成情况

已完成数据模型层的重构,主要改动包括:

1. 统一了实体类的命名规范
2. 引入了类型提示,提升代码可维护性
3. 优化了数据库查询性能

## 测试覆盖

- 单元测试覆盖率: 85%
- 集成测试覆盖率: 70%

## 下一步计划

1. 重构业务逻辑层
2. 解决依赖冲突问题
3. 提升集成测试覆盖率至85%
```

这种格式的优势：

1. **YAML 元数据**：机器可解析，支持精确的字段提取和检索
2. **Markdown 正文**：人类可读，支持丰富的格式化(标题、列表、代码块等)
3. **文件名即 ID**：简化管理，每个笔记的文件名就是其唯一标识

（2）索引文件

NoteTool 维护一个 `notes_index.json` 文件，用于快速检索和管理笔记：

```json
{
  "note_20250119_153000_0": {
    "id": "note_20250119_153000_0",
    "title": "项目进展 - 第一阶段",
    "type": "task_state",
    "tags": ["refactoring", "phase1", "backend"],
    "created_at": "2025-01-19T15:30:00",
    "updated_at": "2025-01-19T15:30:00",
    "file_path": "./notes/note_20250119_153000_0.md"
  }
}
```

这个索引文件的作用：

* 快速检索：无需打开每个文件，直接从索引中查找
* 元数据管理：集中管理所有笔记的元数据
* 完整性校验：可以检测文件缺失或损坏

**9.4.3 核心操作详解**

NoteTool 提供了七个核心操作，覆盖了笔记的完整生命周期管理。

1. create：创建笔记
2. read：读取笔记
3. update：更新笔记
4. search：搜索笔记
5. list：列出笔记
6. summary：笔记摘要
7. delete：删除笔记

<details>
<summary>hello_agents/tools/builtin/note_tool.py</summary>

``````python
"""NoteTool - 结构化笔记工具

为Agent提供结构化笔记能力，支持：
- 创建/读取/更新/删除笔记
- 按类型组织（任务状态、结论、阻塞项、行动计划等）
- 持久化存储（Markdown格式，带YAML前置元数据）
- 搜索与过滤
- 与MemoryTool集成（可选）

使用场景：
- 长时程任务的状态跟踪
- 关键结论与依赖记录
- 待办事项与行动计划
- 项目知识沉淀

笔记格式示例：
```markdown
---
id: note_20250118_120000_0
title: 项目进展
type: task_state
tags: [milestone, phase1]
created_at: 2025-01-18T12:00:00
updated_at: 2025-01-18T12:00:00
---

# 项目进展

已完成需求分析，下一步：设计方案

## 关键里程碑
- [x] 需求收集
- [ ] 方案设计
```
"""

from typing import Dict, Any, List, Optional
from datetime import datetime
from pathlib import Path
import json
import os
import re

from ..base import Tool, ToolParameter


class NoteTool(Tool):
    """笔记工具
    
    为Agent提供结构化笔记管理能力，支持多种笔记类型：
    - task_state: 任务状态
    - conclusion: 关键结论
    - blocker: 阻塞项
    - action: 行动计划
    - reference: 参考资料
    - general: 通用笔记
    
    用法示例：
    ```python
    note_tool = NoteTool(workspace="./project_notes")
    
    # 创建笔记
    note_tool.run({
        "action": "create",
        "title": "项目进展",
        "content": "已完成需求分析，下一步：设计方案",
        "note_type": "task_state",
        "tags": ["milestone", "phase1"]
    })
    
    # 读取笔记
    notes = note_tool.run({"action": "list", "note_type": "task_state"})
    ```
    """
    
    def __init__(
        self,
        workspace: str = "./notes",
        auto_backup: bool = True,
        max_notes: int = 1000
    ):
        super().__init__(
            name="note",
            description="笔记工具 - 创建、读取、更新、删除结构化笔记，支持任务状态、结论、阻塞项等类型"
        )
        
        self.workspace = Path(workspace)
        self.auto_backup = auto_backup
        self.max_notes = max_notes
        
        # 确保工作目录存在
        self.workspace.mkdir(parents=True, exist_ok=True)
        
        # 笔记索引文件
        self.index_file = self.workspace / "notes_index.json"
        self._load_index()
    
    def _load_index(self):
        """加载笔记索引"""
        if self.index_file.exists():
            with open(self.index_file, 'r', encoding='utf-8') as f:
                self.notes_index = json.load(f)
        else:
            self.notes_index = {
                "notes": [],
                "metadata": {
                    "created_at": datetime.now().isoformat(),
                    "total_notes": 0
                }
            }
            self._save_index()
    
    def _save_index(self):
        """保存笔记索引"""
        with open(self.index_file, 'w', encoding='utf-8') as f:
            json.dump(self.notes_index, f, ensure_ascii=False, indent=2)
    
    def _generate_note_id(self) -> str:
        """生成笔记ID"""
        timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
        count = len(self.notes_index["notes"])
        return f"note_{timestamp}_{count}"
    
    def _get_note_path(self, note_id: str) -> Path:
        """获取笔记文件路径"""
        return self.workspace / f"{note_id}.md"
    
    def _note_to_markdown(self, note: Dict[str, Any]) -> str:
        """将笔记对象转换为Markdown格式"""
        # YAML前置元数据
        frontmatter = "---\n"
        frontmatter += f"id: {note['id']}\n"
        frontmatter += f"title: {note['title']}\n"
        frontmatter += f"type: {note['type']}\n"
        if note.get('tags'):
            tags_str = json.dumps(note['tags'])
            frontmatter += f"tags: {tags_str}\n"
        frontmatter += f"created_at: {note['created_at']}\n"
        frontmatter += f"updated_at: {note['updated_at']}\n"
        frontmatter += "---\n\n"
        
        # Markdown内容
        content = f"# {note['title']}\n\n"
        content += note['content']
        
        return frontmatter + content
    
    def _markdown_to_note(self, markdown_text: str) -> Dict[str, Any]:
        """将Markdown文本解析为笔记对象"""
        # 提取YAML前置元数据
        frontmatter_match = re.match(r'^---\s*\n(.*?)\n---\s*\n', markdown_text, re.DOTALL)
        
        if not frontmatter_match:
            raise ValueError("无效的笔记格式：缺少YAML前置元数据")
        
        frontmatter_text = frontmatter_match.group(1)
        content_start = frontmatter_match.end()
        
        # 解析YAML（简化版）
        note = {}
        for line in frontmatter_text.split('\n'):
            if ':' in line:
                key, value = line.split(':', 1)
                key = key.strip()
                value = value.strip()
                
                # 处理特殊字段
                if key == 'tags':
                    try:
                        note[key] = json.loads(value)
                    except:
                        note[key] = []
                else:
                    note[key] = value
        
        # 提取内容（去掉标题行）
        markdown_content = markdown_text[content_start:].strip()
        # 移除第一行的 # 标题
        lines = markdown_content.split('\n')
        if lines and lines[0].startswith('# '):
            markdown_content = '\n'.join(lines[1:]).strip()
        
        note['content'] = markdown_content
        
        # 添加元数据
        note['metadata'] = {
            'word_count': len(markdown_content),
            'status': 'active'
        }
        
        return note
    
    def run(self, parameters: Dict[str, Any]) -> str:
        """执行工具"""
        if not self.validate_parameters(parameters):
            return "❌ 参数验证失败"
        
        action = parameters.get("action")
        
        if action == "create":
            return self._create_note(parameters)
        elif action == "read":
            return self._read_note(parameters)
        elif action == "update":
            return self._update_note(parameters)
        elif action == "delete":
            return self._delete_note(parameters)
        elif action == "list":
            return self._list_notes(parameters)
        elif action == "search":
            return self._search_notes(parameters)
        elif action == "summary":
            return self._get_summary()
        else:
            return f"❌ 不支持的操作: {action}"
    
    def get_parameters(self) -> List[ToolParameter]:
        """获取工具参数定义"""
        return [
            ToolParameter(
                name="action",
                type="string",
                description=(
                    "操作类型: create(创建), read(读取), update(更新), "
                    "delete(删除), list(列表), search(搜索), summary(摘要)"
                ),
                required=True
            ),
            ToolParameter(
                name="title",
                type="string",
                description="笔记标题（create/update时必需）",
                required=False
            ),
            ToolParameter(
                name="content",
                type="string",
                description="笔记内容（create/update时必需）",
                required=False
            ),
            ToolParameter(
                name="note_type",
                type="string",
                description=(
                    "笔记类型: task_state(任务状态), conclusion(结论), "
                    "blocker(阻塞项), action(行动计划), reference(参考), general(通用)"
                ),
                required=False,
                default="general"
            ),
            ToolParameter(
                name="tags",
                type="array",
                description="标签列表（可选）",
                required=False
            ),
            ToolParameter(
                name="note_id",
                type="string",
                description="笔记ID（read/update/delete时必需）",
                required=False
            ),
            ToolParameter(
                name="query",
                type="string",
                description="搜索关键词（search时必需）",
                required=False
            ),
            ToolParameter(
                name="limit",
                type="integer",
                description="返回结果数量限制（默认10）",
                required=False,
                default=10
            ),
        ]
    
    def _create_note(self, params: Dict[str, Any]) -> str:
        """创建笔记"""
        title = params.get("title")
        content = params.get("content")
        note_type = params.get("note_type", "general")
        tags = params.get("tags", [])
        
        if not title or not content:
            return "❌ 创建笔记需要提供 title 和 content"
        
        # 检查笔记数量限制
        if len(self.notes_index["notes"]) >= self.max_notes:
            return f"❌ 笔记数量已达上限 ({self.max_notes})"
        
        # 生成笔记ID
        note_id = self._generate_note_id()
        
        # 创建笔记对象
        note = {
            "id": note_id,
            "title": title,
            "content": content,
            "type": note_type,
            "tags": tags if isinstance(tags, list) else [],
            "created_at": datetime.now().isoformat(),
            "updated_at": datetime.now().isoformat(),
            "metadata": {
                "word_count": len(content),
                "status": "active"
            }
        }
        
        # 保存笔记文件（Markdown格式）
        note_path = self._get_note_path(note_id)
        markdown_content = self._note_to_markdown(note)
        with open(note_path, 'w', encoding='utf-8') as f:
            f.write(markdown_content)
        
        # 更新索引
        self.notes_index["notes"].append({
            "id": note_id,
            "title": title,
            "type": note_type,
            "tags": tags if isinstance(tags, list) else [],
            "created_at": note["created_at"]
        })
        self.notes_index["metadata"]["total_notes"] = len(self.notes_index["notes"])
        self._save_index()
        
        return f"✅ 笔记创建成功\nID: {note_id}\n标题: {title}\n类型: {note_type}"
    
    def _read_note(self, params: Dict[str, Any]) -> str:
        """读取笔记"""
        note_id = params.get("note_id")
        
        if not note_id:
            return "❌ 读取笔记需要提供 note_id"
        
        note_path = self._get_note_path(note_id)
        if not note_path.exists():
            return f"❌ 笔记不存在: {note_id}"
        
        with open(note_path, 'r', encoding='utf-8') as f:
            markdown_text = f.read()
        
        note = self._markdown_to_note(markdown_text)
        
        return self._format_note(note)
    
    def _update_note(self, params: Dict[str, Any]) -> str:
        """更新笔记"""
        note_id = params.get("note_id")
        
        if not note_id:
            return "❌ 更新笔记需要提供 note_id"
        
        note_path = self._get_note_path(note_id)
        if not note_path.exists():
            return f"❌ 笔记不存在: {note_id}"
        
        # 读取现有笔记
        with open(note_path, 'r', encoding='utf-8') as f:
            markdown_text = f.read()
        note = self._markdown_to_note(markdown_text)
        
        # 更新字段
        if "title" in params:
            note["title"] = params["title"]
        if "content" in params:
            note["content"] = params["content"]
            note["metadata"]["word_count"] = len(params["content"])
        if "note_type" in params:
            note["type"] = params["note_type"]
        if "tags" in params:
            note["tags"] = params["tags"] if isinstance(params["tags"], list) else []
        
        note["updated_at"] = datetime.now().isoformat()
        
        # 保存更新（Markdown格式）
        markdown_content = self._note_to_markdown(note)
        with open(note_path, 'w', encoding='utf-8') as f:
            f.write(markdown_content)
        
        # 更新索引
        for idx_note in self.notes_index["notes"]:
            if idx_note["id"] == note_id:
                idx_note["title"] = note["title"]
                idx_note["type"] = note["type"]
                idx_note["tags"] = note["tags"]
                break
        self._save_index()
        
        return f"✅ 笔记更新成功: {note_id}"
    
    def _delete_note(self, params: Dict[str, Any]) -> str:
        """删除笔记"""
        note_id = params.get("note_id")
        
        if not note_id:
            return "❌ 删除笔记需要提供 note_id"
        
        note_path = self._get_note_path(note_id)
        if not note_path.exists():
            return f"❌ 笔记不存在: {note_id}"
        
        # 删除文件
        note_path.unlink()
        
        # 更新索引
        self.notes_index["notes"] = [
            n for n in self.notes_index["notes"] if n["id"] != note_id
        ]
        self.notes_index["metadata"]["total_notes"] = len(self.notes_index["notes"])
        self._save_index()
        
        return f"✅ 笔记已删除: {note_id}"
    
    def _list_notes(self, params: Dict[str, Any]) -> str:
        """列出笔记"""
        note_type = params.get("note_type")
        limit = params.get("limit", 10)
        
        # 过滤笔记
        filtered_notes = self.notes_index["notes"]
        if note_type:
            filtered_notes = [n for n in filtered_notes if n["type"] == note_type]
        
        # 限制数量
        filtered_notes = filtered_notes[:limit]
        
        if not filtered_notes:
            return "📝 暂无笔记"
        
        result = f"📝 笔记列表（共 {len(filtered_notes)} 条）\n\n"
        for note in filtered_notes:
            result += f"• [{note['type']}] {note['title']}\n"
            result += f"  ID: {note['id']}\n"
            if note.get('tags'):
                result += f"  标签: {', '.join(note['tags'])}\n"
            result += f"  创建时间: {note['created_at']}\n\n"
        
        return result
    
    def _search_notes(self, params: Dict[str, Any]) -> str:
        """搜索笔记"""
        query = params.get("query", "").lower()
        limit = params.get("limit", 10)
        
        if not query:
            return "❌ 搜索需要提供 query"
        
        # 搜索匹配的笔记
        matched_notes = []
        for idx_note in self.notes_index["notes"]:
            note_path = self._get_note_path(idx_note["id"])
            if note_path.exists():
                with open(note_path, 'r', encoding='utf-8') as f:
                    markdown_text = f.read()
                
                try:
                    note = self._markdown_to_note(markdown_text)
                except Exception as e:
                    print(f"⚠️ 解析笔记失败 {idx_note['id']}: {e}")
                    continue
                
                # 检查标题、内容、标签是否匹配
                if (query in note["title"].lower() or
                    query in note["content"].lower() or
                    any(query in tag.lower() for tag in note.get("tags", []))):
                    matched_notes.append(note)
        
        # 限制数量
        matched_notes = matched_notes[:limit]
        
        if not matched_notes:
            return f"📝 未找到匹配 '{query}' 的笔记"
        
        result = f"🔍 搜索结果（共 {len(matched_notes)} 条）\n\n"
        for note in matched_notes:
            result += self._format_note(note, compact=True) + "\n"
        
        return result
    
    def _get_summary(self) -> str:
        """获取笔记摘要"""
        total = len(self.notes_index["notes"])
        
        # 按类型统计
        type_counts = {}
        for note in self.notes_index["notes"]:
            note_type = note["type"]
            type_counts[note_type] = type_counts.get(note_type, 0) + 1
        
        result = f"📊 笔记摘要\n\n"
        result += f"总笔记数: {total}\n\n"
        result += "按类型统计:\n"
        for note_type, count in sorted(type_counts.items()):
            result += f"  • {note_type}: {count}\n"
        
        return result
    
    def _format_note(self, note: Dict[str, Any], compact: bool = False) -> str:
        """格式化笔记输出"""
        if compact:
            return (
                f"[{note['type']}] {note['title']}\n"
                f"ID: {note['id']}\n"
                f"内容: {note['content'][:100]}{'...' if len(note['content']) > 100 else ''}"
            )
        else:
            result = f"📝 笔记详情\n\n"
            result += f"ID: {note['id']}\n"
            result += f"标题: {note['title']}\n"
            result += f"类型: {note['type']}\n"
            if note.get('tags'):
                result += f"标签: {', '.join(note['tags'])}\n"
            result += f"创建时间: {note['created_at']}\n"
            result += f"更新时间: {note['updated_at']}\n"
            result += f"\n内容:\n{note['content']}\n"
            return result


``````

</details>

使用示例：

<details>
<summary>chapter9/03_note_tool_operations.py</summary>

```python
"""
NoteTool 基本操作示例

展示 NoteTool 的核心操作：
1. 创建笔记 (create)
2. 读取笔记 (read)
3. 更新笔记 (update)
4. 搜索笔记 (search)
5. 列出笔记 (list)
6. 笔记摘要 (summary)
7. 删除笔记 (delete)
"""

from hello_agents.tools import NoteTool
import re


def extract_note_id(output: str) -> str:
    """从 NoteTool 的输出文本中提取 note_id"""
    match = re.search(r"ID:\s*(note_[0-9_]+)", output)
    if not match:
        raise ValueError(f"无法从输出解析 note_id:\n{output}")
    return match.group(1)


def main():
    print("=" * 80)
    print("NoteTool 基本操作示例")
    print("=" * 80 + "\n")

    # 初始化 NoteTool
    notes = NoteTool(workspace="./project_notes")

    # 1. 创建笔记
    print("1. 创建笔记...")
    create_output_1 = notes.run({
        "action": "create",
        "title": "重构项目 - 第一阶段",
        "content": """## 完成情况
已完成数据模型层的重构,测试覆盖率达到85%。

## 下一步
重构业务逻辑层""",
        "note_type": "task_state",
        "tags": ["refactoring", "phase1"]
    })
    print(create_output_1 + "\n")
    note_id_1 = extract_note_id(create_output_1)

    # 创建第二个笔记
    create_output_2 = notes.run({
        "action": "create",
        "title": "依赖冲突问题",
        "content": """## 问题描述
发现某些第三方库版本不兼容,需要解决。

## 影响范围
业务逻辑层的3个模块

## 下一步
1. 使用虚拟环境隔离
2. 锁定版本
3. 使用 pipdeptree 分析依赖树""",
        "note_type": "blocker",
        "tags": ["dependency", "urgent"]
    })
    print(create_output_2 + "\n")
    note_id_2 = extract_note_id(create_output_2)

    # 2. 读取笔记
    print("2. 读取笔记...")
    note_detail = notes.run({
        "action": "read",
        "note_id": note_id_1
    })
    print(note_detail + "\n")

    # 3. 更新笔记
    print("3. 更新笔记...")
    update_result = notes.run({
        "action": "update",
        "note_id": note_id_1,
        "content": """## 完成情况
已完成数据模型层的重构,测试覆盖率达到85%。

## 问题
遇到依赖版本冲突,已记录到单独笔记。

## 下一步
先解决依赖冲突,再继续重构业务逻辑层"""
    })
    print(update_result + "\n")

    # 4. 搜索笔记
    print("4. 搜索笔记...")
    search_results = notes.run({
        "action": "search",
        "query": "依赖",
        "limit": 5
    })
    print(search_results + "\n")

    # 5. 列出笔记
    print("5. 列出所有 blocker 类型的笔记...")
    blockers = notes.run({
        "action": "list",
        "note_type": "blocker",
        "limit": 10
    })
    print(blockers + "\n")

    # 6. 笔记摘要
    print("6. 生成笔记摘要...")
    summary_output = notes.run({
        "action": "summary"
    })
    print(summary_output + "\n")

    # 7. 删除笔记 (演示，实际使用时谨慎)
    print("7. 删除笔记 (演示)...")
    # delete_result = notes.run({
    #     "action": "delete",
    #     "note_id": note_id_2
    # })
    # print(delete_result + "\n")
    print(f"(已跳过实际删除操作, 笔记ID: {note_id_2})\n")

    print("=" * 80)
    print("NoteTool 操作演示完成!")
    print("=" * 80)


if __name__ == "__main__":
    main()
```

</details>

<details>
<summary>chapter9/03_note_tool_operations.py 运行结果</summary>

```
================================================================================
NoteTool 基本操作示例
================================================================================

1. 创建笔记...
✅ 笔记创建成功
ID: note_20260827_152729_0
标题: 重构项目 - 第一阶段
类型: task_state

✅ 笔记创建成功
ID: note_20260827_152729_1
标题: 依赖冲突问题
类型: blocker

2. 读取笔记...
📝 笔记详情

ID: note_20260827_152729_0
标题: 重构项目 - 第一阶段
类型: task_state
标签: refactoring, phase1
创建时间: 2026-08-27T15:27:29.207059
更新时间: 2026-08-27T15:27:29.207059

内容:
## 完成情况
已完成数据模型层的重构,测试覆盖率达到85%。

## 下一步
重构业务逻辑层


3. 更新笔记...
✅ 笔记更新成功: note_20260827_152729_0

4. 搜索笔记...
🔍 搜索结果（共 2 条）

[task_state] 重构项目 - 第一阶段
ID: note_20260827_152729_0
内容: ## 完成情况
已完成数据模型层的重构,测试覆盖率达到85%。

## 问题
遇到依赖版本冲突,已记录到单独笔记。

## 下一步
先解决依赖冲突,再继续重构业务逻辑层
[blocker] 依赖冲突问题
ID: note_20260827_152729_1
内容: ## 问题描述
发现某些第三方库版本不兼容,需要解决。

## 影响范围
业务逻辑层的3个模块

## 下一步
1. 使用虚拟环境隔离
2. 锁定版本
3. 使用 pipdeptree 分析依赖树


5. 列出所有 blocker 类型的笔记...
📝 笔记列表（共 1 条）

• [blocker] 依赖冲突问题
  ID: note_20260827_152729_1
  标签: dependency, urgent
  创建时间: 2026-08-27T15:27:29.208054



6. 生成笔记摘要...
📊 笔记摘要

总笔记数: 2

按类型统计:
  • blocker: 1
  • task_state: 1


7. 删除笔记 (演示)...
(已跳过实际删除操作, 笔记ID: note_20260827_152729_1)

================================================================================
NoteTool 操作演示完成!
================================================================================```

```
</details>

**9.4.4 与 ContextBuilder 的深度集成**

NoteTool 的真正威力在于与 ContextBuilder 的配合使用。让我们通过一个完整的案例来展示这种集成。

（1）场景设定

假设我们正在构建一个长期项目助手，它需要：

1. 记录项目的阶段性进展
2. 追踪待解决的问题
3. 在每次对话时，自动回顾相关笔记
4. 基于历史笔记提供连贯的建议

（2）实现示例

<details>
<summary>chapter9/04_note_tool_integration.py</summary>

```python
"""
NoteTool 与 ContextBuilder 集成示例

展示如何将 NoteTool 与 ContextBuilder 集成，实现：
1. 长期项目追踪
2. 笔记检索与上下文注入
3. 基于历史笔记的连贯建议
"""
from dotenv import load_dotenv
load_dotenv()
from hello_agents import SimpleAgent, HelloAgentsLLM
from hello_agents.context import ContextBuilder, ContextConfig, ContextPacket
from hello_agents.tools import MemoryTool, RAGTool, NoteTool
from hello_agents.core.message import Message
from datetime import datetime
from typing import List, Dict


class ProjectAssistant(SimpleAgent):
    """长期项目助手,集成 NoteTool 和 ContextBuilder"""

    def __init__(self, name: str, project_name: str, **kwargs):
        # 配置 LLM
        from hello_agents.core.llm import HelloAgentsLLM
        llm = HelloAgentsLLM()

        super().__init__(name=name, llm=llm, **kwargs)

        self.project_name = project_name

        # 初始化工具
        # self.memory_tool = MemoryTool(user_id=project_name)
        # self.rag_tool = RAGTool(knowledge_base_path=f"./{project_name}_kb")
        self.note_tool = NoteTool(workspace=f"./{project_name}_notes")

        # 初始化上下文构建器
        self.context_builder = ContextBuilder(
            # memory_tool=self.memory_tool,
            # rag_tool=self.rag_tool,
            config=ContextConfig(max_tokens=4000)
        )

        self.conversation_history = []

    def run(self, user_input: str, note_as_action: bool = False) -> str:
        """运行助手,自动集成笔记"""

        # 1. 从 NoteTool 检索相关笔记
        relevant_notes = self._retrieve_relevant_notes(user_input)

        # 2. 将笔记转换为 ContextPacket
        note_packets = self._notes_to_packets(relevant_notes)

        # 3. 构建优化的上下文
        optimized_context = self.context_builder.build(
            user_query=user_input,
            conversation_history=self.conversation_history,
            system_instructions=self._build_system_instructions(),
            additional_packets=note_packets
        )

        # 4. 调用 LLM (以 messages 数组形式传入)
        messages = [
            {"role": "system", "content": optimized_context},
            {"role": "user", "content": user_input}
        ]
        response = self.llm.invoke(messages)

        # 5. 如果需要,将交互记录为笔记
        if note_as_action:
            self._save_as_note(user_input, response)

        # 6. 更新对话历史
        self._update_history(user_input, response)

        return response

    def _retrieve_relevant_notes(self, query: str, limit: int = 3) -> List[Dict]:
        """检索相关笔记"""
        try:
            # 优先检索 blocker 和 action 类型的笔记
            blockers_raw = self.note_tool.run({
                "action": "list",
                "note_type": "blocker",
                "limit": 2
            })

            # 通用搜索
            search_results_raw = self.note_tool.run({
                "action": "search",
                "query": query,
                "limit": limit
            })

            blockers = self._ensure_list_of_dicts(blockers_raw)
            search_results = self._ensure_list_of_dicts(search_results_raw)

            # 合并并去重
            all_notes = {}
            for note in blockers + search_results:
                if not isinstance(note, dict):
                    continue
                note_id = (
                    note.get("note_id")
                    or note.get("id")
                    or note.get("uuid")
                    or note.get("title")
                    or str(hash(str(note)))
                )
                all_notes[note_id] = note
            return list(all_notes.values())[:limit]

        except Exception as e:
            print(f"[WARNING] 笔记检索失败: {e}")
            return []

    def _ensure_list_of_dicts(self, data) -> List[Dict]:
        """将 NoteTool 返回规范化为字典列表"""
        import json
        if data is None:
            return []
        if isinstance(data, str):
            try:
                data = json.loads(data)
            except Exception:
                return []
        if isinstance(data, dict):
            # 兼容 {"items": [...]} 或单条记录
            if "items" in data and isinstance(data["items"], list):
                return [item for item in data["items"] if isinstance(item, dict)]
            return [data]
        if isinstance(data, list):
            return [item for item in data if isinstance(item, dict)]
        return []

    def _notes_to_packets(self, notes: List[Dict]) -> List[ContextPacket]:
        """将笔记转换为上下文包"""
        packets = []

        for note in notes:
            title = note.get("title", "")
            body = note.get("content", "")
            content = f"[笔记:{title}]\n{body}"

            # 安全解析时间戳
            ts = None
            for key in ("updated_at", "updatedAt", "time", "timestamp"):
                if key in note:
                    ts = note.get(key)
                    break
            parsed_ts = None
            if isinstance(ts, (int, float)):
                try:
                    parsed_ts = datetime.fromtimestamp(ts)
                except Exception:
                    parsed_ts = None
            elif isinstance(ts, str):
                try:
                    parsed_ts = datetime.fromisoformat(ts)
                except Exception:
                    parsed_ts = None
            if parsed_ts is None:
                parsed_ts = datetime.now()

            note_type = note.get("type") or note.get("note_type") or "note"
            note_id = (
                note.get("note_id")
                or note.get("id")
                or note.get("uuid")
                or title
                or str(hash(str(note)))
            )

            packets.append(ContextPacket(
                content=content,
                timestamp=parsed_ts,
                token_count=len(content) // 4,  # 简单估算
                relevance_score=0.75,  # 笔记具有较高相关性
                metadata={
                    "type": "note",
                    "note_type": note_type,
                    "note_id": note_id
                }
            ))

        return packets

    def _save_as_note(self, user_input: str, response: str):
        """将交互保存为笔记"""
        try:
            # 判断应该保存为什么类型的笔记
            if "问题" in user_input or "阻塞" in user_input:
                note_type = "blocker"
            elif "计划" in user_input or "下一步" in user_input:
                note_type = "action"
            else:
                note_type = "conclusion"

            self.note_tool.run({
                "action": "create",
                "title": f"{user_input[:30]}...",
                "content": f"## 问题\n{user_input}\n\n## 分析\n{response}",
                "note_type": note_type,
                "tags": [self.project_name, "auto_generated"]
            })

        except Exception as e:
            print(f"[WARNING] 保存笔记失败: {e}")

    def _build_system_instructions(self) -> str:
        """构建系统指令"""
        return f"""你是 {self.project_name} 项目的长期助手。

你的职责:
1. 基于历史笔记提供连贯的建议
2. 追踪项目进展和待解决问题
3. 在回答时引用相关的历史笔记
4. 提供具体、可操作的下一步建议

注意:
- 优先关注标记为 blocker 的问题
- 在建议中说明依据来源(笔记、记忆或知识库)
- 保持对项目整体进度的认识"""

    def _update_history(self, user_input: str, response: str):
        """更新对话历史"""
        self.conversation_history.append(
            Message(content=user_input, role="user", timestamp=datetime.now())
        )
        self.conversation_history.append(
            Message(content=response, role="assistant", timestamp=datetime.now())
        )

        # 限制历史长度
        if len(self.conversation_history) > 10:
            self.conversation_history = self.conversation_history[-10:]


def main():
    print("=" * 80)
    print("NoteTool 与 ContextBuilder 集成示例")
    print("=" * 80 + "\n")

    # 使用示例
    assistant = ProjectAssistant(
        name="项目助手",
        project_name="data_pipeline_refactoring"
    )

    # 第一次交互:记录项目状态
    print("第一次交互:记录项目状态")
    response = assistant.run(
        "我们已经完成了数据模型层的重构,测试覆盖率达到85%。下一步计划重构业务逻辑层。",
        note_as_action=True
    )
    print(f"助手回答: {response}\n")

    # 第二次交互:提出问题
    print("第二次交互:提出问题")
    response = assistant.run(
        "在重构业务逻辑层时,我遇到了依赖版本冲突的问题,该如何解决?"
    )
    print(f"助手回答: {response}\n")

    # 查看笔记摘要
    print("查看笔记摘要:")
    summary = assistant.note_tool.run({"action": "summary"})
    import json
    print(json.dumps(summary, indent=2, ensure_ascii=False).replace("\\n", "\n"))

    print("\n" + "=" * 80)
    print("演示完成!")
    print("=" * 80)


if __name__ == "__main__":
    main()
```

</details>

<details>
<summary>chapter9/04_note_tool_integration.py 运行结果</summary>

```
================================================================================
NoteTool 与 ContextBuilder 集成示例
================================================================================

第一次交互:记录项目状态
助手回答: 1. 结论  
   建议先暂停直接拆解业务逻辑层，对数据模型层重构结果进行“契约校验”和“依赖检查”，再基于明确的业务边界制定分阶段重构计划。业务逻辑层重构的核心目标应是**降低耦合、提升可测试性**，而不是单纯提高覆盖率。

2. 依据（假设基于项目历史记录和通用重构经验）  
   - 历史笔记中记录到数据模型层重构已完成，但尚未看到“对外API兼容性检查”和“回归测试基线”结论。  
   - 数据模型层测试覆盖率达到85%是必要条件，但不代表业务逻辑层可以自动继承这种质量；业务逻辑层常有临时逻辑、外部服务依赖和隐蔽的状态管理，测试难度更高。  
   - 根据项目重构笔记的推进节奏，建议沿用“先防腐层→再服务编排→最后事务/异常处理”的顺序，避免一次性重写。

3. 风险与假设  
   - 风险：数据模型层虽然覆盖率达标，但可能隐藏了字段语义变化或延迟加载问题，会在业务逻辑层重构时放大。  
   - 风险：若直接重构业务逻辑层，可能与现有数据访问方式（如ORM、仓储模式）产生双重复杂度。  
   - 假设：你已有业务逻辑层与数据模型层之间的接口清单和调用关系图；否则需要先补充。

4. 下一步行动建议  
   - 第一步：对比数据模型层重构前后的接口签名和文档，标记所有不兼容变更，并更新业务逻辑层的引用。  
   - 第二步：从业务逻辑层中抽取最独立的“纯计算”模块（如校验、策略、计算规则），先改为无副作用函数并补充单元测试。  
   - 第三步：为涉及外部IO（数据库、消息队列、第三方API）的代码建立接口抽象，引入依赖注入，以便后续用Mock进行测试。  
   - 第四步：选择1-2个核心业务用例做端到端重构试点，验证重构后的业务逻辑层与数据模型层的集成稳定性，再推广。  
   - 第五步：将“业务逻辑层重构”拆成可独立交付的子任务，每个子任务都追加回归测试，目标是重构完成后整体覆盖率不低于当前水平。

第二次交互:提出问题
助手回答: ### 1. 结论

**优先通过统一版本管理 + 显式依赖约束来解决，而不是随意排除或强制升级。**  
具体来说：定位冲突路径 → 引入 BOM / 平台定义（如 `spring-boot-dependencies`、`jackson-bom`）或统一 Gradle 版本目录（Version Catalog）→ 对少量无法规避的冲突使用依赖排除或依赖约束（Dependency Constraints），最后用依赖报告验证收敛。

---

### 2. 依据

- **历史笔记（2024-09-12 业务逻辑层重构方案）**  
  其中已记录：`core-api` 模块与 `persistence-impl` 模块各自依赖了不同版本的 `jackson-databind`（2.15.2 与 2.13.4），当时标记为“重构前待解决的技术债”，建议后续通过统一 BOM 管理。

- **项目历史经验（2024-06-03 支付中台抽离事件）**  
  当时遇到 `guava` 版本冲突（20.0 vs 32.0），最终不是直接升级所有引用方，而是引入团队内部 `dependency-platform` 模块，集中声明常用第三方库版本，效果稳定。

- **依赖仲裁知识库**  
  默认情况下 Maven 采用“最短路径优先 + 先声明先赢”，Gradle 采用“最高版本优先”，但两者都不能保证传递依赖语义兼容。正确做法是基于真实依赖树做决策，避免“看似解决但运行时 ClassNotFoundException”。

---

### 3. 风险与假设

- **假设冲突版本间 API 兼容**：如果业务逻辑层直接调用了旧版本中特有 API，盲目统一到新版本可能引发 `NoSuchMethodError`。需先评估调用点。
- **假设未涉及跨技术栈升级**：如果冲突来自框架整体升级（如 Spring 5→6），需要一并处理 `javax` → `jakarta` 命名空间变更，这不是单纯改版本号能解决的。
- **间接依赖冲突可能隐蔽**：例如 `netty` 冲突可能来自 `grpc` 和 `hbase-client`，但业务逻辑层不直接依赖它们，会被忽视。

---

### 4. 下一步行动建议

1. **生成依赖报告**  
   - Gradle：`./gradlew :你的模块:dependencyInsight --dependency org.apache.commons:commons-lang3`  
   - Maven：`mvn dependency:tree -Dverbose`

2. **梳理冲突库的使用面**  
   在业务逻辑层代码中搜索直接引用，列出用到的类/方法，与两个版本进行比对。

3. **尝试最小干预方案**  
   优先在根 `build.gradle.kts`/`pom.xml` 中显式指定一个版本，并加入依赖约束；不要立即用 `exclude` 剔传递依赖，除非确认该传递依赖不会在运行时被需要。

4. **回归验证**  
   对业务逻辑层的关键用例（尤其涉及 JSON 序列化、数据库访问、远程调用的部分）补充集成测试，确保运行时行为一致。

5. **同步更新项目笔记**  
   在现有“业务逻辑层重构”笔记中追加本次冲突的根因、决策结果及最终锁定的版本清单，方便后续模块遵循同一基线。

---

如果需要，我可以帮你整理一份“冲突库排查表”，直接列出当前项目中待检查的依赖坐标。

查看笔记摘要:
"📊 笔记摘要

总笔记数: 1

按类型统计:
  • action: 1
"

================================================================================
演示完成!
================================================================================
```

</details>

**9.4.5 最佳实践**

在实际使用 NoteTool 时，以下最佳实践能帮助您构建更强大的长时程智能体：

1. **合理的笔记分类**：
     * `task_state`：记录阶段性进展和状态
     * `conclusion`：记录重要的结论和发现
     * `blocker`：记录阻塞问题，优先级最高
     * `action`：记录下一步行动计划
     * `reference`：记录重要的参考资料
2. **定期清理和归档**：
     * 对于已解决的 blocker，更新为 conclusion
     * 对于过时的 action，及时删除或更新
     * 使用 tags 进行版本管理，如 `["v1.0", "completed"]`
3. **与 ContextBuilder 的配合**：
     * 在每轮对话前检索相关笔记
     * 根据笔记类型设置不同的相关性分数(blocker > action > conclusion)
     * 限制笔记数量，避免上下文过载
4. **人机协作**：
     * 笔记是人类可读的 Markdown 格式，支持手动编辑
     * 使用 Git 进行版本控制，追踪笔记的演化
     * 在关键阶段，人工审核 Agent 生成的笔记
5. **自动化工作流**：
     * 定期生成笔记摘要报告
     * 基于笔记自动生成项目进度文档
     * 将笔记内容同步到其他系统(如 Notion、Confluence)


### 9.5 **TerminalTool：即时文件系统访问**

在前面的章节中，我们介绍了 MemoryTool 和 RAGTool，它们分别提供了对话记忆和知识检索能力。

然而，在许多实际场景中，智能体需要**即时访问和探索文件系统**——查看日志文件、分析代码库结构、检索配置文件等。这就是 TerminalTool 的用武之地。

TerminalTool 为智能体提供了**安全的命令行执行能力**，支持常用的文件系统和文本处理命令，同时通过多层安全机制确保系统安全。这种设计实现了 9.2.2 节提到的"即时(Just-in-time, JIT)上下文"理念——智能体不需要预先加载所有文件，而是按需探索和检索。

9.5.1 设计理念与安全机制

（1）为什么需要 TerminalTool?

在构建长程智能体时，我们经常遇到以下场景：

**场景1：代码库探索**

一个开发助手需要帮助用户理解一个大型代码库的结构：

```python
# 传统方式:预先索引所有文件(成本高、可能过时)
rag_tool.add_document("./project/**/*.py")  # 耗时、占用大量存储

# TerminalTool 方式:即时探索
terminal.run({"command": "find . -name '*.py' -type f"})  # 快速、实时
terminal.run({"command": "grep -r 'class UserService' ."})  # 精确定位
terminal.run({"command": "head -n 50 src/services/user.py"})  # 按需查看
```

**场景2：日志文件分析**

一个运维助手需要分析应用日志：

```python
# 检查日志文件大小
terminal.run({"command": "ls -lh /var/log/app.log"})

# 查看最新的错误日志
terminal.run({"command": "tail -n 100 /var/log/app.log | grep ERROR"})

# 统计错误类型分布
terminal.run({"command": "grep ERROR /var/log/app.log | cut -d':' -f3 | sort | uniq -c"})
```

场景3：数据文件预览

一个数据分析助手需要快速了解数据文件的结构：

```python
# 查看 CSV 文件的前几行
terminal.run({"command": "head -n 5 data/sales.csv"})

# 统计行数
terminal.run({"command": "wc -l data/*.csv"})

# 查看列名
terminal.run({"command": "head -n 1 data/sales.csv | tr ',' '\n'"})
```

这些场景的共同特点是：**需要实时、轻量级的文件系统访问，而不是预先索引和向量化**。TerminalTool 正是为这种"探索式"工作流设计的。

（2）安全机制详解

允许智能体执行命令是一个强大但危险的能力。TerminalTool 通过多层安全机制确保系统安全：

**第一层：命令白名单**

只允许安全的只读命令，完全禁止任何可能修改系统的操作：

```python
ALLOWED_COMMANDS = {
    # 文件列表与信息
    'ls', 'dir', 'tree',
    # 文件内容查看
    'cat', 'head', 'tail', 'less', 'more',
    # 文件搜索
    'find', 'grep', 'egrep', 'fgrep',
    # 文本处理
    'wc', 'sort', 'uniq', 'cut', 'awk', 'sed',
    # 目录操作
    'pwd', 'cd',
    # 文件信息
    'file', 'stat', 'du', 'df',
    # 其他
    'echo', 'which', 'whereis',
}
```

如果智能体尝试执行白名单外的命令，会立即被拒绝：

```python
terminal.run({"command": "rm -rf /"})
# ❌ 不允许的命令: rm
# 允许的命令: cat, cd, cut, dir, du, ...
```

**第二层：工作目录限制(沙箱)**

TerminalTool 只能访问指定的工作目录及其子目录，无法访问系统其他部分：

```python
# 初始化时指定工作目录
terminal = TerminalTool(workspace="./project")

# 允许:访问工作目录内的文件
terminal.run({"command": "cat ./src/main.py"})  # ✅

# 禁止:访问工作目录外的文件
terminal.run({"command": "cat /etc/passwd"})  # ❌ 不允许访问工作目录外的路径

# 禁止:通过 .. 逃逸
terminal.run({"command": "cd ../../../etc"})  # ❌ 不允许访问工作目录外的路径
```

这种沙箱机制确保了即使智能体的行为出现异常，也无法影响系统其他部分。

**第三层：超时控制**

每个命令都有执行时间限制，防止无限循环或资源耗尽：

```python
terminal = TerminalTool(
    workspace="./project",
    timeout=30  # 30秒超时
)

# 如果命令执行超过30秒
terminal.run({"command": "find / -name '*.log'"})
# ❌ 命令执行超时（超过 30 秒）
```

**第四层：输出大小限制**

限制命令输出的大小，防止内存溢出：

```python
terminal = TerminalTool(
    workspace="./project",
    max_output_size=10 * 1024 * 1024  # 10MB
)

# 如果输出超过10MB
terminal.run({"command": "cat huge_file.log"})
# ... (前10MB的内容) ...
# ⚠️ 输出被截断（超过 10485760 字节）
```

通过这四层安全机制，TerminalTool 在提供强大能力的同时，最大程度地保证了系统安全。

**9.5.2 核心功能详解**

TerminalTool 的实现聚焦于两个核心功能：命令执行和目录导航。

（1）命令执行

核心的 **_execute_command** 方法负责实际执行命令：

<details>
<summary>hello_agents/tools/builtin/terminal_tool.py 中 TerminalTool 命令行工具类的 _execute_command 私有函数</summary>

```python
    def _execute_command(self, command: str) -> str:
        """执行命令"""
        try:
            # 在当前目录下执行命令
            result = subprocess.run(
                command,
                shell=True,
                cwd=str(self.current_dir),
                capture_output=True,
                text=True,
                timeout=self.timeout,
                env=os.environ.copy()
            )
            
            # 合并标准输出和标准错误
            output = result.stdout
            if result.stderr:
                output += f"\n[stderr]\n{result.stderr}"
            
            # 检查输出大小
            if len(output) > self.max_output_size:
                output = output[:self.max_output_size]
                output += f"\n\n⚠️ 输出被截断（超过 {self.max_output_size} 字节）"
            
            # 添加返回码信息
            if result.returncode != 0:
                output = f"⚠️ 命令返回码: {result.returncode}\n\n{output}"
            
            return output if output else "✅ 命令执行成功（无输出）"
            
        except subprocess.TimeoutExpired:
            return f"❌ 命令执行超时（超过 {self.timeout} 秒）"
        except Exception as e:
            return f"❌ 命令执行失败: {e}"
```

</details>

这个实现的关键点：

* **当前目录感知**：使用 cwd 参数在正确的目录下执行命令
* **错误处理**：捕获并合并标准错误，提供完整的诊断信息
* **返回码检查**：非零返回码会被标记为警告
* **容错设计**：超时和异常都会被妥善处理，不会导致智能体崩溃

（2）目录导航

`cd` 命令的特殊处理支持智能体在文件系统中导航：

<details>
<summary>hello_agents/tools/builtin/terminal_tool.py 中 TerminalTool 命令行工具类的 _handle_cd 私有函数</summary>

```python
    def _handle_cd(self, parts: List[str]) -> str:
        """处理 cd 命令"""
        if not self.allow_cd:
            return "❌ cd 命令已禁用"
        
        if len(parts) < 2:
            # cd 无参数，返回当前目录
            return f"当前目录: {self.current_dir}"
        
        target_dir = parts[1]
        
        # 处理相对路径
        if target_dir == "..":
            new_dir = self.current_dir.parent
        elif target_dir == ".":
            new_dir = self.current_dir
        elif target_dir == "~":
            new_dir = self.workspace
        else:
            new_dir = (self.current_dir / target_dir).resolve()
        
        # 检查是否在工作目录内
        try:
            new_dir.relative_to(self.workspace)
        except ValueError:
            return f"❌ 不允许访问工作目录外的路径: {new_dir}"
        
        # 检查目录是否存在
        if not new_dir.exists():
            return f"❌ 目录不存在: {new_dir}"
        
        if not new_dir.is_dir():
            return f"❌ 不是目录: {new_dir}"
        
        # 更新当前目录
        self.current_dir = new_dir
        return f"✅ 切换到目录: {self.current_dir}"
```

</details>

这种设计支持智能体进行多步骤的文件系统探索：

```python
# 第一步:查看项目结构
terminal.run({"command": "ls -la"})

# 第二步:进入源代码目录
terminal.run({"command": "cd src"})

# 第三步:查找特定文件
terminal.run({"command": "find . -name '*service*.py'"})

# 第四步:查看文件内容
terminal.run({"command": "cat user_service.py"})
```

9.5.3 典型使用模式

TerminalTool 支持多种常见的文件系统操作模式。

1. **探索式导航**：智能体可以像人类开发者一样逐步探索代码库
2. **数据文件分析**：快速了解数据文件的结构和内容
3. **日志文件分析**：实时分析应用日志，快速定位问题
4. **代码库分析**：辅助代码审查和理解

<details>
<summary>chapter9/05_terminal_tool_examples.py</summary>

```python
"""
TerminalTool 使用示例

展示 TerminalTool 的典型使用模式：
1. 探索式导航
2. 数据文件分析
3. 日志文件分析
4. 代码库分析
"""

import os
from pathlib import Path
from hello_agents.tools import TerminalTool

# 获取脚本所在目录
SCRIPT_DIR = Path(__file__).parent.absolute()


def demo_exploratory_navigation():
    """演示探索式导航"""
    print("\n" + "=" * 80)
    print("场景1: 探索式导航")
    print("=" * 80 + "\n")

    terminal = TerminalTool(workspace=str(SCRIPT_DIR))

    # 第一步:查看当前目录
    print("1. 查看当前目录:")
    result = terminal.run({"command": "ls -la"})
    print(result)

    # 第二步:查看Python文件
    print("\n2. 查看Python文件:")
    result = terminal.run({"command": "ls -la *.py"})
    print(result)

    # 第三步:查找特定文件
    print("\n3. 查找特定模式的文件:")
    result = terminal.run({"command": "find . -name '*codebase_maintainer.py'"})
    print(result)

    # 第四步:查看文件内容
    print("\n4. 查看文件内容:")
    result = terminal.run({"command": "head -n 20 codebase_maintainer.py"})
    print(result)


def demo_data_file_analysis():
    """演示数据文件分析"""
    print("\n" + "=" * 80)
    print("场景2: 数据文件分析")
    print("=" * 80 + "\n")

    terminal = TerminalTool(workspace=str(SCRIPT_DIR / "data"))

    # 查看 CSV 文件的前几行
    print("1. 查看 CSV 文件前5行:")
    result = terminal.run({"command": "head -n 5 sales_2024.csv"})
    print(result)

    # 统计总行数
    print("\n2. 统计文件行数:")
    result = terminal.run({"command": "wc -l *.csv"})
    print(result)

    # 提取和统计产品类别
    print("\n3. 统计产品类别分布:")
    result = terminal.run({"command": "tail -n +2 sales_2024.csv | cut -d',' -f3 | sort | uniq -c"})
    print(result)


def demo_log_analysis():
    """演示日志文件分析"""
    print("\n" + "=" * 80)
    print("场景3: 日志文件分析")
    print("=" * 80 + "\n")

    terminal = TerminalTool(workspace=str(SCRIPT_DIR / "logs"))

    # 查看最新的错误日志
    print("1. 查看最新的错误日志:")
    result = terminal.run({"command": "tail -n 50 app.log | grep ERROR"})
    print(result)

    # 统计错误类型分布
    print("\n2. 统计错误类型分布:")
    result = terminal.run({"command": "grep ERROR app.log | awk '{print $4}' | sort | uniq -c | sort -rn"})
    print(result)

    # 查找特定时间段的日志
    print("\n3. 查找特定时间段的日志:")
    result = terminal.run({"command": "grep '2024-01-19 15:' app.log | tail -n 20"})
    print(result)


def demo_codebase_analysis():
    """演示代码库分析"""
    print("\n" + "=" * 80)
    print("场景4: 代码库分析")
    print("=" * 80 + "\n")

    terminal = TerminalTool(workspace=str(SCRIPT_DIR / "codebase"))

    # 统计代码行数
    print("1. 统计代码行数:")
    result = terminal.run({"command": "find . -name '*.py' -exec wc -l {} + | tail -n 1"})
    print(result)

    # 查找所有 TODO 注释
    print("\n2. 查找所有 TODO 注释:")
    result = terminal.run({"command": "grep -rn 'TODO' --include='*.py'"})
    print(result)

    # 查找特定函数的定义
    print("\n3. 查找特定函数的定义:")
    result = terminal.run({"command": "grep -rn 'def process_data' --include='*.py'"})
    print(result)


def demo_security_features():
    """演示安全特性"""
    print("\n" + "=" * 80)
    print("安全特性演示")
    print("=" * 80 + "\n")

    terminal = TerminalTool(workspace=str(SCRIPT_DIR / "project"))

    # 尝试执行不允许的命令
    print("1. 尝试执行危险命令 (rm):")
    result = terminal.run({"command": "rm -rf /"})
    print(result)

    # 尝试访问工作目录外的文件
    print("\n2. 尝试访问工作目录外的文件:")
    result = terminal.run({"command": "cat /etc/passwd"})
    print(result)

    # 尝试逃逸工作目录
    print("\n3. 尝试通过 .. 逃逸工作目录:")
    result = terminal.run({"command": "cd ../../../etc"})
    print(result)


def main():
    print("=" * 80)
    print("TerminalTool 使用示例")
    print("=" * 80)

    # 演示各种使用场景
    demo_exploratory_navigation()
    demo_data_file_analysis()
    demo_log_analysis()
    demo_codebase_analysis()
    demo_security_features()

    print("\n" + "=" * 80)
    print("演示完成!")
    print("=" * 80)


if __name__ == "__main__":
    main()
```

</details>

**9.5.4 与其他工具的协同**

TerminalTool 的真正威力在于与 MemoryTool、NoteTool 和 ContextBuilder 的协同使用。

（1）与 MemoryTool 协同

TerminalTool 发现的信息可以存储到记忆系统中：

```python
# 使用 TerminalTool 发现项目结构
structure = terminal.run({"command": "tree -L 2 src"})

# 存储到语义记忆
memory_tool.run({
    "action": "add",
    "content": f"项目结构:\n{structure}",
    "memory_type": "semantic",
    "importance": 0.8,
    "metadata": {"type": "project_structure"}
})
```

（2）与 NoteTool 协同

重要的发现可以记录为结构化笔记：

```python
# 发现一个性能瓶颈
log_analysis = terminal.run({"command": "grep 'slow query' app.log | tail -n 10"})

# 记录为 blocker 笔记
note_tool.run({
    "action": "create",
    "title": "数据库慢查询问题",
    "content": f"## 问题描述\n发现多个慢查询,影响系统性能\n\n## 日志分析\n```\n{log_analysis}\n```\n\n## 下一步\n1. 分析慢查询SQL\n2. 添加索引\n3. 优化查询逻辑",
    "note_type": "blocker",
    "tags": ["performance", "database"]
})
```

（3）与 ContextBuilder 协同

TerminalTool 的输出可以作为上下文的一部分：

```python
# 探索代码库
code_structure = terminal.run({"command": "ls -R src"})
recent_changes = terminal.run({"command": "git log --oneline -10"})

# 转换为 ContextPacket
from hello_agents.context import ContextPacket
from datetime import datetime

packets = [
    ContextPacket(
        content=f"代码库结构:\n{code_structure}",
        timestamp=datetime.now(),
        token_count=len(code_structure) // 4,
        relevance_score=0.7,
        metadata={"type": "code_structure", "source": "terminal"}
    ),
    ContextPacket(
        content=f"最近提交:\n{recent_changes}",
        timestamp=datetime.now(),
        token_count=len(recent_changes) // 4,
        relevance_score=0.8,
        metadata={"type": "git_history", "source": "terminal"}
    )
]

# 在构建上下文时包含这些信息
context = context_builder.build(
    user_query="如何重构用户服务模块?",
    custom_packets=packets
)
```

### 9.6 **长程智能体实战：代码库维护助手**

现在，让我们将 ContextBuilder、NoteTool 和 TerminalTool 整合起来，构建一个完整的长程智能体——**代码库维护助手**。

这个助手能够：

1. 探索和理解代码库结构
2. 记录发现的问题和改进点
3. 追踪长期的重构任务
4. 在上下文窗口限制下保持连贯性

**9.6.1 场景设定与需求分析**

**业务场景**

假设我们正在维护一个中型 Python Web 应用，这个代码库包含约 50 个 Python 文件，使用 Flask 框架构建，涵盖数据模型、业务逻辑、API 接口等多个模块，同时存在一些技术债务需要逐步清理。

在这样的场景下，我们需要一个智能助手来帮助我们探索代码库，理解项目结构、依赖关系和代码风格；识别代码中的问题，比如代码重复、复杂度过高、缺少测试等；追踪任务进度，记录待办事项、已完成工作和遇到的阻塞；并基于历史上下文提供连贯的重构建议。

**挑战与解决方案**

这个场景面临几个典型的长程任务挑战。

1. **信息量超出上下文窗口**，整个代码库可能包含数万行代码，无法一次性放入上下文窗口，
    * 我们通过使用 TerminalTool 进行即时、按需的代码探索来解决这个问题，只在需要时查看具体文件。
2. **跨会话的状态管理挑战**，重构任务可能持续数天，需要跨多个会话保持进度，
    * 我们使用 NoteTool 记录阶段性进展、待办事项和关键决策来应对。
3. **上下文质量与相关性的问题**，每次对话需要回顾相关的历史信息，但不能被无关信息淹没，
    * 我们通过 ContextBuilder 智能筛选和组织上下文，确保高信号密度。

**9.6.2 系统架构设计**

我们的代码库维护助手采用三层架构：

![图 9.3 代码库维护助手三层架构.png](..%2Fimages%2F%E5%9B%BE%209.3%20%E4%BB%A3%E7%A0%81%E5%BA%93%E7%BB%B4%E6%8A%A4%E5%8A%A9%E6%89%8B%E4%B8%89%E5%B1%82%E6%9E%B6%E6%9E%84.png)

**9.6.3 核心实现**

现在让我们实现这个系统的核心类：

<details>
<summary>chapter9/codebase_maintainer.py</summary>

```python
"""
CodebaseMaintainer - 代码库维护助手

完整的长程智能体实现，整合:
1. ContextBuilder - 上下文管理
2. NoteTool - 结构化笔记
3. TerminalTool - 即时文件访问
4. MemoryTool - 对话记忆

关键改进：使用 Agentic 方式，让 agent 自主决定使用哪些工具
"""

from typing import Dict, Any, List, Optional
from datetime import datetime
import json

from hello_agents import HelloAgentsLLM
from hello_agents.agents import FunctionCallAgent
from hello_agents.context import ContextBuilder, ContextConfig, ContextPacket
from hello_agents.tools import MemoryTool, NoteTool, TerminalTool
from hello_agents.tools.registry import ToolRegistry
from hello_agents.core.message import Message


class CodebaseMaintainer:
    """代码库维护助手 - 长程智能体示例

    整合 ContextBuilder + NoteTool + TerminalTool + MemoryTool
    实现跨会话的代码库维护任务管理
    
    核心特性：
    - Agent 自主使用工具探索代码库
    - 不预定义工作流，完全基于 agent 决策
    - 跨会话记忆和上下文管理
    """

    def __init__(
        self,
        project_name: str,
        codebase_path: str,
        llm: Optional[HelloAgentsLLM] = None
    ):
        self.project_name = project_name
        self.codebase_path = codebase_path
        self.session_id = f"session_{datetime.now().strftime('%Y%m%d_%H%M%S')}"

        # 初始化 LLM
        self.llm = llm or HelloAgentsLLM()

        # 初始化工具
        self.memory_tool = MemoryTool(
            user_id=project_name,
            memory_types=["working"]
        )
        self.note_tool = NoteTool(workspace=f"./{project_name}_notes")
        self.terminal_tool = TerminalTool(workspace=codebase_path, timeout=60)

        # 初始化上下文构建器
        self.context_builder = ContextBuilder(
            memory_tool=self.memory_tool,
            rag_tool=None,  # 本案例不使用 RAG
            config=ContextConfig(
                max_tokens=4000,
                reserve_ratio=0.15,
                min_relevance=0.2,
                enable_compression=True
            )
        )

        # 创建工具注册表并注册工具
        self.tool_registry = ToolRegistry()
        self.tool_registry.register_tool(self.terminal_tool)
        self.tool_registry.register_tool(self.note_tool)
        self.tool_registry.register_tool(self.memory_tool)

        # 创建 Agent
        self.agent = FunctionCallAgent(
            name="CodebaseMaintainer",
            llm=self.llm,
            system_prompt=self._build_base_system_prompt(),
            tool_registry=self.tool_registry,
            enable_tool_calling=True,
            max_tool_iterations=30
        )

        # 对话历史
        self.conversation_history: List[Message] = []

        # 统计信息
        self.stats = {
            "session_start": datetime.now(),
            "commands_executed": 0,
            "notes_created": 0,
            "issues_found": 0,
            "tool_calls": 0
        }

        print(f"✅ 代码库维护助手已初始化: {project_name} (Agentic Mode)")
        print(f"📁 工作目录: {codebase_path}")
        print(f"🆔 会话ID: {self.session_id}")
        print(f"🔧 可用工具: {', '.join(self.tool_registry.list_tools())}")

    def run(self, user_input: str, mode: str = "auto") -> str:
        """运行助手（Agentic 方式）

        Args:
            user_input: 用户输入
            mode: 运行模式提示（给 agent 提供方向性建议）
                - "auto": 自动决策是否使用工具
                - "explore": 建议 agent 侧重代码探索
                - "analyze": 建议 agent 侧重问题分析
                - "plan": 建议 agent 侧重任务规划

        Returns:
            str: 助手的回答
        """
        print(f"\n{'='*80}")
        print(f"👤 用户: {user_input}")
        print(f"{'='*80}\n")

        # 第一步: 检索相关笔记（为 agent 提供上下文）
        relevant_notes = self._retrieve_relevant_notes(user_input)
        note_packets = self._notes_to_packets(relevant_notes)

        # 第二步: 构建优化的上下文
        context = self.context_builder.build(
            user_query=user_input,
            conversation_history=self.conversation_history,
            system_instructions=self._build_system_instructions(mode),
            additional_packets=note_packets
        )

        # 第三步: 让 Agent 自主决策和使用工具
        print("🤖 Agent 正在思考并决定使用哪些工具...\n")
        
        # 更新 agent 的系统提示（包含上下文）
        self.agent.system_prompt = context
        
        # 调用 agent（agent 会自主决定是否使用工具）
        response = self.agent.run(user_input)

        # 第四步: 统计工具使用情况
        self._track_tool_usage()

        # 第五步: 更新对话历史
        self._update_history(user_input, response)

        print(f"\n🤖 助手: {response}\n")
        print(f"{'='*80}\n")

        return response

    def _build_base_system_prompt(self) -> str:
        """构建基础系统提示"""
        return f"""你是 {self.project_name} 项目的代码库维护助手。

你的核心能力:
1. 使用 TerminalTool 探索代码库
   - 你可以执行任何 shell 命令: ls, cat, grep, find, git 等
   - 工作目录: {self.codebase_path}
   
2. 使用 NoteTool 记录发现和任务
   - 创建笔记记录重要发现
   - 笔记类型: blocker(阻塞问题)、action(行动计划)、task_state(任务状态)、conclusion(结论)
   
3. 使用 MemoryTool 存储关键信息
   - 记住重要的上下文信息
   - 跨会话保持连贯性

当前会话ID: {self.session_id}

重要原则:
- 你要自主决定使用哪些工具、执行什么命令
- 探索代码库时，先了解整体结构，再深入细节
- 发现重要信息时，主动使用 NoteTool 记录
- 保持回答的专业性和实用性
"""

    def _track_tool_usage(self):
        """统计工具使用情况"""
        # 从 agent 的执行历史中统计
        if hasattr(self.agent, 'message_history'):
            for msg in self.agent.message_history[-10:]:  # 只看最近10条
                if msg.role == "tool":
                    self.stats["tool_calls"] += 1
                    # 根据工具名统计
                    if "terminal" in str(msg.content).lower() or "command" in str(msg.content).lower():
                        self.stats["commands_executed"] += 1
                    elif "note" in str(msg.content).lower():
                        if "create" in str(msg.content).lower():
                            self.stats["notes_created"] += 1

    def _retrieve_relevant_notes(self, query: str, limit: int = 3) -> List[Dict]:
        """检索相关笔记"""
        try:
            # 优先检索 blocker
            blockers_raw = self.note_tool.run({
                "action": "list",
                "note_type": "blocker",
                "limit": 2
            })
            blockers = self._normalize_note_results(blockers_raw)

            # 搜索相关笔记
            search_results_raw = self.note_tool.run({
                "action": "search",
                "query": query,
                "limit": limit
            })
            search_results = self._normalize_note_results(search_results_raw)

            # 合并去重
            all_notes = {}
            for note in blockers + search_results:
                if not isinstance(note, dict):
                    continue
                note_id = note.get('note_id') or note.get('id')
                if not note_id:
                    continue
                if note_id not in all_notes:
                    all_notes[note_id] = note

            return list(all_notes.values())[:limit]

        except Exception as e:
            print(f"[WARNING] 笔记检索失败: {e}")
            return []

    def _normalize_note_results(self, result: Any) -> List[Dict]:
        """将笔记工具的返回值转换为笔记字典列表"""
        if not result:
            return []

        if isinstance(result, dict):
            return [result]

        if isinstance(result, list):
            return [item for item in result if isinstance(item, dict)]

        if isinstance(result, str):
            text = result.strip()
            if not text:
                return []
            if text.startswith("{") or text.startswith("["):
                try:
                    parsed = json.loads(text)
                    return self._normalize_note_results(parsed)
                except Exception:
                    return []
            return []

        return []

    def _notes_to_packets(self, notes: List[Dict]) -> List[ContextPacket]:
        """将笔记转换为上下文包"""
        packets = []

        for note in notes:
            if not isinstance(note, dict):
                continue
            # 根据笔记类型设置不同的相关性分数
            relevance_map = {
                "blocker": 0.9,
                "action": 0.8,
                "task_state": 0.75,
                "conclusion": 0.7
            }

            note_type = note.get('type', 'general')
            relevance = relevance_map.get(note_type, 0.6)

            content = f"[笔记:{note.get('title', 'Untitled')}]\n类型: {note_type}\n\n{note.get('content', '')}"
            updated_at = note.get('updated_at')
            try:
                note_timestamp = datetime.fromisoformat(updated_at) if updated_at else datetime.now()
            except (ValueError, TypeError):
                note_timestamp = datetime.now()

            packets.append(ContextPacket(
                content=content,
                timestamp=note_timestamp,
                token_count=len(content) // 4,
                relevance_score=relevance,
                metadata={
                    "type": "note",
                    "note_type": note_type,
                    "note_id": note.get('note_id') or note.get('id')
                }
            ))

        return packets

    def _build_system_instructions(self, mode: str) -> str:
        """构建系统指令（Agentic 方式）"""
        base_instructions = self._build_base_system_prompt()

        mode_hints = {
            "explore": """
用户当前关注: 探索代码库

建议策略:
- 考虑使用 TerminalTool 了解代码结构（如 find, ls, tree）
- 查看关键文件（如 README, 主要模块）
- 将架构信息记录到笔记方便后续查阅
""",
            "analyze": """
用户当前关注: 分析代码质量

建议策略:
- 考虑使用 grep 查找潜在问题（TODO, FIXME, BUG）
- 分析代码复杂度和结构
- 将发现的问题记录为 blocker 或 action 笔记
""",
            "plan": """
用户当前关注: 任务规划

建议策略:
- 回顾历史笔记了解当前进度
- 基于已有信息制定行动计划
- 创建或更新 task_state 类型的笔记
""",
            "auto": """
用户当前关注: 自由对话

建议策略:
- 根据用户需求灵活决策
- 在需要时主动使用工具获取信息
- 不需要时可以直接回答
"""
        }

        return base_instructions + "\n" + mode_hints.get(mode, mode_hints["auto"])


    def _update_history(self, user_input: str, response: str):
        """更新对话历史"""
        self.conversation_history.append(
            Message(content=user_input, role="user", timestamp=datetime.now())
        )
        self.conversation_history.append(
            Message(content=response, role="assistant", timestamp=datetime.now())
        )

        # 限制历史长度(保留最近10轮对话)
        if len(self.conversation_history) > 20:
            self.conversation_history = self.conversation_history[-20:]

    # === 便捷方法 ===

    def explore(self, target: str = ".") -> str:
        """探索代码库（Agentic 方式）
        
        Agent 会自主决定使用哪些命令来探索代码库
        """
        return self.run(f"请探索 {target} 的代码结构，了解项目组织方式", mode="explore")

    def analyze(self, focus: str = "") -> str:
        """分析代码质量（Agentic 方式）
        
        Agent 会自主决定如何分析代码质量
        """
        query = f"请分析代码质量" + (f"，重点关注{focus}" if focus else "")
        return self.run(query, mode="analyze")

    def plan_next_steps(self) -> str:
        """规划下一步任务（Agentic 方式）
        
        Agent 会查看历史笔记并规划下一步
        """
        return self.run("根据我们之前的分析和当前进度，规划下一步任务", mode="plan")

    def execute_command(self, command: str) -> str:
        """执行终端命令"""
        result = self.terminal_tool.run({"command": command})
        self.stats["commands_executed"] += 1
        return result

    def create_note(
        self,
        title: str,
        content: str,
        note_type: str = "general",
        tags: List[str] = None
    ) -> str:
        """创建笔记"""
        result = self.note_tool.run({
            "action": "create",
            "title": title,
            "content": content,
            "note_type": note_type,
            "tags": tags or [self.project_name]
        })
        self.stats["notes_created"] += 1
        return result

    def get_stats(self) -> Dict[str, Any]:
        """获取统计信息"""
        duration = (datetime.now() - self.stats["session_start"]).total_seconds()

        # 获取笔记摘要
        try:
            note_summary = self.note_tool.run({"action": "summary"})
        except:
            note_summary = {}

        return {
            "session_info": {
                "session_id": self.session_id,
                "project": self.project_name,
                "duration_seconds": duration
            },
            "activity": {
                "commands_executed": self.stats["commands_executed"],
                "notes_created": self.stats["notes_created"],
                "issues_found": self.stats["issues_found"]
            },
            "notes": note_summary
        }

    def generate_report(self, save_to_file: bool = True) -> Dict[str, Any]:
        """生成会话报告"""
        report = self.get_stats()

        if save_to_file:
            report_file = f"maintainer_report_{self.session_id}.json"
            with open(report_file, 'w', encoding='utf-8') as f:
                json.dump(report, f, ensure_ascii=False, indent=2, default=str)
            report["report_file"] = report_file
            print(f"📄 报告已保存: {report_file}")

        return report


def main():
    """主函数 - 演示 CodebaseMaintainer 的使用（Agentic 版本）
    
    在这个版本中：
    - Agent 自主决定使用哪些工具
    - 不预定义工作流
    - Agent 根据需求灵活探索代码库
    """
    print("=" * 80)
    print("CodebaseMaintainer 演示（Agentic 版本）")
    print("=" * 80 + "\n")

    # 初始化助手
    maintainer = CodebaseMaintainer(
        project_name="my_flask_app",
        codebase_path="./my_flask_app",
        llm=HelloAgentsLLM()
    )

    # 探索代码库（Agent 自主决定如何探索）
    print("\n### 探索代码库（Agent 自主探索）###")
    response = maintainer.explore()

    # 分析代码质量（Agent 自主决定分析方法）
    print("\n### 分析代码质量（Agent 自主分析）###")
    response = maintainer.analyze()

    # 规划下一步（Agent 基于历史信息规划）
    print("\n### 规划下一步任务（Agent 自主规划）###")
    response = maintainer.plan_next_steps()

    # 生成报告
    print("\n### 生成会话报告 ###")
    report = maintainer.generate_report()
    print(json.dumps(report, indent=2, ensure_ascii=False))

    print("\n" + "=" * 80)
    print("演示完成!")
    print("=" * 80)


if __name__ == "__main__":
    main()
```

</details>

**9.6.4 完整使用示例**

现在让我们通过一个完整的使用场景，展示这个长程智能体的工作流程：

<details>
<summary>chapter9/06_three_day_workflow.py</summary>

```python
"""
CodebaseMaintainer 三天工作流演示

完整展示长程智能体在三天内的工作流程:
- 第一天: 探索代码库（Agent 自主探索）
- 第二天: 分析代码质量（Agent 自主分析）
- 第三天: 规划重构任务（Agent 自主规划）
- 一周后: 检查进度

"""

import os
# 配置嵌入模型（三选一）
# 方案一：TF-IDF（最简单，无需额外依赖）
# os.environ['EMBED_MODEL_TYPE'] = 'tfidf'
# os.environ['EMBED_MODEL_NAME'] = ''  # 重要：必须清空，否则会传递不兼容的参数
from dotenv import load_dotenv
load_dotenv()
# 方案二：本地Transformer（需要: pip install sentence-transformers 和 HF token）
# os.environ['EMBED_MODEL_TYPE'] = 'local'
# os.environ['EMBED_MODEL_NAME'] = 'sentence-transformers/all-MiniLM-L6-v2'
# os.environ['HF_TOKEN'] = 'your_hf_token_here'  # 或使用 huggingface-cli login
# 方案三：通义千问（需要API key）
# os.environ['EMBED_MODEL_TYPE'] = 'dashscope'
# os.environ['EMBED_MODEL_NAME'] = 'text-embedding-v3'
# os.environ['EMBED_API_KEY'] = 'your_api_key_here'

from hello_agents import HelloAgentsLLM
from datetime import datetime
import json
import time

# 导入 CodebaseMaintainer
import sys
sys.path.append('.')
from codebase_maintainer import CodebaseMaintainer


def day_1_exploration(maintainer):
    """第一天: 探索代码库（Agentic 方式）
    
    在这个阶段，我们只给 Agent 高层次的目标，
    Agent 会自主决定：
    - 使用哪些 shell 命令探索代码库
    - 查看哪些文件
    - 是否记录笔记
    """
    print("\n" + "=" * 80)
    print("第一天: 探索代码库（Agent 自主探索）")
    print("=" * 80 + "\n")

    # 1. 初步探索 - Agent 自主决定如何探索
    print("### 1. 初步探索项目结构 ###")
    print("💡 提示：Agent 会自主决定使用哪些命令（如 find, ls, cat）\n")
    response = maintainer.explore()
    print(f"\n助手总结:\n{response[:500]}...\n")

    # 2. 深入分析某个模块 - Agent 自主决定分析方法
    print("### 2. 分析数据处理模块 ###")
    print("💡 提示：Agent 会自主决定如何分析这个文件\n")
    response = maintainer.run("请查看 data_processor.py 文件，分析其代码设计")
    print(f"\n助手总结:\n{response[:500]}...\n")

    # 模拟时间流逝
    time.sleep(1)


def day_2_analysis(maintainer):
    """第二天: 分析代码质量（Agentic 方式）
    
    Agent 会自主决定：
    - 使用什么方法分析代码质量（grep TODO? 统计行数? 检查复杂度?）
    - 是否需要创建笔记记录问题
    - 如何组织分析结果
    """
    print("\n" + "=" * 80)
    print("第二天: 分析代码质量（Agent 自主分析）")
    print("=" * 80 + "\n")

    # 1. 整体质量分析 - Agent 自主决定分析方法
    print("### 1. 分析代码质量 ###")
    print("💡 提示：Agent 会自主决定如何分析（如 grep TODO, wc -l, 复杂度分析）\n")
    response = maintainer.analyze()
    print(f"\n助手总结:\n{response[:500]}...\n")

    # 2. 查看具体问题 - Agent 自主深入分析
    print("### 2. 分析 API 客户端代码 ###")
    print("💡 提示：Agent 会自主决定如何分析这个文件的质量\n")
    response = maintainer.run(
        "请分析 api_client.py 的代码质量，特别是错误处理部分，给出改进建议"
    )
    print(f"\n助手总结:\n{response[:500]}...\n")

    # 模拟时间流逝
    time.sleep(1)


def day_3_planning(maintainer):
    """第三天: 规划重构任务（Agentic 方式）
    
    Agent 会自主决定：
    - 回顾哪些历史笔记
    - 如何组织任务规划
    - 是否需要创建新的笔记
    - 如何安排优先级
    """
    print("\n" + "=" * 80)
    print("第三天: 规划重构任务（Agent 自主规划）")
    print("=" * 80 + "\n")

    # 1. 回顾进度 - Agent 自主查看历史笔记并规划
    print("### 1. 回顾当前进度并规划下一步 ###")
    print("💡 提示：Agent 会自主查看历史笔记，分析当前进度，并制定计划\n")
    response = maintainer.plan_next_steps()
    print(f"\n助手总结:\n{response[:500]}...\n")

    # 2. 询问 Agent 创建详细计划（Agent 会自主决定是否使用 NoteTool）
    print("### 2. 让 Agent 创建详细的重构计划 ###")
    print("💡 提示：Agent 会自主决定如何创建和组织重构计划\n")
    response = maintainer.run(
        "请基于我们的分析，创建一个详细的本周重构计划。"
        "计划应该包括：目标、具体任务清单、时间安排和风险。"
        "请使用 NoteTool 创建一个 task_state 类型的笔记来记录这个计划。"
    )
    print(f"\n助手总结:\n{response[:500]}...\n")

    # 模拟时间流逝
    time.sleep(1)


def week_later_review(maintainer):
    """一周后: 检查进度"""
    print("\n" + "=" * 80)
    print("一周后: 检查进度")
    print("=" * 80 + "\n")

    # 1. 查看笔记摘要
    print("### 1. 笔记摘要 ###")
    summary = maintainer.note_tool.run({"action": "summary"})
    print("📊 笔记摘要:")
    print(json.dumps(summary, indent=2, ensure_ascii=False))
    print()

    # 2. 生成完整报告
    print("### 2. 会话报告 ###")
    report = maintainer.generate_report()
    print("\n📄 会话报告:")
    print(json.dumps(report, indent=2, ensure_ascii=False))


def demonstrate_cross_session_continuity():
    """演示跨会话的连贯性"""
    print("\n" + "=" * 80)
    print("演示跨会话的连贯性")
    print("=" * 80 + "\n")

    # 第一次会话
    print("### 第一次会话 (session_1) ###")
    maintainer_1 = CodebaseMaintainer(
        project_name="demo_codebase",
        #实际使用的时候替换代码路径
        codebase_path="./codebase",
        llm=HelloAgentsLLM()
    )

    # 创建一些笔记
    maintainer_1.create_note(
        title="代码质量问题",
        content="发现多处 TODO 注释需要实现，特别是数据验证和错误处理部分",
        note_type="blocker",
        tags=["quality", "urgent"]
    )

    stats_1 = maintainer_1.get_stats()
    print(f"会话1统计: {stats_1['activity']}\n")

    # 模拟会话结束
    time.sleep(1)

    # 第二次会话 (新的会话ID,但笔记被保留)
    print("### 第二次会话 (session_2) ###")
    maintainer_2 = CodebaseMaintainer(
        project_name="demo_codebase",  # 同一个项目
        #实际使用的时候替换代码路径
        codebase_path="./codebase",
        llm=HelloAgentsLLM()
    )

    # 检索之前的笔记
    response = maintainer_2.run(
        "我们之前发现了什么代码质量问题？现在应该优先处理哪些？"
    )
    print(f"\n助手回答:\n{response[:300]}...\n")

    stats_2 = maintainer_2.get_stats()
    print(f"会话2统计: {stats_2['activity']}\n")

    # 展示笔记摘要
    summary = maintainer_2.note_tool.run({"action": "summary"})
    print("📊 跨会话笔记摘要:")
    print(json.dumps(summary, indent=2, ensure_ascii=False))


def demonstrate_tool_synergy():
    """演示三大工具的协同（Agentic 方式）
    
    在这个演示中：
    - 我们不再手动调用工具
    - 而是让 Agent 自主决定使用哪些工具
    - Agent 会根据任务自动协同使用多个工具
    """
    print("\n" + "=" * 80)
    print("演示三大工具的协同（Agent 自主协调）")
    print("=" * 80 + "\n")

    maintainer = CodebaseMaintainer(
        project_name="synergy_demo",
        #实际使用的时候替换代码路径
        codebase_path="./codebase",
        llm=HelloAgentsLLM()
    )

    # Agent 自主分析并记录
    print("### Agent 自主分析代码库中的 TODO 项 ###")
    print("💡 提示：Agent 会自主决定：\n")
    print("   1. 使用 TerminalTool 查找 TODO")
    print("   2. 使用 NoteTool 记录发现")
    print("   3. 使用 MemoryTool 记住关键信息\n")
    
    response = maintainer.run(
        "请分析代码库中的所有 TODO 项，并将发现记录到笔记中。"
        "然后告诉我应该优先实现哪些功能。"
    )
    print(f"助手回答:\n{response[:500]}...\n")

    # 展示统计信息
    stats = maintainer.get_stats()
    print("📊 工具使用统计:")
    print(f"  - 工具调用次数: {stats['activity']['tool_calls']}")
    print(f"  - 执行的命令: {stats['activity']['commands_executed']}")
    print(f"  - 创建的笔记: {stats['activity']['notes_created']}")


def main():
    """主函数"""
    print("=" * 80)
    print("CodebaseMaintainer 三天工作流演示（Agentic 版本）")
    print("=" * 80)
    
    print("\n✨ 核心特性：Agent 自主决策")
    print("💡 使用我们在 chapter9 创建的示例代码库")
    print("📁 代码库路径: ./codebase")
    print("📦 包含文件: data_processor.py, api_client.py, utils.py, models.py")
    print("\n🔧 Agent 可用工具：")
    print("   - TerminalTool: 执行 shell 命令")
    print("   - NoteTool: 创建和管理笔记")
    print("   - MemoryTool: 记忆管理")
    print("\n⚡ Agent 会自主决定：")
    print("   - 使用哪些工具")
    print("   - 执行什么命令")
    print("   - 如何组织信息\n")

    # 初始化助手
    maintainer = CodebaseMaintainer(
        project_name="demo_codebase",
        #实际使用的时候替换代码路径
        codebase_path="./codebase",
        llm=HelloAgentsLLM()
    )

    # 执行三天工作流
    day_1_exploration(maintainer)
    day_2_analysis(maintainer)
    day_3_planning(maintainer)
    week_later_review(maintainer)

    # 额外演示
    print("\n\n" + "=" * 80)
    print("额外演示")
    print("=" * 80)

    demonstrate_cross_session_continuity()
    demonstrate_tool_synergy()

    print("\n" + "=" * 80)
    print("完整演示结束!")
    print("=" * 80)


if __name__ == "__main__":
    main()

```

</details>

<details>
<summary>chapter9/06_three_day_workflow.py 的运行结果</summary>

```python
报了个gbk bug
```

</details>


**9.6.5 运行效果分析**

通过这个完整的案例，我们可以看到长程智能体的几个关键特性。

首先是跨会话的连贯性，智能体通过 NoteTool 保持了跨多天、多个会话的任务连贯性，第一天探索的问题在第二天分析时被自动考虑，第三天规划时能够综合前两天的所有发现，一周后检查时完整的历史都被保留。

其次是智能的上下文管理，ContextBuilder 确保每次对话都有高质量的上下文，自动汇集相关笔记(特别是 blocker 类型)，根据对话模式动态调整预处理策略，在 token 预算内选择最相关的信息。

第三个特性是即时的文件系统访问，TerminalTool 支持灵活的代码探索，无需预先索引整个代码库，可以即时查看具体文件内容，支持复杂的文本处理(grep、awk等)。第四是自动化的知识管理，系统自动化地管理发现的知识，发现问题时自动创建 blocker 笔记，讨论计划时自动创建 action 笔记，关键信息自动存储到记忆系统。最后是人机协作，这个系统支持灵活的人机协作模式，智能体可以自动化地完成探索和分析，人类可以通过笔记系统进行干预和指导，支持手动创建详细的计划笔记。

这个基础框架可以进一步扩展，比如集成 RAGTool 为代码库建立向量索引结合语义检索，拆分为专门的探索者、分析者、规划者实现多智能体协作，集成测试工具自动验证重构结果，通过 TerminalTool 执行 git 命令追踪代码变更，或者使用 Gradio/Streamlit 构建可视化界面。

### 9.7 **本章总结**

在本章中，我们深入探讨了上下文工程的理论基础和工程实践：

**理论层面**

1. **上下文工程的本质**：从"提示工程"到"上下文工程"的演进，核心是管理有限的注意力预算
2. **上下文腐蚀**：理解长上下文带来的性能下降，认识到上下文是稀缺资源
3. **三大策略**：压缩整合、结构化笔记、子代理架构

**工程实践**

1. **ContextBuilder**：实现了 GSSC 流水线，提供统一的上下文管理接口
2. **NoteTool**：Markdown+YAML 的混合格式，支持结构化的长期记忆
3. **TerminalTool**：安全的命令行工具，支持即时的文件系统访问
4. **长程智能体**：整合三大工具，构建了跨会话的代码库维护助手

**核心收获**

* **分层设计**：即时访问(TerminalTool) + 会话记忆(MemoryTool) + 持久笔记(NoteTool)
* **智能筛选**：基于相关性和新近性的评分机制
* **安全第一**：多层安全机制确保系统稳定
* **人机协作**：自动化与可控性的平衡

通过这一章的学习，您不仅掌握了上下文工程的核心技术，更重要的是理解了如何构建能够在长时间跨度内保持连贯性和有效性的智能体系统。这些技能将成为您构建生产级智能体应用的重要基础。

在下一章中，我们将探讨智能体通信协议，学习如何让智能体与外部世界进行更广泛的交互。