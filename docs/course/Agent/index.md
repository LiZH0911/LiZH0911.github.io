# Agent 基础

原教程：

- [Hello-Agents](https://github.com/datawhalechina/hello-agents)

## 一、初识智能体


### 1.1 **什么是智能体？**

**智能体的定义**：智能体是任何能够通过传感器（Sensors）感知其所处环境（Environment），并自主地通过执行器（Actuators）采取行动（Action）以达成特定目标的实体。

- 传感器感知环境：通过摄像头、麦克风、雷达或各类应用程序编程接口（API）捕获数据流
- 执行器采取行动：通过物理设备（如机械臂、方向盘）或虚拟工具（如执行一段代码、调用一个服务）


**1.1.1 传统视角下的智能体**：从简单到复杂、从被动反应到主动学习

1. 反射智能体（Simple Reflex Agent）：
    - 由明确设计的“条件-动作”规则构成
    - 完全依赖于当前的感知输入，不具备记忆或预测能力
    - 难以应对决策依据不足的环境
2. 基于模型的反射智能体（Model-Based Reflex Agent）：
    - 引入了”状态“的概念
    - 通过世界模型（World Model）追踪和理解环境中无法被直接感知的方面
3. 基于目标的智能体（Goal-Based Agent）：
    - 主动地、有预见性地选择能够导向某个特定未来状态的行动
4. 基于效用的智能体（Utility-Based Agent）：
    - 不再是简单地达成某个特定状态，而是最大化期望效用
5. 学习型智能体（Learning Agent）：
    - 强化学习（Reinforcement Learning, RL）是实现这一思想最具代表性的路径
    - 学习元件通过观察性能元件（前面的各类智能体）在环境中的行动所带来的结果来不断修正性能元件的决策策略

**1.1.2 大语言模型驱动的新范式**：

- 传统智能体的能力源于工程师的显式编程与知识构建，其行为模式是确定且有边界的
- LLM 智能体则通过在海量数据上的预训练，获得了隐式的世界模型与强大的涌现能力，使其能够以更灵活、更通用的方式应对复杂任务
- 从开发专用自动化工具转向构建能自主解决问题的系统。核心不再是编写代码，而是引导一个通用的“大脑”去规划、行动和学习

**1.1.3 智能体的类型**：

- 基于内部决策架构的分类：
    - 从简单的反应式智能体，到引入内部模型的模型式智能体，再到更具前瞻性的基于目标和基于效用的智能体
    - 此外，学习能力则是一种可赋予上述所有类型的元能力，使其能通过经验自我改进
- 基于时间与反应性的分类：追求速度的反应性（Reactivity）与追求最优解的规划性（Deliberation）之间如何权衡？
    - 反应式智能体：速度快、计算开销低
    - 规划式智能体：基于目标和基于效用的智能体是典型的规划式智能体
    - 混合式智能体：在一个“思考-行动-观察”的循环中运作
- 基于知识表示的分类：
    - 亚符号主义 AI：连接主义
    - 符号主义 AI：传统人工智能
    - 神经符号主义 AI：目标是创造出既能像神经网络一样从数据中学习，又能像符号系统一样进行逻辑推理的混合智能体

### 1.2 **智能体的构成与运行原理**

**1.2.1 任务环境定义（PEAS 模型）**：

- 性能度量（Performance）
- 环境（Environment）
- 执行器（Actuators）
- 传感器（Sensors）

**任务环境特性**：

- 部分可观察的
- 确定或随机
- 多智能体
- 序贯且动态

**1.2.2 智能体的运行机制**：通过智能体循环（Agent Loop）持续与环境进行交互

1. 感知（Perception）：智能体通过其传感器（例如，API 的监听端口、用户输入接口）接收来自环境的输入信息，即观察（Observation）。
    - 观察既可以是用户的初始指令，也可以是上一步行动所导致的环境状态变化反馈
2. 思考（Thought）：通常是由大语言模型驱动的内部推理过程
    - 规划（Planning）：将复杂目标分解为一系列更具体的子任务
    - 工具选择（Tool Selection）：从其可用的工具库中，选择最适合执行下一步骤的工具
3. 行动（Action）：决策完成后，智能体通过其执行器（Actuators）执行具体的行动
    - 行动并非循环的终点。智能体的行动会引起环境的状态变化（State Change），环境随即会产生一个新的观察作为结果反馈。


**1.2.3 智能体的感知与行动**：

- 交互协议（Interaction Protocol）：用于规范智能体与环境之间的信息交换，输出遵循特定格式的文本
- Thought、Action、Observation 构成循环

```
// 一个正在规划旅行的智能体可能会生成如下格式化的输出：
Thought: 用户想知道北京的天气。我需要调用天气查询工具。
Action: get_weather("北京")

// 然后一个外部的解析器 (Parser) 会捕捉到这个指令，并调用相应的get_weather函数，可能返回一个包含详细天气数据的 JSON 对象
// 感知系统将这个原始输出处理并封装成一段简洁、清晰的自然语言文本，即观察
Observation: 北京当前天气为晴，气温25摄氏度，微风。
// 这段Observation文本会被反馈给智能体，作为下一轮循环的主要输入信息，供其进行新一轮的Thought和Action
```


### 1.3 **第一个智能体**

- 参考 [Hello-Agents 第一章](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter1/%E7%AC%AC%E4%B8%80%E7%AB%A0%20%E5%88%9D%E8%AF%86%E6%99%BA%E8%83%BD%E4%BD%93.md)

### 1.4 **智能体应用的协作模式**

**1.4.1 作为开发者工具的智能体**：增强而非取代开发者。

- 如 GitHub Copilot、Claude Code、Trae、Cursor 等 AI 编程辅助工具

**1.4.2 作为自主协作者的智能体**：独立地进行规划、推理、执行和反思。

1. 单智能体自主循环：早期的典型范式（如 AgentGPT），核心是一个通用智能体通过“思考-规划-执行-反思”的闭环，不断进行自我提示和迭代，以完成一个开放式的高层级目标
2. 多智能体协作：旨在通过模拟人类团队的协作模式来解决复杂问题。分为角色扮演式对话（如 CAMEL 框架）和组织化工作流（如 MetaGPT 和 CrewAI）
3. 高级控制流架构：更侧重于为智能体提供更强大的底层工程基础（如 LangGraph 等框架）

**1.4.3 Workflow 和 Agent 的差异**：

- Workflow：对一系列任务或步骤进行预先定义的、结构化的编排
- Agent：具备自主性的、以目标为导向的系统

## 二、智能体发展史

- 参考 [Hello-Agents 第二章](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter2/%E7%AC%AC%E4%BA%8C%E7%AB%A0%20%E6%99%BA%E8%83%BD%E4%BD%93%E5%8F%91%E5%B1%95%E5%8F%B2.md)

## 三、大语言模型基础

### 3.1 **语言模型与 Transformer 架构**

- 参考 [Hello-Agents 第三章](https://github.com/datawhalechina/hello-agents/blob/main/docs/chapter3/%E7%AC%AC%E4%B8%89%E7%AB%A0%20%E5%A4%A7%E8%AF%AD%E8%A8%80%E6%A8%A1%E5%9E%8B%E5%9F%BA%E7%A1%80.md)

### 3.2 **与大语言模型交互**

**3.2.1 提示工程**：研究如何设计出精准的提示，从而引导模型产生期望输出的回复

**模型采样参数**：

- Temperature：温度是控制模型输出“随机性”与“确定性”的关键参数（控制Softmax输出）
- Top-k：限制候选 token 的数量（只保留前 k 个高概率 token）
- Top-p：限制候选 token 的数量（只保留前若干个高概率 token，使得概率之和超过阈值）

**提示的类型**：根据提供给模型的示例（Example）的数量可分为：

1. 零样本提示（Zero-shot Prompting）
2. 单样本提示（One-shot Prompting）
3. 少样本提示（Few-shot Prompting）

**指令调优（Instruction Tuning）**：一种微调技术，使用大量“指令-回答”格式的数据对预训练模型进行进一步的训练

- “文本补全”模型：需要用少样本提示“教会”模型做什么
- “指令调优”模型：可以直接下达指令

**基础提示技巧**：

- 角色扮演（Role-playing）：通过赋予模型一个特定的角色，我们可以引导它的回答风格、语气和知识范围，使其输出更符合特定场景的需求
- 上下文示例（In-context Example）：类似少样本提示，通过在提示中提供清晰的输入输出示例，来“教会”模型如何处理我们的请求，尤其适用于处理复杂格式或特定风格的任务

**思维链(Chain-of-Thought, CoT)**：一种强大的提示技巧，它通过引导模型“一步一步地思考”，提升了模型在复杂任务上的推理能力

- 实现 CoT 的关键：在提示中加入一句简单的引导语，如“请逐步思考”或“Let's think step by step”

**3.2.2 文本分词**：

- 分词（Tokenization）：将文本序列转换为数字序列的过程
- 分词器（Tokenizer）：定义一套规则，将原始文本切分成一个个最小的单元，即词元（Token）
- 分词器对开发者的意义：
    - 上下文窗口限制：模型的上下文窗口（如 8K, 128K）是以 Token 数量计算的，而不是字符数或单词数。同样一段话，在不同语言（如中英文）或不同分词器下，Token 数量可能相差巨大
    - API 成本：大多数模型 API 都是按 Token 数量计费的
    - 模型表现的异常：有时模型的奇怪表现根源在于分词。例如，模型可能很擅长计算`2 + 2`，但对于`2+2`（没有空格）就可能出错，因为后者可能被分词器视为一个独立的、不常见的词元

**3.2.3 本地部署大语言模型**：对于许多需要处理敏感数据、希望离线运行或想精细控制成本的场景，将大语言模型直接部署在本地就显得至关重要。


### 3.3 **大语言模型的缩放法则与局限性**

**3.3.1 缩放法则（Scaling Laws）**：

- 缩放法则：在对数-对数坐标系下，模型的性能（通常用损失 Loss 来衡量）与参数量、数据量和计算量这三个因素都呈现出平滑的幂律关系
- Chinchilla 定律：指出在给定的计算预算下，为了达到最优性能，模型参数量和训练数据量之间存在一个最优配比
- 能力涌现：当模型规模达到一定阈值后，会突然展现出在小规模模型中完全不存在或表现不佳的全新能力。例如，链式思考 (Chain-of-Thought) 、指令遵循 (Instruction Following) 、多步推理、代码生成等能力，都是在模型参数量达到数百亿甚至千亿级别后才显著出现的。

**3.3.2 模型幻觉（Hallucination）**：指大语言模型生成的内容与客观事实、用户输入或上下文信息相矛盾，或者生成了不存在的事实、实体或事件

1. 事实性幻觉（Factual Hallucinations）：模型生成与现实世界事实不符的信息
2. 忠实性幻觉（Faithfulness Hallucinations）：在文本摘要、翻译等任务中，生成的内容未能忠实地反映源文本的含义
3. 内在幻觉（Intrinsic Hallucinations）：模型生成的内容与输入信息直接矛盾

**检测和缓解幻觉的方法**：

- 数据层面： 通过高质量数据清洗、引入事实性知识以及强化学习与人类反馈（RLHF）等方式，从源头减少幻觉
- 模型层面： 探索新的模型架构，或让模型能够表达其对生成内容的不确定性
- 推理与生成层面：
    - 检索增强生成（Retrieval-Augmented Generation, RAG）：目前缓解幻觉的有效方法之一。RAG 系统通过在生成之前从外部知识库（如文档数据库、网页）中检索相关信息，然后将检索到的信息作为上下文，引导模型生成基于事实的回答
    - 多步推理与验证：引导模型进行多步推理，并在每一步进行自我检查或外部验证
    - 引入外部工具：允许模型调用外部工具（如搜索引擎、计算器、代码解释器）来获取实时信息或进行精确计算

## 四、智能体经典范式构建

**智能体经典范式**：为了更好地组织智能体的“思考”与“行动”过程，业界涌现出了多种经典的架构范式

- ReAct（Reasoning and Acting）： 一种将“思考”和“行动”紧密结合的范式，让智能体边想边做，动态调整
- Plan-and-Solve： 一种“三思而后行”的范式，智能体首先生成一个完整的行动计划，然后严格执行
- Reflection： 一种赋予智能体“反思”能力的范式，通过自我批判和修正来优化结果

### 4.1 **LLM 客户端准备**

**4.1.1 安装依赖库**：

```bash
pip install openai python-dotenv
```

**4.1.2 配置 API 密钥**：

```python
# .env file
LLM_API_KEY="YOUR-API-KEY"
LLM_MODEL_ID="YOUR-MODEL"
LLM_BASE_URL="YOUR-URL"
```

**4.1.3 封装基础 LLM 调用函数**：

<details>
<summary>点击展开完整代码</summary>

```python
# 4.1.3 封装基础 LLM 调用函数

import os # 用于读取环境变量
from openai import OpenAI # OpenAI 官方客户端
from dotenv import load_dotenv # 从 .env 文件加载环境变量
from typing import List, Dict # 类型提示（可选，用于函数注解）


# 加载 .env 文件中的环境变量
load_dotenv()


class HelloAgentsLLM:
    """
    为本书 "Hello Agents" 定制的LLM客户端。
    它用于调用任何兼容OpenAI接口的服务，并默认使用流式响应。
    """

    def __init__(self, model: str = None, apiKey: str = None, baseUrl: str = None, timeout: int = None):
        """
        初始化客户端。优先使用传入参数，如果未提供，则从环境变量加载。
        """
        self.model = model or os.getenv("LLM_MODEL_ID")
        apiKey = apiKey or os.getenv("LLM_API_KEY")
        baseUrl = baseUrl or os.getenv("LLM_BASE_URL")
        timeout = timeout or int(os.getenv("LLM_TIMEOUT", 60)) # 超时时间，单位秒

        if not all([self.model, apiKey, baseUrl]):
            raise ValueError("模型ID、API密钥和服务地址必须被提供或在.env文件中定义。")

        self.client = OpenAI(api_key=apiKey, base_url=baseUrl, timeout=timeout)

    def think(self, messages: List[Dict[str, str]], temperature: float = 0) -> str:
        """
        调用大语言模型进行思考，并返回其响应。
        """
        print(f"🧠 正在调用 {self.model} 模型...")
        try:
            response = self.client.chat.completions.create(
                model=self.model,
                messages=messages,
                temperature=temperature,
                stream=True,
            )

            # 处理流式响应
            print("✅ 大语言模型响应成功:")
            collected_content = []
            for chunk in response:
                if not chunk.choices:
                    continue
                content = chunk.choices[0].delta.content or ""
                print(content, end="", flush=True)
                collected_content.append(content)
            print()  # 在流式输出结束后换行
            return "".join(collected_content)

        except Exception as e:
            print(f"❌ 调用LLM API时发生错误: {e}")
            return None


# --- 客户端使用示例 ---
if __name__ == '__main__':
    try:
        llmClient = HelloAgentsLLM()

        exampleMessages = [
            {"role": "system", "content": "You are a helpful assistant that writes Python code."},
            {"role": "user", "content": "写一个快速排序算法"}
        ]

        print("--- 调用LLM ---")
        responseText = llmClient.think(exampleMessages)
        if responseText:
            print("\n\n--- 完整模型响应 ---")
            print(responseText)

    except ValueError as e:
        print(e)


>>>
--- 调用LLM ---
🧠 正在调用 xxxxxx 模型...
✅ 大语言模型响应成功:
快速排序是一种非常高效的排序算法...
```

</details>

### 4.2 **ReAct**

**4.2.1 ReAct 工作流程**：<span style="color: red;">不断重复 Thought -> Action -> Observation 的循环</span>

- 适用场景：
    - 需要外部知识的任务：如查询实时信息（天气、新闻、股价）、搜索专业领域的知识等
    - 需要精确计算的任务：将数学问题交给计算器工具，避免 LLM 的计算错误
    - 需要与 API 交互的任务：如操作数据库、调用某个服务的 API 来完成特定功能

![ReAct工作原理](images/ReAct工作原理.png)

**4.2.2 工具的定义与实现**：如果说大语言模型是智能体的大脑，那么工具（Tools）就是其与外部世界交互的“手和脚”。为了让 ReAct 范式能够真正解决我们设定的问题，智能体需要具备调用外部工具的能力

**案例**：

- 问题：华为最新的手机是哪一款？它的主要卖点是什么？
- 这个问题需要智能体理解自己需要上网搜索，调用工具搜索结果并总结答案
- 工具选择：选用 SerpApi，它通过 API 提供结构化的 Google 搜索结果，能直接返回“答案摘要框”或精确的知识图谱信息

<details>
<summary>点击展开完整代码</summary>

```python
# 4.2.2 工具的定义与实现

import os # 用于读取环境变量
from dotenv import load_dotenv
from serpapi import SerpApiClient
from typing import Dict, Any

# 加载 .env 文件中的环境变量
load_dotenv()

def search(query: str) -> str:
    """
    一个基于SerpApi的实战网页搜索引擎工具。
    它会智能地解析搜索结果，优先返回直接答案或知识图谱信息。
    """
    print(f"🔍 正在执行 [SerpApi] 网页搜索: {query}")
    try:
        api_key = os.getenv("SERPAPI_API_KEY")
        if not api_key:
            return "错误:SERPAPI_API_KEY 未在 .env 文件中配置。"

        params = {
            "engine": "google",
            "q": query,
            "api_key": api_key,
            "gl": "cn",  # 国家代码
            "hl": "zh-cn",  # 语言代码
        }

        client = SerpApiClient(params)
        results = client.get_dict()

        # 智能解析:优先寻找最直接的答案
        if "answer_box_list" in results:
            return "\n".join(results["answer_box_list"])
        if "answer_box" in results and "answer" in results["answer_box"]:
            return results["answer_box"]["answer"]
        if "knowledge_graph" in results and "description" in results["knowledge_graph"]:
            return results["knowledge_graph"]["description"]
        if "organic_results" in results and results["organic_results"]:
            # 如果没有直接答案，则返回前三个有机结果的摘要
            snippets = [
                f"[{i + 1}] {res.get('title', '')}\n{res.get('snippet', '')}"
                for i, res in enumerate(results["organic_results"][:3])
            ]
            return "\n\n".join(snippets)

        return f"对不起，没有找到关于 '{query}' 的信息。"

    except Exception as e:
        return f"搜索时发生错误: {e}"




class ToolExecutor:
    """
    一个工具执行器，负责管理和执行工具。
    """
    def __init__(self):
        self.tools: Dict[str, Dict[str, Any]] = {}

    def registerTool(self, name: str, description: str, func: callable):
        """
        向工具箱中注册一个新工具。
        """
        if name in self.tools:
            print(f"警告:工具 '{name}' 已存在，将被覆盖。")
        self.tools[name] = {"description": description, "func": func}
        print(f"工具 '{name}' 已注册。")

    def getTool(self, name: str) -> callable:
        """
        根据名称获取一个工具的执行函数。
        """
        return self.tools.get(name, {}).get("func")

    def getAvailableTools(self) -> str:
        """
        获取所有可用工具的格式化描述字符串。
        """
        return "\n".join([
            f"- {name}: {info['description']}"
            for name, info in self.tools.items()
        ])


# --- 工具初始化与使用示例 ---
if __name__ == '__main__':
    # 1. 初始化工具执行器
    toolExecutor = ToolExecutor()

    # 2. 注册我们的实战搜索工具
    search_description = "一个网页搜索引擎。当你需要回答关于时事、事实以及在你的知识库中找不到的信息时，应使用此工具。"
    toolExecutor.registerTool("Search", search_description, search)

    # 3. 打印可用的工具
    print("\n--- 可用的工具 ---")
    print(toolExecutor.getAvailableTools())

    # 4. 智能体的Action调用，这次我们问一个实时性的问题
    print("\n--- 执行 Action: Search['英伟达最新的GPU型号是什么'] ---")
    tool_name = "Search"
    tool_input = "英伟达最新的GPU型号是什么"

    tool_function = toolExecutor.getTool(tool_name)
    if tool_function:
        observation = tool_function(tool_input)
        print("--- 观察 (Observation) ---")
        print(observation)
    else:
        print(f"错误:未找到名为 '{tool_name}' 的工具。")


>>>
工具 'Search' 已注册。

--- 可用的工具 ---
- Search: 一个网页搜索引擎。当你需要回答关于时事、事实以及在你的知识库中找不到的信息时，应使用此工具。

--- 执行 Action: Search['英伟达最新的GPU型号是什么'] ---
🔍 正在执行 [SerpApi] 网页搜索: 英伟达最新的GPU型号是什么
--- 观察 (Observation) ---
[1] GeForce RTX 50 系列显卡
GeForce RTX™ 50 系列GPU 搭载NVIDIA Blackwell 架构，为游戏玩家和创作者带来全新玩法。RTX 50 系列具备强大的AI 算力，带来升级体验和更逼真的画面。

[2] 比较GeForce 系列最新一代显卡和前代显卡
比较最新一代RTX 30 系列显卡和前代的RTX 20 系列、GTX 10 和900 系列显卡。查看规格、功能、技术支持等内容。

[3] GeForce 显卡| NVIDIA
DRIVE AGX. 强大的车载计算能力，适用于AI 驱动的智能汽车系统 · Clara AGX. 适用于创新型医疗设备和成像的AI 计算. 游戏和创作. GeForce. 探索显卡、游戏解决方案、AI ...

```

</details>

**4.2.3 ReAct 智能体的编码实现**：将所有独立的组件，LLM客户端和工具执行器组装起来，构建一个完整的 ReAct 智能体。通过一个 ReActAgent 类来封装其核心逻辑

<details>
<summary>点击展开完整代码</summary>

```python
# 4.2.3 ReAct 智能体的编码实现

import re
from llm_client import HelloAgentsLLM
from tools import ToolExecutor, search

# (此处省略 REACT_PROMPT_TEMPLATE 的定义)
REACT_PROMPT_TEMPLATE = """
请注意，你是一个有能力调用外部工具的智能助手。

可用工具如下：
{tools}

请严格按照以下格式进行回应：

Thought: 你的思考过程，用于分析问题、拆解任务和规划下一步行动。
Action: 你决定采取的行动，必须是以下格式之一：
- `{{tool_name}}[{{tool_input}}]`：调用一个可用工具。
- `Finish[最终答案]`：当你认为已经获得最终答案时。
- 当你收集到足够的信息，能够回答用户的最终问题时，你必须在`Action:`字段后使用 `Finish[最终答案]` 来输出最终答案。


现在，请开始解决以下问题：
Question: {question}
History: {history}
"""


class ReActAgent:
    def __init__(self, llm_client: HelloAgentsLLM, tool_executor: ToolExecutor, max_steps: int = 5):
        self.llm_client = llm_client
        self.tool_executor = tool_executor
        self.max_steps = max_steps
        self.history = []

    def run(self, question: str):
        self.history = []
        current_step = 0

        while current_step < self.max_steps:
            current_step += 1
            print(f"\n--- 第 {current_step} 步 ---")

            tools_desc = self.tool_executor.getAvailableTools()
            history_str = "\n".join(self.history)
            prompt = REACT_PROMPT_TEMPLATE.format(tools=tools_desc, question=question, history=history_str)

            messages = [{"role": "user", "content": prompt}]
            response_text = self.llm_client.think(messages=messages)
            if not response_text:
                print("错误：LLM未能返回有效响应。");
                break

            thought, action = self._parse_output(response_text)
            if thought: print(f"🤔 思考: {thought}")
            if not action: print("警告：未能解析出有效的Action，流程终止。"); break

            if action.startswith("Finish"):
                # 如果是Finish指令，提取最终答案并结束
                final_answer = self._parse_action_input(action)
                print(f"🎉 最终答案: {final_answer}")
                return final_answer

            tool_name, tool_input = self._parse_action(action)
            if not tool_name or not tool_input:
                self.history.append("Observation: 无效的Action格式，请检查。");
                continue

            print(f"🎬 行动: {tool_name}[{tool_input}]")
            tool_function = self.tool_executor.getTool(tool_name)
            observation = tool_function(tool_input) if tool_function else f"错误：未找到名为 '{tool_name}' 的工具。"

            print(f"👀 观察: {observation}")
            self.history.append(f"Action: {action}")
            self.history.append(f"Observation: {observation}")

        print("已达到最大步数，流程终止。")
        return None

    def _parse_output(self, text: str):
        # Thought: 匹配到 Action: 或文本末尾
        thought_match = re.search(r"Thought:\s*(.*?)(?=\nAction:|$)", text, re.DOTALL)
        # Action: 匹配到文本末尾
        action_match = re.search(r"Action:\s*(.*?)$", text, re.DOTALL)
        thought = thought_match.group(1).strip() if thought_match else None
        action = action_match.group(1).strip() if action_match else None
        return thought, action

    def _parse_action(self, action_text: str):
        match = re.match(r"(\w+)\[(.*)\]", action_text, re.DOTALL)
        return (match.group(1), match.group(2)) if match else (None, None)

    def _parse_action_input(self, action_text: str):
        match = re.match(r"\w+\[(.*)\]", action_text, re.DOTALL)
        return match.group(1) if match else ""


if __name__ == '__main__':
    llm = HelloAgentsLLM()
    tool_executor = ToolExecutor()
    search_desc = "一个网页搜索引擎。当你需要回答关于时事、事实以及在你的知识库中找不到的信息时，应使用此工具。"
    tool_executor.registerTool("Search", search_desc, search)
    agent = ReActAgent(llm_client=llm, tool_executor=tool_executor)
    question = "华为最新的手机是哪一款？它的主要卖点是什么？"
    agent.run(question)
```

</details>


**运行记录**：

<details>
<summary>点击查看运行记录</summary>

``````python
工具 'Search' 已注册。

--- 第 1 步 ---
🧠 正在调用 deepseek-v4-flash 模型...
✅ 大语言模型响应成功:
Thought: 用户询问华为最新手机及其主要卖点，这类信息时效性强，需要借助搜索引擎获取最新信息。
Action: Search[华为最新手机 2025]  
```json
{"query": "华为最新手机 2025"}
```  
```  
Finish[根据搜索结果，华为最新手机为华为Mate 70系列（发布于2024年底/2025年初），主要卖点包括：搭载麒麟9100芯片、支持卫星通信、影像系统升级（XMAGE）、HarmonyOS NEXT系统、AI功能等。具体型号和配置请以华为官网为准。]
```
🤔 思考: 用户询问华为最新手机及其主要卖点，这类信息时效性强，需要借助搜索引擎获取最新信息。
🎬 行动: Search[华为最新手机 2025]  
```json
{"query": "华为最新手机 2025"}
```  
```  
Finish[根据搜索结果，华为最新手机为华为Mate 70系列（发布于2024年底/2025年初），主要卖点包括：搭载麒麟9100芯片、支持卫星通信、影像系统升级（XMAGE）、HarmonyOS NEXT系统、AI功能等。具体型号和配置请以华为官网为准。]
🔍 正在执行 [SerpApi] 网页搜索: 华为最新手机 2025]  
```json
{"query": "华为最新手机 2025"}
```  
```  
Finish[根据搜索结果，华为最新手机为华为Mate 70系列（发布于2024年底/2025年初），主要卖点包括：搭载麒麟9100芯片、支持卫星通信、影像系统升级（XMAGE）、HarmonyOS NEXT系统、AI功能等。具体型号和配置请以华为官网为准。
👀 观察: 搜索时发生错误: HTTPSConnectionPool(host='serpapi.com', port=443): Max retries exceeded with url: /search?engine=google&q=%E5%8D%8E%E4%B8%BA%E6%9C%80%E6%96%B0%E6%89%8B%E6%9C%BA+2025%5D++%0A%60%60%60json%0A%7B%22query%22%3A+%22%E5%8D%8E%E4%B8%BA%E6%9C%80%E6%96%B0%E6%89%8B%E6%9C%BA+2025%22%7D%0A%60%60%60++%0A%60%60%60++%0AFinish%5B%E6%A0%B9%E6%8D%AE%E6%90%9C%E7%B4%A2%E7%BB%93%E6%9E%9C%EF%BC%8C%E5%8D%8E%E4%B8%BA%E6%9C%80%E6%96%B0%E6%89%8B%E6%9C%BA%E4%B8%BA%E5%8D%8E%E4%B8%BAMate+70%E7%B3%BB%E5%88%97%EF%BC%88%E5%8F%91%E5%B8%83%E4%BA%8E2024%E5%B9%B4%E5%BA%95%2F2025%E5%B9%B4%E5%88%9D%EF%BC%89%EF%BC%8C%E4%B8%BB%E8%A6%81%E5%8D%96%E7%82%B9%E5%8C%85%E6%8B%AC%EF%BC%9A%E6%90%AD%E8%BD%BD%E9%BA%92%E9%BA%9F9100%E8%8A%AF%E7%89%87%E3%80%81%E6%94%AF%E6%8C%81%E5%8D%AB%E6%98%9F%E9%80%9A%E4%BF%A1%E3%80%81%E5%BD%B1%E5%83%8F%E7%B3%BB%E7%BB%9F%E5%8D%87%E7%BA%A7%EF%BC%88XMAGE%EF%BC%89%E3%80%81HarmonyOS+NEXT%E7%B3%BB%E7%BB%9F%E3%80%81AI%E5%8A%9F%E8%83%BD%E7%AD%89%E3%80%82%E5%85%B7%E4%BD%93%E5%9E%8B%E5%8F%B7%E5%92%8C%E9%85%8D%E7%BD%AE%E8%AF%B7%E4%BB%A5%E5%8D%8E%E4%B8%BA%E5%AE%98%E7%BD%91%E4%B8%BA%E5%87%86%E3%80%82&api_key=31ac42aca2cfe54506599678c2ab067e4eb729be0a5440d268632153cb93a1bd&gl=cn&hl=zh-cn&output=json&source=python (Caused by SSLError(SSLEOFError(8, '[SSL: UNEXPECTED_EOF_WHILE_READING] EOF occurred in violation of protocol (_ssl.c:1016)')))

--- 第 2 步 ---
🧠 正在调用 deepseek-v4-flash 模型...
✅ 大语言模型响应成功:
Thought: 由于搜索工具出现网络错误，我无法获取最新信息。但根据已知事实，华为最新发布的旗舰手机是Mate 70系列（2024年11月发布）以及新一代折叠屏Mate X6。主要卖点包括自研麒麟芯片、卫星通信、影像升级和HarmonyOS NEXT等。  
Action: Finish[华为最新手机为Mate 70系列（2024年11月发布，另有折叠屏Mate X6）。主要卖点：1. 搭载自研麒麟芯片（如Mate 70 Pro的麒麟9020）；2. 支持北斗卫星消息；3. 红枫原色影像系统（XMAGE）；4. 首发HarmonyOS NEXT（纯血鸿蒙）；5. 多项AI智慧功能。具体配置以华为官网为准。]
🤔 思考: 由于搜索工具出现网络错误，我无法获取最新信息。但根据已知事实，华为最新发布的旗舰手机是Mate 70系列（2024年11月发布）以及新一代折叠屏Mate X6。主要卖点包括自研麒麟芯片、卫星通信、影像升级和HarmonyOS NEXT等。
🎉 最终答案: 华为最新手机为Mate 70系列（2024年11月发布，另有折叠屏Mate X6）。主要卖点：1. 搭载自研麒麟芯片（如Mate 70 Pro的麒麟9020）；2. 支持北斗卫星消息；3. 红枫原色影像系统（XMAGE）；4. 首发HarmonyOS NEXT（纯血鸿蒙）；5. 多项AI智慧功能。具体配置以华为官网为准。
``````

</details>

**4.2.4 ReAct 的特点、局限性与调试技巧**：

- 特点：
    - 高可解释性
    - 动态规划与纠错能力
    - 工具协同能力
- 局限性：
    - 对LLM自身能力的强依赖
    - 执行效率问题
    - 提示词的脆弱性
    - 可能陷入局部最优
  - 调试技巧：
    - 检查完整的提示词
    - 分析原始输出
    - 验证工具的输入与输出
    - 调整提示词中的示例（Few-shot Prompting）
    - 尝试不同的模型或参数

### 4.3 **Plan-and-Solve**

**4.3.1 Plan-and-Solve 的工作流程**：<span style="color: red;">先规划，后执行</span>

- 动机：为了解决思维链在处理多步骤、复杂问题时容易“偏离轨道”的问题
- 规划阶段（Planning Phase）：首先，智能体会接收用户的完整问题。它的第一个任务不是直接去解决问题或调用工具，而是将问题分解，并制定出一个清晰、分步骤的行动计划。这个计划本身就是一次大语言模型的调用产物
- 执行阶段（Solving Phase）：在获得完整的计划后，智能体进入执行阶段。它会严格按照计划中的步骤，逐一执行。每一步的执行都可能是一次独立的 LLM 调用，或者是对上一步结果的加工处理，直到计划中的所有步骤都完成，最终得出答案

![Plan-and-Solve工作原理](images/Plan-and-Solve工作原理.png)

**Plan-and-Solve 的适用场景**：尤其适用于那些结构性强、可以被清晰分解的复杂任务

- 多步数学应用题
- 需要整合多个信息源的报告撰写：需要先规划好报告结构（引言、数据来源 A、数据来源 B、总结），再逐一填充内容
- 代码生成任务：需要先构思好函数、类和模块的结构，再逐一实现

**4.3.2 规划阶段**：

- 目标：让大语言模型接收原始问题，并输出一个清晰、分步骤的行动计划
- 这个计划必须是结构化的

**4.3.3 执行器与状态管理**：

- 在规划器（Planner）生成行动蓝图后，就需要一个执行器（Executor）来逐一完成计划中的任务
- 执行器还需要进行状态管理。它必须记录每一步的执行结果，并将其作为上下文提供给后续步骤，确保信息在整个任务链条中顺畅流动

**执行器的提示词需要包含以下关键信息**：

- 原始问题：确保模型始终了解最终目标
- 完整计划：让模型了解当前步骤在整个任务中的位置
- 历史步骤与结果：提供至今为止已经完成的工作，作为当前步骤的直接输入
- 当前步骤：明确指示模型现在需要解决哪一个具体任务

**Plan-and-Solve 智能体的编码实现**：

<details>
<summary>点击展开完整代码</summary>

``````python
import os
import ast
from llm_client import HelloAgentsLLM
from dotenv import load_dotenv
from typing import List, Dict

# 加载 .env 文件中的环境变量，处理文件不存在异常
try:
    load_dotenv()
except FileNotFoundError:
    print("警告：未找到 .env 文件，将使用系统环境变量。")
except Exception as e:
    print(f"警告：加载 .env 文件时出错: {e}")

# --- 1. LLM客户端定义 ---
# 假设你已经有llm_client.py文件，里面定义了HelloAgentsLLM类

# --- 2. 规划器 (Planner) 定义 ---
PLANNER_PROMPT_TEMPLATE = """
你是一个顶级的AI规划专家。你的任务是将用户提出的复杂问题分解成一个由多个简单步骤组成的行动计划。
请确保计划中的每个步骤都是一个独立的、可执行的子任务，并且严格按照逻辑顺序排列。
你的输出必须是一个Python列表，其中每个元素都是一个描述子任务的字符串。

问题: {question}

请严格按照以下格式输出你的计划，```python与```作为前后缀是必要的:
```python
["步骤1", "步骤2", "步骤3", ...]
```
"""


class Planner:
    def __init__(self, llm_client: HelloAgentsLLM):
        self.llm_client = llm_client

    def plan(self, question: str) -> list[str]:
        prompt = PLANNER_PROMPT_TEMPLATE.format(question=question)
        messages = [{"role": "user", "content": prompt}]

        print("--- 正在生成计划 ---")
        response_text = self.llm_client.think(messages=messages) or ""
        print(f"✅ 计划已生成:\n{response_text}")

        try:
            plan_str = response_text.split("```python")[1].split("```")[0].strip()
            plan = ast.literal_eval(plan_str)
            return plan if isinstance(plan, list) else []
        except (ValueError, SyntaxError, IndexError) as e:
            print(f"❌ 解析计划时出错: {e}")
            print(f"原始响应: {response_text}")
            return []
        except Exception as e:
            print(f"❌ 解析计划时发生未知错误: {e}")
            return []


# --- 3. 执行器 (Executor) 定义 ---
EXECUTOR_PROMPT_TEMPLATE = """
你是一位顶级的AI执行专家。你的任务是严格按照给定的计划，一步步地解决问题。
你将收到原始问题、完整的计划、以及到目前为止已经完成的步骤和结果。
请你专注于解决“当前步骤”，并仅输出该步骤的最终答案，不要输出任何额外的解释或对话。

# 原始问题:
{question}

# 完整计划:
{plan}

# 历史步骤与结果:
{history}

# 当前步骤:
{current_step}

请仅输出针对“当前步骤”的回答:
"""


class Executor:
    def __init__(self, llm_client: HelloAgentsLLM):
        self.llm_client = llm_client

    def execute(self, question: str, plan: list[str]) -> str:
        history = ""
        final_answer = ""

        print("\n--- 正在执行计划 ---")
        for i, step in enumerate(plan, 1):
            print(f"\n-> 正在执行步骤 {i}/{len(plan)}: {step}")
            prompt = EXECUTOR_PROMPT_TEMPLATE.format(
                question=question, plan=plan, history=history if history else "无", current_step=step
            )
            messages = [{"role": "user", "content": prompt}]

            response_text = self.llm_client.think(messages=messages) or ""

            history += f"步骤 {i}: {step}\n结果: {response_text}\n\n"
            final_answer = response_text
            print(f"✅ 步骤 {i} 已完成，结果: {final_answer}")

        return final_answer


# --- 4. 智能体 (Agent) 整合 ---
class PlanAndSolveAgent:
    def __init__(self, llm_client: HelloAgentsLLM):
        self.llm_client = llm_client
        self.planner = Planner(self.llm_client)
        self.executor = Executor(self.llm_client)

    def run(self, question: str):
        print(f"\n--- 开始处理问题 ---\n问题: {question}")
        plan = self.planner.plan(question)
        if not plan:
            print("\n--- 任务终止 --- \n无法生成有效的行动计划。")
            return
        final_answer = self.executor.execute(question, plan)
        print(f"\n--- 任务完成 ---\n最终答案: {final_answer}")


# --- 5. 主函数入口 ---
if __name__ == '__main__':
    try:
        llm_client = HelloAgentsLLM()
        agent = PlanAndSolveAgent(llm_client)
        question = "一个水果店周一卖出了15个苹果。周二卖出的苹果数量是周一的两倍。周三卖出的数量比周二少了5个。请问这三天总共卖出了多少个苹果？"
        agent.run(question)
    except ValueError as e:
        print(e)
``````

</details>

**运行记录**：

<details>
<summary>点击查看运行记录</summary>

``````python
--- 开始处理问题 ---
问题: 一个水果店周一卖出了15个苹果。周二卖出的苹果数量是周一的两倍。周三卖出的数量比周二少了5个。请问这三天总共卖出了多少个苹果？
--- 正在生成计划 ---
🧠 正在调用 deepseek-v4-flash 模型...
✅ 大语言模型响应成功:
```python
["计算周二卖出的苹果数量：15乘以2等于30", "计算周三卖出的苹果数量：30减去5等于25", "计算三天总销量：15加30加25等于70"]
```
✅ 计划已生成:
```python
["计算周二卖出的苹果数量：15乘以2等于30", "计算周三卖出的苹果数量：30减去5等于25", "计算三天总销量：15加30加25等于70"]
```

--- 正在执行计划 ---

-> 正在执行步骤 1/3: 计算周二卖出的苹果数量：15乘以2等于30
🧠 正在调用 deepseek-v4-flash 模型...
✅ 大语言模型响应成功:
30
✅ 步骤 1 已完成，结果: 30

-> 正在执行步骤 2/3: 计算周三卖出的苹果数量：30减去5等于25
🧠 正在调用 deepseek-v4-flash 模型...
✅ 大语言模型响应成功:
25
✅ 步骤 2 已完成，结果: 25

-> 正在执行步骤 3/3: 计算三天总销量：15加30加25等于70
🧠 正在调用 deepseek-v4-flash 模型...
✅ 大语言模型响应成功:
70
✅ 步骤 3 已完成，结果: 70

--- 任务完成 ---
最终答案: 70
``````

</details>


### 4.4 **Reflection**

**4.4.1 Reflection 的工作流程**：引入一种事后（post-hoc）的自我校正循环，即<span style="color: red;">执行 -> 反思 -> 优化</span>三步循环

- 执行（Execution）：使用熟悉的方法（如 ReAct 或 Plan-and-Solve）尝试完成任务，生成一个初步的解决方案。这可以看作是“初稿”。
- 反思（Reflection）：调用一个独立的、或者带有特殊提示词的大语言模型实例，来扮演一个“评审员”的角色。这个“评审员”会审视第一步生成的“初稿”，并从多个维度进行评估，根据评估，它会生成一段结构化的反馈 (Feedback)，指出具体的问题所在和改进建议。
    - 事实性错误：是否存在与常识或已知事实相悖的内容？
    - 逻辑漏洞：推理过程是否存在不连贯或矛盾之处？
    - 效率问题：是否有更直接、更简洁的路径来完成任务？
    - 遗漏信息：是否忽略了问题的某些关键约束或方面？
- 优化（Refinement）：将“初稿”和“反馈”作为新的上下文，再次调用大语言模型，要求它根据反馈内容对初稿进行修正，生成一个更完善的“修订稿”。

![Reflection工作原理](images/Reflection工作原理.png)


**Reflection 智能体的编码实现**：

<details>
<summary>点击展开完整代码</summary>

``````python
from typing import List, Dict, Any
# 假设 llm_client.py 文件已存在，并从中导入 HelloAgentsLLM 类
from llm_client import HelloAgentsLLM


# --- 模块 1: 记忆模块 ---

class Memory:
    """
    一个简单的短期记忆模块，用于存储智能体的行动与反思轨迹。
    """

    def __init__(self):
        # 初始化一个空列表来存储所有记录
        self.records: List[Dict[str, Any]] = []

    def add_record(self, record_type: str, content: str):
        """
        向记忆中添加一条新记录。

        参数:
        - record_type (str): 记录的类型 ('execution' 或 'reflection')。
        - content (str): 记录的具体内容 (例如，生成的代码或反思的反馈)。
        """
        self.records.append({"type": record_type, "content": content})
        print(f"📝 记忆已更新，新增一条 '{record_type}' 记录。")

    def get_trajectory(self) -> str:
        """
        将所有记忆记录格式化为一个连贯的字符串文本，用于构建提示词。
        """
        trajectory = ""
        for record in self.records:
            if record['type'] == 'execution':
                trajectory += f"--- 上一轮尝试 (代码) ---\n{record['content']}\n\n"
            elif record['type'] == 'reflection':
                trajectory += f"--- 评审员反馈 ---\n{record['content']}\n\n"
        return trajectory.strip()

    def get_last_execution(self) -> str:
        """
        获取最近一次的执行结果 (例如，最新生成的代码)。
        """
        for record in reversed(self.records):
            if record['type'] == 'execution':
                return record['content']
        return None


# --- 模块 2: Reflection 智能体 ---

# 1. 初始执行提示词
INITIAL_PROMPT_TEMPLATE = """
你是一位资深的Python程序员。请根据以下要求，编写一个Python函数。
你的代码必须包含完整的函数签名、文档字符串，并遵循PEP 8编码规范。

要求: {task}

请直接输出代码，不要包含任何额外的解释。
"""

# 2. 反思提示词
REFLECT_PROMPT_TEMPLATE = """
你是一位极其严格的代码评审专家和资深算法工程师，对代码的性能有极致的要求。
你的任务是审查以下Python代码，并专注于找出其在**算法效率**上的主要瓶颈。

# 原始任务:
{task}

# 待审查的代码:
```python
{code}
```

请分析该代码的时间复杂度，并思考是否存在一种**算法上更优**的解决方案来显著提升性能。
如果存在，请清晰地指出当前算法的不足，并提出具体的、可行的改进算法建议（例如，使用筛法替代试除法）。
如果代码在算法层面已经达到最优，才能回答“无需改进”。

请直接输出你的反馈，不要包含任何额外的解释。
"""

# 3. 优化提示词
REFINE_PROMPT_TEMPLATE = """
你是一位资深的Python程序员。你正在根据一位代码评审专家的反馈来优化你的代码。

# 原始任务:
{task}

# 你上一轮尝试的代码:
{last_code_attempt}

# 评审员的反馈:
{feedback}

请根据评审员的反馈，生成一个优化后的新版本代码。
你的代码必须包含完整的函数签名、文档字符串，并遵循PEP 8编码规范。
请直接输出优化后的代码，不要包含任何额外的解释。
"""


class ReflectionAgent:
    def __init__(self, llm_client, max_iterations=3):
        self.llm_client = llm_client
        self.memory = Memory()
        self.max_iterations = max_iterations

    def run(self, task: str):
        print(f"\n--- 开始处理任务 ---\n任务: {task}")

        # --- 1. 初始执行 ---
        print("\n--- 正在进行初始尝试 ---")
        initial_prompt = INITIAL_PROMPT_TEMPLATE.format(task=task)
        initial_code = self._get_llm_response(initial_prompt)
        self.memory.add_record("execution", initial_code)

        # --- 2. 迭代循环：反思与优化 ---
        for i in range(self.max_iterations):
            print(f"\n--- 第 {i + 1}/{self.max_iterations} 轮迭代 ---")

            # a. 反思
            print("\n-> 正在进行反思...")
            last_code = self.memory.get_last_execution()
            reflect_prompt = REFLECT_PROMPT_TEMPLATE.format(task=task, code=last_code)
            feedback = self._get_llm_response(reflect_prompt)
            self.memory.add_record("reflection", feedback)

            # b. 检查是否需要停止
            if "无需改进" in feedback or "no need for improvement" in feedback.lower():
                print("\n✅ 反思认为代码已无需改进，任务完成。")
                break

            # c. 优化
            print("\n-> 正在进行优化...")
            refine_prompt = REFINE_PROMPT_TEMPLATE.format(
                task=task,
                last_code_attempt=last_code,
                feedback=feedback
            )
            refined_code = self._get_llm_response(refine_prompt)
            self.memory.add_record("execution", refined_code)

        final_code = self.memory.get_last_execution()
        print(f"\n--- 任务完成 ---\n最终生成的代码:\n{final_code}")
        return final_code

    def _get_llm_response(self, prompt: str) -> str:
        """一个辅助方法，用于调用LLM并获取完整的流式响应。"""
        messages = [{"role": "user", "content": prompt}]
        # 确保能处理生成器可能返回None的情况
        response_text = self.llm_client.think(messages=messages) or ""
        return response_text


if __name__ == '__main__':
    # 1. 初始化LLM客户端 (请确保你的 .env 和 llm_client.py 文件配置正确)
    try:
        llm_client = HelloAgentsLLM()
    except Exception as e:
        print(f"初始化LLM客户端时出错: {e}")
        exit()

    # 2. 初始化 Reflection 智能体，设置最多迭代2轮
    agent = ReflectionAgent(llm_client, max_iterations=2)

    # 3. 定义任务并运行智能体
    task = "编写一个Python函数，找出1到n之间所有的素数 (prime numbers)。"
    agent.run(task)
``````

</details>

**运行记录**：

<details>
<summary>点击查看运行记录</summary>

``````python
--- 开始处理任务 ---
任务: 编写一个Python函数，找出1到n之间所有的素数 (prime numbers)。

--- 正在进行初始尝试 ---
🧠 正在调用 deepseek-v4-flash 模型...
✅ 大语言模型响应成功:
```python
def find_primes(n):
    """
    找出 1 到 n 之间所有的素数（包含 n）。

    参数：
        n (int): 正整数上限

    返回：
        list: 由小到大排列的素数列表

    异常：
        ValueError: 当 n 不是正整数时抛出

    示例：
        >>> find_primes(10)
        [2, 3, 5, 7]
        >>> find_primes(1)
        []
    """
    if not isinstance(n, int) or n < 1:
        raise ValueError("n 必须是正整数")

    if n < 2:
        return []

    # 埃拉托斯特尼筛法
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False

    for i in range(2, int(n ** 0.5) + 1):
        if is_prime[i]:
            for j in range(i * i, n + 1, i):
                is_prime[j] = False

    return [num for num in range(2, n + 1) if is_prime[num]]
```
📝 记忆已更新，新增一条 'execution' 记录。

--- 第 1/2 轮迭代 ---

-> 正在进行反思...
🧠 正在调用 deepseek-v4-flash 模型...
✅ 大语言模型响应成功:
该代码使用埃拉托斯特尼筛法，时间复杂度为 **O(n log log n)**，空间复杂度为 **O(n)**。该算法是求解 1 到 n 内全部素数的经典最优方法之一，在算法层面已无需改进。
📝 记忆已更新，新增一条 'reflection' 记录。

✅ 反思认为代码已无需改进，任务完成。

--- 任务完成 ---
最终生成的代码:
```python
def find_primes(n):
    """
    找出 1 到 n 之间所有的素数（包含 n）。

    参数：
        n (int): 正整数上限

    返回：
        list: 由小到大排列的素数列表

    异常：
        ValueError: 当 n 不是正整数时抛出

    示例：
        >>> find_primes(10)
        [2, 3, 5, 7]
        >>> find_primes(1)
        []
    """
    if not isinstance(n, int) or n < 1:
        raise ValueError("n 必须是正整数")

    if n < 2:
        return []

    # 埃拉托斯特尼筛法
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False

    for i in range(2, int(n ** 0.5) + 1):
        if is_prime[i]:
            for j in range(i * i, n + 1, i):
                is_prime[j] = False

    return [num for num in range(2, n + 1) if is_prime[num]]
```
``````

</details>

**4.4.5 Reflection 机制的成本收益分析**：

- 主要成本
    - 模型调用开销增加
    - 任务延迟显著提高
    - 提示工程复杂度上升
- 核心收益
    - 解决方案质量的跃迁
    - 鲁棒性与可靠性增强

**Reflection 的适用场景**：非常适合那些对最终结果的质量、准确性和可靠性有极高要求，且对任务完成的实时性要求相对宽松的场景

- 生成关键的业务代码或技术报告
- 在科学研究中进行复杂的逻辑推演
- 需要深度分析和规划的决策支持系统

## 五、基于低代码平台的智能体搭建

在前一章中，通过编写 Python 代码，从零开始实现了 ReAct、Plan-and-Solve 和 Reflection 多种经典的智能体工作流。这个过程为我们打下了坚实的技术基础，让我们深刻理解了智能体内部的运作机理。然而，对于一个快速发展的领域而言，纯代码的开发模式并非总是最高效的选择，尤其是在需要快速验证想法、或者非专业开发者希望参与构建的场景中。

### 5.1 **平台化构建的兴起**

随着技术的成熟，我们看到越来越多的能力正在被“平台化”。正如网站的开发从手写 HTML/CSS/JS，演进到了可以使用 WordPress、Wix 等建站平台一样，智能体的构建也迎来了平台化的浪潮。本章将聚焦于如何利用图形化、模块化的低代码平台，来快速、直观地搭建、调试和部署智能体应用，将我们的重心从“实现细节”转向“业务逻辑”。

**5.1.1 为何需要低代码平台（低代码平台的核心价值）**

- 降低技术门槛
- 提升开发效率
- 提供更优的可视化与可观测性
- 标准化与最佳实践沉淀

**5.1.2 低代码平台的选择**

**Coze**

- 核心定位：由字节跳动推出的 Coze，主打零代码/低代码的 Agent 的构建体验，让不具备编程背景的用户也能轻松创造
- 特点分析：Coze 拥有极其友好的可视化界面，用户可以像搭建乐高积木一样，通过拖拽插件、配置知识库和设定工作流来创建智能体。其内置了极为丰富的插件库，并支持一键发布到抖音、飞书、微信公众号等多个主流平台，极大地简化了分发流程。
- 适用人群：AI 应用的入门用户、产品经理、运营人员，以及希望快速将创意变为可交互产品的个人创作者。

**Dify**

- 核心定位：Dify 是一个开源的、功能全面的 LLM 应用开发与运营平台[2]，旨在为开发者提供从原型构建到生产部署的一站式解决方案。
- 特点分析：它融合了后端服务和模型运营的理念，支持 Agent 工作流、RAG Pipeline、数据标注与微调等多种能力。对于追求专业、稳定、可扩展的企业级应用而言，Dify 提供了坚实的基础。
- 适用人群：有一定技术背景的开发者、需要构建可扩展的企业级 AI 应用的团队。

**FastGPT**

- 核心定位：FastGPT 是一个开源的、基于 LLM 大语言模型的知识库问答平台与 Agent 构建工具[3]，专注于提供简单易用的 RAG（检索增强生成）解决方案和可视化工作流编排能力。
- 特点分析：FastGPT 最核心的优势在于其对知识库问答场景的极致优化。它提供了从数据导入、自动文本分块、向量化存储到智能检索的完整 RAG 链路，并支持通过直观的可视化界面（Flow 模块）编排复杂的对话流程和 Agent 工作流。平台采用模型中立设计，可灵活对接 OpenAI、Claude、通义千问等多种国内外主流大模型，同时提供了完善的 API 接口和插件市场，便于与企业微信、钉钉、飞书等现有系统快速集成。
- 适用人群：希望基于私有知识库快速搭建智能客服、企业内部知识助手、文档问答机器人的开发者和中小企业团队，以及对 RAG 技术感兴趣但希望降低实现门槛的技术爱好者。

**n8n**

- 核心定位：n8n 本质上是一个开源工作流自动化工具，而非纯粹的 LLM 平台。近年来，它积极集成了 AI 能力。
- 特点分析：n8n 的强项在于“连接”。它拥有数百个预置的节点，可以轻松地将各类 SaaS 服务、数据库、API 连接成复杂的自动化业务流程。你可以在这个流程中嵌入 LLM 节点，使其成为整个自动化链路中的一环。虽然在 LLM 功能的专一度上不如前两者，但其通用自动化能力是独一无二的。不过，其学习曲线也相对陡峭。
- 适用人群：需要将 AI 能力深度整合进现有业务流程、实现高度定制化自动化的开发者和企业。

### 5.2 **平台一：Coze**

**5.2.3 Coze 的优势与局限性分析**

**优势**：

- 强大的插件生态系统
- 直观的可视化编排
- 灵活的提示词控制
- 便捷的多平台部署

**局限性**：

- 不支持MCP
- 部分插件配置的复杂度高
- 无法直接导入编排 json 文件

### 5.3 **平台二：Dify**

### 5.4 **平台三：FastGPT**

### 5.5 **平台四：n8n**

## 六、框架开发实践

**本章目标**：探讨如何利用业界主流的一些智能体框架，来高效、规范地构建可靠的智能体应用

**框架的本质**：将所有智能体共有的、重复性的工作（如主循环、状态管理、工具调用、日志记录等）进行抽象和封装

### 6.1 **从手动实现到框架开发**

**6.1.1 为何需要智能体框架**：

- 提升代码复用与开发效率：这是最直接的价值。一个好的框架会提供一个通用的 Agent 基类或执行器，它封装了智能体运行的核心循环（Agent Loop）。无论是 ReAct 还是 Plan-and-Solve，都可以基于框架提供的标准组件快速搭建，从而避免重复劳动。
- 实现核心组件的解耦与可扩展性：
     - 模型层 (Model Layer)：负责与大语言模型交互，可以轻松替换不同的模型（OpenAI, Anthropic, 本地模型）
     - 工具层 (Tool Layer)：提供标准化的工具定义、注册和执行接口，添加新工具不会影响其他代码
     - 记忆层 (Memory Layer)：处理短期和长期记忆，可以根据需求切换不同的记忆策略（如滑动窗口、摘要记忆）
- 标准化复杂的状态管理：我们在`ReflectionAgent`中实现的`Memory`类只是一个简单的开始。在真实的、长时运行的智能体应用中，状态管理是一个巨大的挑战，它需要处理上下文窗口限制、历史信息持久化、多轮对话状态跟踪等问题。一个框架可以提供一套强大而通用的状态管理机制，开发者无需每次都重新处理这些复杂问题。
- 简化可观测性与调试过程：当智能体的行为变得复杂时，理解其决策过程变得至关重要。一个精心设计的框架可以内置强大的可观测性能力。例如，通过引入事件回调机制（Callbacks），我们可以在智能体生命周期的关键节点（如 `on_llm_start`, `on_tool_end`, `on_agent_finish`）自动触发日志记录或数据上报，从而轻松地追踪和调试智能体的完整运行轨迹。这远比在代码中手动添加`print`语句要高效和系统化。

**6.1.2 主流框架的选型与对比**：

![四种智能体框架对比.png](images%2F%E5%9B%9B%E7%A7%8D%E6%99%BA%E8%83%BD%E4%BD%93%E6%A1%86%E6%9E%B6%E5%AF%B9%E6%AF%94.png)

### 6.2 **框架一：微软 AutoGen**——企业级多智能体协作框架

**6.2.1 AutoGen 的核心机制**：

![AutoGen架构图.png](images%2FAutoGen%E6%9E%B6%E6%9E%84%E5%9B%BE.png)

（1）框架结构的演进

- 分层设计： 框架被拆分为两个核心模块：
    - autogen-core：作为框架的底层基础，封装了与语言模型交互、消息传递等核心功能。它的存在保证了框架的稳定性和未来扩展性。
    - autogen-agentchat：构建于 core 之上，提供了用于开发对话式智能体应用的高级接口，简化了多智能体应用的开发流程。
- 异步优先： 新架构全面转向异步编程 (async/await)。在多智能体协作场景中，网络请求（如调用 LLM API）是主要耗时操作。异步模式允许系统在等待一个智能体响应时处理其他任务，从而避免了线程阻塞，显著提升了并发处理能力和系统资源的利用效率。

（2）核心智能体组件

- AssistantAgent (助理智能体)： 这是任务的主要解决者，其核心是封装了一个大型语言模型（LLM）。它的职责是根据对话历史生成富有逻辑和知识的回复，例如提出计划、撰写文章或编写代码。通过不同的系统消息（System Message），我们可以为其赋予不同的“专家”角色。
- UserProxyAgent (用户代理智能体)： 这是 AutoGen 中功能独特的组件。它扮演着双重角色：既是人类用户的“代言人”，负责发起任务和传达意图；又是一个可靠的“执行器”，可以配置为执行代码或调用工具，并将结果反馈给其他智能体。这种设计清晰地区分了“思考”（由 AssistantAgent 完成）与“行动”。

（3）从 GroupChatManager 到 Team

- 轮询群聊 (RoundRobinGroupChat)： 这是一种明确的、顺序化的对话协调机制。它会让参与的智能体按照预定义的顺序依次发言。这种模式非常适用于流程固定的任务，例如一个典型的软件开发流程：产品经理先提出需求，然后工程师编写代码，最后由代码审查员进行检查。
- 工作流：
    1. 首先，创建一个 `RoundRobinGroupChat` 实例，并将所有参与协作的智能体（如产品经理、工程师等）加入其中。
    2. 当一个任务开始时，群聊会按照预设的顺序，依次激活相应的智能体。
    3. 被选中的智能体根据当前的对话上下文进行响应。
    4. 群聊将新的回复加入对话历史，并激活下一个智能体。
    5. 这个过程会持续进行，直到达到最大对话轮次或满足预设的终止条件。

**6.2.2 软件开发团队**：

本节将通过一个完整的实战案例来具体展示如何应用这些新特性。我们将构建一个模拟的软件开发团队，该团队由多个具有不同专业技能的智能体组成，它们将协作完成一个真实的软件开发任务。

（1）业务目标

我们的目标是开发一个功能明确的 Web 应用：实时显示比特币当前价格。这个任务虽小，却完整地覆盖了软件开发的典型环节：从需求分析、技术选型、编码实现到代码审查和最终测试。这使其成为检验 AutoGen 自动化协作流程的理想场景。

（2）智能体团队角色

为了模拟真实的软件开发流程，我们设计了四个职责分明的智能体角色：

- ProductManager (产品经理): 负责将用户的模糊需求转化为清晰、可执行的开发计划。
- Engineer (工程师): 依据开发计划，负责编写具体的应用程序代码。
- CodeReviewer (代码审查员): 负责审查工程师提交的代码，确保其质量、可读性和健壮性。
- UserProxy (用户代理): 代表最终用户，发起初始任务，并负责执行和验证最终交付的代码。

这种角色划分是多智能体系统设计中的关键一步，它将一个复杂任务分解为多个由领域“专家”处理的子任务。

**6.2.3 核心代码实现**：

下面，我们将分步解析这个自动化团队的核心代码。

（1）模型客户端配置

所有基于 LLM 的智能体都需要一个模型客户端来与语言模型进行交互。AutoGen `0.7.4` 提供了标准化的 `OpenAIChatCompletionClient`，它可以方便地与任何兼容 OpenAI API 规范的模型服务（包括 OpenAI 官方服务、Azure OpenAI 以及本地模型服务如 Ollama等）进行对接。

我们通过一个独立的函数来创建和配置模型客户端，并通过环境变量管理 API Key 和服务地址，这是一种良好的工程实践，增强了代码的灵活性和安全性。

```python
from autogen_ext.models.openai import OpenAIChatCompletionClient

def create_openai_model_client():
    """创建并配置 OpenAI 模型客户端"""
    return OpenAIChatCompletionClient(
        model=os.getenv("LLM_MODEL_ID", "gpt-4o"),
        api_key=os.getenv("LLM_API_KEY"),
        base_url=os.getenv("LLM_BASE_URL", "https://api.openai.com/v1")
    )
```

（2）智能体角色的定义

定义智能体的核心在于编写高质量的系统消息 (System Message)。系统消息就像是给智能体设定的“行为准则”和“专业知识库”，它精确地规定了智能体的角色、职责、工作流程，甚至是与其他智能体交互的方式。一个精心设计的系统消息是确保多智能体系统能够高效、准确协作的关键。在我们的软件开发团队中，我们为每一个角色都创建了一个独立的函数来封装其定义。

<strong>产品经理 (ProductManager)</strong>

产品经理负责启动整个流程。它的系统消息不仅定义了其职责，还规范了其输出的结构，并包含了引导对话转向下一环节（工程师）的明确指令。

```python
def create_product_manager(model_client):
    """创建产品经理智能体"""
    system_message = """你是一位经验丰富的产品经理，专门负责软件产品的需求分析和项目规划。

你的核心职责包括：
1. **需求分析**：深入理解用户需求，识别核心功能和边界条件
2. **技术规划**：基于需求制定清晰的技术实现路径
3. **风险评估**：识别潜在的技术风险和用户体验问题
4. **协调沟通**：与工程师和其他团队成员进行有效沟通

当接到开发任务时，请按以下结构进行分析：
1. 需求理解与分析
2. 功能模块划分
3. 技术选型建议
4. 实现优先级排序
5. 验收标准定义

请简洁明了地回应，并在分析完成后说"请工程师开始实现"。"""

    return AssistantAgent(
        name="ProductManager",
        model_client=model_client,
        system_message=system_message,
    )
```

<strong>工程师 (Engineer)</strong>

工程师的系统消息聚焦于技术实现。它列举了工程师的技术专长，并规定了其在接收到任务后的具体行动步骤，同样也包含了引导流程转向代码审查员的指令。

```python
def create_engineer(model_client):
    """创建软件工程师智能体"""
    system_message = """你是一位资深的软件工程师，擅长 Python 开发和 Web 应用构建。

你的技术专长包括：
1. **Python 编程**：熟练掌握 Python 语法和最佳实践
2. **Web 开发**：精通 Streamlit、Flask、Django 等框架
3. **API 集成**：有丰富的第三方 API 集成经验
4. **错误处理**：注重代码的健壮性和异常处理

当收到开发任务时，请：
1. 仔细分析技术需求
2. 选择合适的技术方案
3. 编写完整的代码实现
4. 添加必要的注释和说明
5. 考虑边界情况和异常处理

请提供完整的可运行代码，并在完成后说"请代码审查员检查"。"""

    return AssistantAgent(
        name="Engineer",
        model_client=model_client,
        system_message=system_message,
    )
```

<strong>代码审查员 (CodeReviewer)</strong>

代码审查员的定义则侧重于代码的质量、安全性和规范性。它的系统消息详细列出了审查的重点和流程，确保了代码交付前的质量关卡。

```python
def create_code_reviewer(model_client):
    """创建代码审查员智能体"""
    system_message = """你是一位经验丰富的代码审查专家，专注于代码质量和最佳实践。

你的审查重点包括：
1. **代码质量**：检查代码的可读性、可维护性和性能
2. **安全性**：识别潜在的安全漏洞和风险点
3. **最佳实践**：确保代码遵循行业标准和最佳实践
4. **错误处理**：验证异常处理的完整性和合理性

审查流程：
1. 仔细阅读和理解代码逻辑
2. 检查代码规范和最佳实践
3. 识别潜在问题和改进点
4. 提供具体的修改建议
5. 评估代码的整体质量

请提供具体的审查意见，完成后说"代码审查完成，请用户代理测试"。"""

    return AssistantAgent(
        name="CodeReviewer",
        model_client=model_client,
        system_message=system_message,
    )
```

<strong>用户代理 (UserProxy)</strong>

`UserProxyAgent` 是一个特殊的智能体，它不依赖 LLM 进行回复，而是作为用户在系统中的代理。它的 `description` 字段清晰地描述了其职责，尤其重要的是，它负责在任务最终完成后发出 `TERMINATE` 指令，以正常结束整个协作流程。

```python
def create_user_proxy():
    """创建用户代理智能体"""
    return UserProxyAgent(
        name="UserProxy",
        description="""用户代理，负责以下职责：
1. 代表用户提出开发需求
2. 执行最终的代码实现
3. 验证功能是否符合预期
4. 提供用户反馈和建议

完成测试后请回复 TERMINATE。""",
    )
```

通过这四个独立的定义函数，我们不仅构建了一支功能完备的“虚拟团队”，也展示了通过系统消息进行“提示工程” ，是设计高效多智能体应用的核心环节。

（3）定义团队协作流程

在本案例中，软件开发的流程是相对固定的（需求->编码->审查->测试），因此 `RoundRobinGroupChat` (轮询群聊) 是理想的选择。我们按照业务逻辑顺序，将四个智能体加入到参与者列表中。

```python
from autogen_agentchat.teams import RoundRobinGroupChat
from autogen_agentchat.conditions import TextMentionTermination

# 定义团队聊天和协作规则
team_chat = RoundRobinGroupChat(
    participants=[
        product_manager,
        engineer,
        code_reviewer,
        user_proxy
    ],
    termination_condition=TextMentionTermination("TERMINATE"),
    max_turns=20,
)
```

- <strong>参与者顺序:</strong> `participants` 列表的顺序决定了智能体发言的先后次序。
- <strong>终止条件:</strong> `termination_condition` 是控制协作流程何时结束的关键。这里我们设定，当任何消息中包含关键词 "TERMINATE" 时，对话便结束。在我们的设计中，这个指令由 `UserProxy` 在完成最终测试后发出。
- <strong>最大轮次:</strong> `max_turns` 是一个安全阀，用于防止对话陷入无限循环，避免不必要的资源消耗。

（4）启动与运行

由于 AutoGen `0.7.4` 采用异步架构，整个协作流程的启动和运行都在一个异步函数中完成，并最终通过 `asyncio.run()` 来执行。

```python
async def run_software_development_team():
    # ... 初始化客户端和智能体 ...
    
    # 定义任务描述
    task = """我们需要开发一个比特币价格显示应用，具体要求如下：
            核心功能：
            - 实时显示比特币当前价格（USD）
            - 显示24小时价格变化趋势（涨跌幅和涨跌额）
            - 提供价格刷新功能

            技术要求：
            - 使用 Streamlit 框架创建 Web 应用
            - 界面简洁美观，用户友好
            - 添加适当的错误处理和加载状态

            请团队协作完成这个任务，从需求分析到最终实现。"""
    
    # 异步执行团队协作，并流式输出对话过程
    result = await Console(team_chat.run_stream(task=task))
    return result

# 主程序入口
if __name__ == "__main__":
    result = asyncio.run(run_software_development_team())
```

当程序运行时，`task` 作为初始消息被传入 `team_chat`，产品经理作为第一个参与者接收到该消息，随后整个自动化协作流程便开始了。

（5）预期协作效果

当我们运行这个软件开发团队时，可以观察到一个完整的协作流程：

```bash
🔧 正在初始化模型客户端...
👥 正在创建智能体团队...
🚀 启动 AutoGen 软件开发团队协作...
============================================================
---------- TextMessage (user) ----------
我们需要开发一个比特币价格显示应用，具体要求如下：
...
请团队协作完成这个任务，从需求分析到最终实现。
---------- TextMessage (ProductManager) ----------
### 1. 需求理解与分析
...
请工程师开始实现。
---------- TextMessage (Engineer) ----------
### 技术方案实施
...
请代码审查员检查。
---------- TextMessage (CodeReviewer) ----------
### 代码审查
...
代码审查完成，请用户代理测试。    
---------- TextMessage (UserProxy) ----------
已经完成需求
---------- TextMessage (ProductManager) ----------
太好了，感谢您的反馈！如果在使用过程中有任何问题，或者有其他功能需求和改进建议，请随时告知我们。我们会持续提供支持和改进。期待您对我们的应用
有愉快的使用体验！
---------- TextMessage (Engineer) ----------
很高兴听到项目顺利完成。如果您或用户有任何问题或者需要帮助，请随时联系我们。感谢您对我们工作的支持，让我们一起确保应用稳定运行并不断优化用户
体验！
---------- TextMessage (CodeReviewer) ----------
非常感谢大家的努力与协作，使得项目能够顺利完成。未来若有更多技术支持的需求或者需要改进的地方，我们愿意为项目的持续优化贡
献力量。期待用户能够享受到流畅的体验，同时也欢迎提出更多的反馈与建议。再次感谢团队的合作！
---------- TextMessage (UserProxy) ----------
Enter your response: TERMINATE
============================================================
✅ 团队协作完成！

📋 协作结果摘要：
- 参与智能体数量：4个
- 任务完成状态：成功
```


**该案例的生成代码**：

<details>
<summary>点击展开上述案例的生成代码</summary>

```python
import streamlit as st
import requests

# 页面配置
st.set_page_config(
    page_title="Bitcoin Price Tracker",
    page_icon="₿",
    layout="centered"
)

API_URL = "https://api.coingecko.com/api/v3/coins/bitcoin"


# 使用 cache_data，TTL=30秒；命中缓存时不显示 spinner，未命中时显示自定义提示
@st.cache_data(ttl=30, show_spinner="Fetching latest Bitcoin price...")
def fetch_bitcoin_data():
    """获取比特币市场数据"""
    params = {
        "localization": "false",
        "tickers": "false",
        "market_data": "true",
        "community_data": "false",
        "developer_data": "false"
    }

    resp = requests.get(API_URL, params=params, timeout=10)
    resp.raise_for_status()
    data = resp.json()

    return {
        "price": data["market_data"]["current_price"]["usd"],
        "price_change_24h": data["market_data"]["price_change_24h"],
        "price_change_24h_pct": data["market_data"]["price_change_percentage_24h"],
        "high_24h": data["market_data"]["high_24h"]["usd"],
        "low_24h": data["market_data"]["low_24h"]["usd"]
    }


def show_error(message):
    """显示错误信息及重试按钮"""
    st.error(f"⚠️ {message}")
    if st.button("🔄 重试"):
        fetch_bitcoin_data.clear()
        st.rerun()


def main():
    # 顶部标题区
    st.title("₿ Bitcoin Price Tracker")
    st.caption("Live BTC/USD price and 24-hour performance")

    # 布局：按钮放左上角，内容居中
    col_top_left, col_top_center, col_top_right = st.columns([1, 3, 1])
    with col_top_left:
        # 手动刷新（只清除该函数缓存）
        if st.button("🔄 Refresh", use_container_width=True):
            fetch_bitcoin_data.clear()
            st.rerun()

    # 获取数据（自动依赖 cache_data 的 TTL）
    try:
        data = fetch_bitcoin_data()
    except requests.exceptions.Timeout:
        show_error("请求超时，网络连接不稳定。")
        return
    except requests.exceptions.RequestException as e:
        show_error(f"网络错误：{str(e)}")
        return
    except (KeyError, ValueError) as e:
        show_error(f"API 返回数据格式异常：{str(e)}")
        return

    # 成功获取数据后展示
    price = data["price"]
    change_abs = data["price_change_24h"]
    change_pct = data["price_change_24h_pct"]

    # 涨跌方向与颜色
    if change_pct >= 0:
        arrow = "🚀"
        color = "normal"
    else:
        arrow = "📉"
        color = "inverse"

    # 核心价格指标
    st.metric(
        label="Bitcoin (BTC) Price",
        value=f"${price:,.2f}",
        delta=f"{change_abs:+,.2f} ({change_pct:+.2f}%)",
        delta_color=color,
        help="24小时价格变化（金额和百分比）"
    )

    # 24h 高/低
    st.markdown(
        f"{arrow} 24小时最高：${data['high_24h']:,.2f} | 最低：${data['low_24h']:,.2f}"
    )

    # 更新说明
    st.caption("数据来源：CoinGecko | 每30秒自动更新，也可手动刷新")


if __name__ == "__main__":
    main()
```

</details>

**测试结果**：

![AutoGen 运行记录.png](images%2FAutoGen%20%E8%BF%90%E8%A1%8C%E8%AE%B0%E5%BD%95.png)


整个协作过程展现了 AutoGen 框架的优势：<strong>自然的对话驱动协作</strong>、<strong>角色专业化分工</strong>、<strong>流程自动化管理</strong>和<strong>完整的开发闭环</strong>。

**6.2.4 AutoGen 的优势与局限性分析**：

任何技术框架都有其特定的适用场景和设计权衡。在本节中，我们将客观地分析 AutoGen 的核心优势及其在实际应用中可能面临的局限性与挑战。

（1）优势

- 如案例所示，我们无需为智能体团队设计复杂的状态机或控制流逻辑，而是将一个完整的软件开发流程，自然地映射为产品经理、工程师和审查员之间的对话。这种方式更贴近人类团队的协作模式，显著降低了为复杂任务建模的门槛。开发者可以将更多精力聚焦于定义“谁（角色）”以及“做什么（职责）”，而非“如何做（流程控制）”。
- 框架允许通过系统消息（System Message）为每个智能体赋予高度专业化的角色。在案例中，`ProductManager` 专注于需求，而 `CodeReviewer` 则专注于质量。一个精心设计的智能体可以在不同项目中被复用，易于维护和扩展。
- 对于流程化任务，`RoundRobinGroupChat` 这样机制提供了清晰、可预测的协作流程。同时，`UserProxyAgent` 的设计为“人类在环”（Human-in-the-loop）提供了天然的接口。它既可以作为任务的发起者，也可以是流程的监督者和最终的验收者。这种设计确保了自动化系统始终处于人类的监督之下。

（2）局限性

- 虽然 `RoundRobinGroupChat` 提供了顺序化的流程，但基于 LLM 的对话本质上具有不确定性。智能体可能会产生偏离预期的回复，导致对话走向意外的分支，甚至陷入循环。
- 当智能体团队的工作结果未达预期时，调试过程可能非常棘手。与传统程序不同，我们得到的不是清晰的错误堆栈，而是一长串的对话历史。这被称为“对话式调试”的难题。

（3）非 OpenAI 模型的配置补充

如果你想使用非 OpenAI 系列的模型（如 DeepSeek、通义千问等），在 0.7.4 版本中需要在 `OpenAIChatCompletionClient` 的参数中传入模型信息字典。以 DeepSeek 为例：

```python
from autogen_ext.models.openai import OpenAIChatCompletionClient

model_client = OpenAIChatCompletionClient(
    model="deepseek-chat",
    api_key=os.getenv("DEEPSEEK_API_KEY"),
    base_url="https://api.deepseek.com/v1",
    model_info={
        "function_calling": True,
        "max_tokens": 4096,
        "context_length": 32768,
        "vision": False,
        "json_output": True,
        "family": "deepseek",
        "structured_output": True,
    }
)
```

这个 `model_info` 字典帮助 AutoGen 了解模型的能力边界，从而更好地适配不同的模型服务。


### 6.3 **框架二：阿里 AgentScope**——消息驱动架构

**6.3.1 AgentScope 的设计**：

（1）分层架构体系

![AgentScope架构图.png](images%2FAgentScope%E6%9E%B6%E6%9E%84%E5%9B%BE.png)

- 基础组件层 (Foundational Components)
- 智能体基础设施层 (Agent-level Infrastructure) 
- 多智能体协作层 (Multi-Agent Cooperation) ：核心创新
- 开发与部署层 (Deployment & Development)

（2）消息驱动

AgentScope 的核心创新在于其消息驱动架构。在这个架构中，所有的智能体交互都被抽象为消息的发送和接收，而不是传统的函数调用。

```python
from agentscope.message import Msg

# 消息的标准结构
message = Msg(
    name="Alice",           # 发送者名称
    content="Hello, Bob!",  # 消息内容
    role="user",           # 角色类型
    metadata={             # 元数据信息
        "timestamp": "2024-01-15T10:30:00Z",
        "message_type": "text",
        "priority": "normal"
    }
)
```

将消息作为交互的基础单元，带来了几个关键优势：

- 异步解耦: 消息的发送方和接收方在时间上解耦，无需相互等待，天然支持高并发场景
- 位置透明: 智能体无需关心另一个智能体是在本地进程还是在远程服务器上，消息系统会自动处理路由
- 可观测性: 每一条消息都可以被记录、追踪和分析，极大地简化了复杂系统的调试与监控
- 可靠性: 消息可以被持久化存储和重试，即使系统出现故障，也能保证交互的最终一致性，提升了系统的容错能力

（3）智能体生命周期管理

在 AgentScope 中，每个智能体都有明确的生命周期（初始化、运行、暂停、销毁等），并基于一个统一的基类 `AgentBase` 来实现。开发者通常只需要关注其核心的 `reply` 方法。

```python
from agentscope.agents import AgentBase

class CustomAgent(AgentBase):
    def __init__(self, name: str, **kwargs):
        super().__init__(name=name, **kwargs)
        # 智能体初始化逻辑
    
    def reply(self, x: Msg) -> Msg:
        # 智能体的核心响应逻辑
        response = self.model(x.content)
        return Msg(name=self.name, content=response, role="assistant")
    
    def observe(self, x: Msg) -> None:
        # 智能体的观察逻辑（可选）
        self.memory.add(x)
```

这种设计模式分离了智能体的内部逻辑与外部通信，开发者只需在 `reply` 方法中定义智能体“思考和回应”的方式即可。

（4）消息传递机制

AgentScope 内置了一个**消息中心 (MsgHub)**，它是整个消息驱动架构的中枢。MsgHub 不仅负责消息的路由和分发，还集成了持久化和分布式通信等高级功能，它有以下这些特点：

- 灵活的消息路由: 支持点对点、广播、组播等多种通信模式，可以构建灵活复杂的交互网络。
- 消息持久化: 能够将所有消息自动保存到数据库（如 SQLite, MongoDB），确保了长期运行任务的状态可以被恢复。
- 原生分布式支持: 这是 AgentScope 的标志性特性。智能体可以被部署在不同的进程或服务器上，`MsgHub` 会通过 RPC（远程过程调用）自动处理跨节点的通信，对开发者完全透明。

这些由底层架构提供的工程化能力，使得 AgentScope 在处理需要高并发、高可靠性的复杂应用场景时，比传统的对话驱动框架更具优势。当然，这也要求开发者理解并适应消息驱动的异步编程范式。


**6.3.2 三国狼人杀游戏**：


### 6.4 **框架三：KAUST CAMEL**——双智能体协作框架

与 AutoGen 和 AgentScope 这样功能全面的框架不同，CAMEL最初的核心目标是探索如何在最少的人类干预下，让两个智能体通过“角色扮演”自主协作解决复杂任务。

[camel 仓库](https://github.com/camel-ai/camel)

**6.4.1 CAMEL 的自主协作**

CAMEL 实现自主协作的基石是两大核心概念：角色扮演 (Role-Playing) 和 引导性提示 (Inception Prompting)。

（1）角色扮演

在 CAMEL 最初的设计中，一个任务通常由两个智能体协作完成。这两个智能体被赋予了互补的、明确定义的“角色”。一个扮演“AI 用户” (AI User)，负责提出需求、下达指令和构思任务步骤；另一个则扮演“AI 助理” (AI Assistant)，负责根据指令执行具体操作和提供解决方案。

例如，在一个“开发股票交易策略分析工具”的任务中：

* AI 用户 的角色可能是一位“资深股票交易员”。它懂市场、懂策略，但不懂编程。
* AI 助理 的角色则是一位“优秀的 Python 程序员”。它精通编程，但对股票交易一无所知。

通过这种设定，任务的解决过程就被自然地转化为一场两位“跨领域专家”之间的对话。交易员提出专业需求，程序员将其转化为代码实现，两者协作完成任何一方都无法独立完成的复杂任务。

（2）引导性提示

仅仅设定角色还不够，如何确保两个 AI 在没有人类持续监督的情况下，能始终“待在自己的角色里”，并且高效地朝着共同目标前进呢？这就是 CAMEL 最核心的技术，引导性提示发挥作用的地方。“引导性提示”是在对话开始前，分别注入给两个智能体的一段精心设计的、结构化的初始指令（System Prompt）。这段指令就像是为智能体植入的“行动纲领”，它通常包含以下几个关键部分：

* 明确自身角色：例如，“你是一位资深的股票交易员...”
* 告知协作者角色：例如，“你正在与一位优秀的 Python 程序员合作...”
* 定义共同目标：例如，“你们的共同目标是开发一个股票交易策略分析工具。”
* 设定行为约束和沟通协议：这是最关键的一环。例如，指令会要求 AI 用户“一次只提出一个清晰、具体的步骤”，并要求 AI 助理“在完成上一步之前不要追问更多细节”，同时规定双方需在回复的末尾使用特定标志（如`<SOLUTION>`）来标识任务的完成。

**6.4.2 案例-AI科普电子书**

略

**6.4.3 CAMEL 的优势与局限性分析**

（1）优势

- 轻架构、重提示

（2）主要局限性

- 对提示工程的高度依赖
- 协作规模的限制
- 任务适用性的边界


### 6.5 **框架四：LangChain AI LangGraph**——状态化多智能体编排框架

**6.5.1 LangGraph 的结构梳理**

LangGraph 作为 LangChain 生态系统的重要扩展，代表了智能体框架设计的一个全新方向。与前面介绍的基于“对话”的框架（如 AutoGen 和 CAMEL）不同，LangGraph 将智能体的执行流程建模为一种状态机（State Machine），并将其表示为有向图（Directed Graph）。在这种范式中，图的节点（Nodes）代表一个具体的计算步骤（如调用 LLM、执行工具），而边（Edges）则定义了从一个节点到另一个节点的跳转逻辑。这种设计的革命性之处在于它天然支持循环，使得构建能够进行迭代、反思和自我修正的复杂智能体工作流变得前所未有的直观和简单。

要理解 LangGraph，我们需要先掌握它的三个基本构成要素。

（1）全局状态（State）

整个图的执行过程都围绕一个共享的状态对象进行。这个状态通常被定义为一个 Python 的 `TypedDict`，它可以包含任何你需要追踪的信息，如对话历史、中间结果、迭代次数等。所有的节点都能读取和更新这个中心状态。

```python
from typing import TypedDict, List

# 定义全局状态的数据结构
class AgentState(TypedDict):
    messages: List[str]      # 对话历史
    current_task: str        # 当前任务
    final_answer: str        # 最终答案
    # ... 任何其他需要追踪的状态
```

（2）节点（Nodes）

每个节点都是一个接收当前状态作为输入、并返回一个更新后的状态作为输出的 Python 函数。节点是执行具体工作的单元。

```python
# 定义一个“规划者”节点函数
def planner_node(state: AgentState) -> AgentState:
    """根据当前任务制定计划，并更新状态。"""
    current_task = state["current_task"]
    # ... 调用LLM生成计划 ...
    plan = f"为任务 '{current_task}' 生成的计划..."
    
    # 将新消息追加到状态中
    state["messages"].append(plan)
    return state

# 定义一个“执行者”节点函数
def executor_node(state: AgentState) -> AgentState:
    """执行最新计划，并更新状态。"""
    latest_plan = state["messages"][-1]
    # ... 执行计划并获得结果 ...
    result = f"执行计划 '{latest_plan}' 的结果..."
    
    state["messages"].append(result)
    return state
```

（3）边（Edges）

边负责连接节点，定义工作流的方向。最简单的边是常规边，它指定了一个节点的输出总是流向另一个固定的节点。而 LangGraph 最强大的功能在于条件边（Conditional Edges）。它通过一个函数来判断当前的状态，然后动态地决定下一步应该跳转到哪个节点。这正是实现循环和复杂逻辑分支的关键。

```python
def should_continue(state: AgentState) -> str:
    """条件函数：根据状态决定下一步路由。"""
    # 假设如果消息少于3条，则需要继续规划
    if len(state["messages"]) < 3:
        # 返回的字符串需要与添加条件边时定义的键匹配
        return "continue_to_planner"
    else:
        state["final_answer"] = state["messages"][-1]
        return "end_workflow"
```

在定义了状态、节点和边之后，我们可以像搭积木一样将它们组装成一个可执行的工作流。

```python
from langgraph.graph import StateGraph, END

# 初始化一个状态图，并绑定我们定义的状态结构
workflow = StateGraph(AgentState)

# 将节点函数添加到图中
workflow.add_node("planner", planner_node)
workflow.add_node("executor", executor_node)

# 设置图的入口点
workflow.set_entry_point("planner")

# 添加常规边，连接 planner 和 executor
workflow.add_edge("planner", "executor")

# 添加条件边，实现动态路由
workflow.add_conditional_edges(
    # 起始节点
    "executor",
    # 判断函数
    should_continue,
    # 路由映射：将判断函数的返回值映射到目标节点
    {
        "continue_to_planner": "planner", # 如果返回"continue_to_planner"，则跳回planner节点
        "end_workflow": END               # 如果返回"end_workflow"，则结束流程
    }
)

# 编译图，生成可执行的应用
app = workflow.compile()

# 运行图
inputs = {"current_task": "分析最近的AI行业新闻", "messages": []}
for event in app.stream(inputs):
    print(event)
```

**6.5.2 案例-三步问答助手**

在理解了 LangGraph 的核心概念之后，我们将通过一个实战案例来巩固所学。我们将构建一个简化的问答对话助手，它会遵循一个清晰、固定的三步流程来回答用户的问题：

1. 理解 (Understand)：首先，分析用户的查询意图。
2. 搜索 (Search)：然后，模拟搜索与意图相关的信息。
3. 回答 (Answer)：最后，基于意图和搜索到的信息，生成最终答案。

这个案例将清晰地展示如何定义状态、创建节点以及将它们线性地连接成一个完整的工作流。我们将代码分解为四个核心步骤：定义状态、创建节点、构建图、以及运行应用。

（1）定义全局状态

首先，我们需要定义一个贯穿整个工作流的全局状态。**这是一个共享的数据结构，它在图的每个节点之间传递，作为工作流的持久化上下文**。 每个节点都可以读取该结构中的数据，并对其进行更新。

```python
from typing import TypedDict, Annotated
from langgraph.graph.message import add_messages

class SearchState(TypedDict):
    messages: Annotated[list, add_messages]
    user_query: str      # 经过LLM理解后的用户需求总结
    search_query: str    # 优化后用于Tavily API的搜索查询
    search_results: str  # Tavily搜索返回的结果
    final_answer: str    # 最终生成的答案
    step: str            # 标记当前步骤
```

我们创建了 `SearchState` 这个 `TypedDict`，为状态对象定义了一个清晰的数据模式（Schema）。一个关键的设计是同时包含了 `user_query` 和 `search_query` 字段。这允许智能体先将用户的自然语言提问，优化成更适合搜索引擎的精炼关键词，从而显著提升搜索结果的质量。

（2）定义工作流节点

定义好状态结构后，下一步是创建构成我们工作流的各个节点。在 LangGraph 中，每个节点都是一个执行具体任务的 Python 函数。这些函数接收当前的状态对象作为输入，并返回一个包含更新后字段的字典。

在开始定义节点之前，我们先完成项目的初始化设置，包括加载环境变量和实例化大语言模型。

```python
import os
from dotenv import load_dotenv
from langchain_openai import ChatOpenAI
from langchain_core.messages import HumanMessage, AIMessage, SystemMessage
from tavily import TavilyClient

# 加载 .env 文件中的环境变量
load_dotenv()

# 初始化模型
# 我们将使用这个 llm 实例来驱动所有节点的智能
llm = ChatOpenAI(
    model=os.getenv("LLM_MODEL_ID", "gpt-4o-mini"),
    api_key=os.getenv("LLM_API_KEY"),
    base_url=os.getenv("LLM_BASE_URL", "https://api.openai.com/v1"),
    temperature=0.7
)
# 初始化Tavily客户端
tavily_client = TavilyClient(api_key=os.getenv("TAVILY_API_KEY"))
```

**理解与查询节点**：此节点是工作流的第一步，此节点的职责是理解用户意图，并为其生成一个最优化的搜索查询。

```python
def understand_query_node(state: SearchState) -> dict:
    """步骤1：理解用户查询并生成搜索关键词"""
    user_message = state["messages"][-1].content
    
    understand_prompt = f"""分析用户的查询："{user_message}"
请完成两个任务：
1. 简洁总结用户想要了解什么
2. 生成最适合搜索引擎的关键词（中英文均可，要精准）

格式：
理解：[用户需求总结]
搜索词：[最佳搜索关键词]"""

    response = llm.invoke([SystemMessage(content=understand_prompt)])
    response_text = response.content
    
    # 解析LLM的输出，提取搜索关键词
    search_query = user_message # 默认使用原始查询
    if "搜索词：" in response_text:
        search_query = response_text.split("搜索词：")[1].strip()
    
    return {
        "user_query": response_text,
        "search_query": search_query,
        "step": "understood",
        "messages": [AIMessage(content=f"我将为您搜索：{search_query}")]
    }
```

该节点通过一个结构化的提示，要求 LLM 同时完成“意图理解”和“关键词生成”两个任务，并将解析出的专用搜索关键词更新到状态的 search_query 字段中，为下一步的精确搜索做好准备。

**搜索节点**：负责执行智能体的“工具使用”能力，它将调用 Tavily API 进行真实的互联网搜索，并具备基础的错误处理功能

```python
def tavily_search_node(state: SearchState) -> dict:
    """步骤2：使用Tavily API进行真实搜索"""
    search_query = state["search_query"]
    try:
        print(f"🔍 正在搜索: {search_query}")
        response = tavily_client.search(
            query=search_query, search_depth="basic", max_results=5, include_answer=True
        )
        # ... (处理和格式化搜索结果) ...
        search_results = ... # 格式化后的结果字符串
        
        return {
            "search_results": search_results,
            "step": "searched",
            "messages": [AIMessage(content="✅ 搜索完成！正在整理答案...")]
        }
    except Exception as e:
        # ... (处理错误) ...
        return {
            "search_results": f"搜索失败：{e}",
            "step": "search_failed",
            "messages": [AIMessage(content="❌ 搜索遇到问题...")]
        }
```

此节点通过 `tavily_client.search` 发起真实的 API 调用。它被包裹在 `try...except` 块中，用于捕获可能的异常。如果搜索失败，它会更新 `step` 状态为 "`search_failed`"，这个状态将被下一个节点用来触发备用方案。

**回答节点**：最后的回答节点能够根据上一步的搜索是否成功，来选择不同的回答策略，具备了一定的弹性。

```python
def generate_answer_node(state: SearchState) -> dict:
    """步骤3：基于搜索结果生成最终答案"""
    if state["step"] == "search_failed":
        # 如果搜索失败，执行回退策略，基于LLM自身知识回答
        fallback_prompt = f"搜索API暂时不可用，请基于您的知识回答用户的问题：\n用户问题：{state['user_query']}"
        response = llm.invoke([SystemMessage(content=fallback_prompt)])
    else:
        # 搜索成功，基于搜索结果生成答案
        answer_prompt = f"""基于以下搜索结果为用户提供完整、准确的答案：
用户问题：{state['user_query']}
搜索结果：\n{state['search_results']}
请综合搜索结果，提供准确、有用的回答..."""
        response = llm.invoke([SystemMessage(content=answer_prompt)])
    
    return {
        "final_answer": response.content,
        "step": "completed",
        "messages": [AIMessage(content=response.content)]
    }
```

该节点通过检查 `state["step"]` 的值来执行条件逻辑。如果搜索失败，它会利用 LLM 的内部知识回答并告知用户情况。如果搜索成功，它则会使用包含实时搜索结果的提示，来生成一个有时效性且有据可依的回答。

（3）构建图

```python
from langgraph.graph import StateGraph, START, END
from langgraph.checkpoint.memory import InMemorySaver

def create_search_assistant():
    workflow = StateGraph(SearchState)
    
    # 添加节点
    workflow.add_node("understand", understand_query_node)
    workflow.add_node("search", tavily_search_node)
    workflow.add_node("answer", generate_answer_node)
    
    # 设置线性流程
    workflow.add_edge(START, "understand")
    workflow.add_edge("understand", "search")
    workflow.add_edge("search", "answer")
    workflow.add_edge("answer", END)
    
    # 编译图
    memory = InMemorySaver()
    app = workflow.compile(checkpointer=memory)
    return app
```

（4）运行案例展示

运行此脚本后，用户可以提出一些需要实时信息的问题，例如第一章中的案例：`明天我要去北京，天气怎么样？有合适的景点吗`

会看到终端清晰地展示出智能体的“思考”过程：

<details>
<summary>点击展开运行结果</summary>

```
🔍 智能搜索助手启动！
我会使用Tavily API为您搜索最新、最准确的信息
支持各种问题：新闻、技术、知识问答等
(输入 'quit' 退出)

🤔 您想了解什么: 明天我要去北京，天气怎么样？有合适的景点吗

============================================================
🧠 理解阶段: 我理解您的需求：理解：用户想了解明天北京的天气情况以及适合游玩的景点推荐。  
搜索词：北京 明天 天气 景点推荐 (Beijing weather tomorrow attractions)
🔍 正在搜索: 北京 明天 天气 景点推荐 (Beijing weather tomorrow attractions)
🔍 搜索阶段: ✅ 搜索完成！找到了相关信息，正在为您整理答案...

💡 最终回答:
明天（2026年8月24日）北京的天气预报是阴天，预计最高气温为23°C，最低气温约为22°C。由于天气变化较大，建议您穿着适合的层次服装，以应对可能的气温波动和湿度。

### 适合游玩的景点推荐
1. **故宫（Forbidden City）**：这是中国最著名的历史遗迹之一，拥有丰富的文化和历史背景，值得一游。
2. **慕田峪长城（Great Wall at Mutianyu）**：相对较少游客，景色壮观，是体验长城的绝佳地点。
3. **天坛（Temple of Heaven）**：这是一处重要的宗教和文化遗址，典雅的建筑和广阔的公园适合散步和放松。
4. **颐和园（Summer Palace）**：这是一个结合了自然美景与古代建筑的皇家园林，非常适合游览和拍照。

### 建议
- 由于天气较为阴沉，出门时可以携带一把轻便的雨伞，以防突如其来的小雨。
- 建议穿着舒适的鞋子，因为这些景点通常需要较多的步行。

希望以上信息能帮助您更好地规划明天的行程！如果您还有其他问题或需要更多信息，请随时询问。

============================================================

🤔 您想了解什么: 
```

</details>

并且他是一个可以持续交互的助手，你也可以继续向他发问。


**6.5.3 LangGraph 的优势与局限性分析**

（1）优势

- LangGraph 将一个完整的实时问答流程，显式地定义为一个由状态、节点和边构成的“流程图”。这种设计的最大优势是高度的可控性与可预测性。
- 此外，由于每个节点都是一个独立的 Python 函数，这带来了高度的模块化。
- 同时，在流程中插入一个等待人类审核的节点也变得非常直接，为实现可靠的“人机协作”（Human-in-the-loop）提供了坚实的基础。

（2）局限性

- 与基于对话的框架相比，LangGraph 需要开发者编写更多的前期代码（Boilerplate）。定义状态、节点、边等一系列操作，使得对于简单任务而言，开发过程显得更为繁琐。开发者需要更多地思考“如何控制流程（how）”，而不仅仅是“做什么（what）”。由于工作流是预先定义的，LangGraph 的行为虽然可控，但也缺少了对话式智能体那种动态的、“涌现”式的交互。它的强项在于执行一个确定的、可靠的流程，而非模拟开放式的、不可预测的社会性协作。

- 调试过程同样存在挑战。虽然流程比对话历史更清晰，但问题可能出在多个环节：某个节点内部的逻辑错误、在节点间传递的状态数据发生异变，或是边跳转的条件判断失误。这要求开发者对整个图的运行机制有全局性的理解。

## 七、构建你的智能体框架

### 7.1 **框架整体架构设计**

**7.1.1 为何需要自建Agent框架**

（1）市面框架的快速迭代与局限性

（2）从使用者到构建者的能力跃迁

（3）定制化需求与深度掌握的必要性

**7.1.2 HelloAgents框架的设计理念**

构建一个新的 Agent 框架，关键不在于功能的多少，而在于设计理念是否能真正解决现有框架的痛点。

当你初次接触任何成熟的框架时，可能会被其丰富的功能所吸引，但很快就会发现一个问题：要完成一个简单的任务，往往需要理解 Chain、Agent、Tool、Memory、Retriever 等十几个不同的概念。每个概念都有自己的抽象层，学习曲线变得异常陡峭。

这种复杂性虽然带来了强大的功能，但也成为了初学者的障碍。

HelloAgents 框架试图在功能完整性和学习友好性之间找到平衡点，形成了四个核心的设计理念。

（1）轻量级与教学友好的平衡

（2）基于标准API的务实选择

（3）渐进式学习路径的精心设计

（4）统一的“工具”抽象：万物皆为工具

**7.1.3 本章学习目标**

```python
hello-agents/
├── hello_agents/
│   │
│   ├── core/                     # 核心框架层
│   │   ├── agent.py              # Agent基类
│   │   ├── llm.py                # HelloAgentsLLM统一接口
│   │   ├── message.py            # 消息系统
│   │   ├── config.py             # 配置管理
│   │   └── exceptions.py         # 异常体系
│   │
│   ├── agents/                   # Agent实现层
│   │   ├── simple_agent.py       # SimpleAgent实现
│   │   ├── react_agent.py        # ReActAgent实现
│   │   ├── reflection_agent.py   # ReflectionAgent实现
│   │   └── plan_solve_agent.py   # PlanAndSolveAgent实现
│   │
│   ├── tools/                    # 工具系统层
│   │   ├── base.py               # 工具基类
│   │   ├── registry.py           # 工具注册机制
│   │   ├── chain.py              # 工具链管理系统
│   │   ├── async_executor.py     # 异步工具执行器
│   │   └── builtin/              # 内置工具集
│   │       ├── calculator.py     # 计算工具
│   │       └── search.py         # 搜索工具
└──
```

**快速开始：安装 HelloAgents 框架**

```bash
# hello-agents 框架代码可见链接：https://github.com/jjyaoao/helloagents
# Python 版本需要>=3.10
pip install "hello-agents==0.1.1"
```

**体验使用 Hello-agents 构建简单智能体**

<details>
<summary>chapter7/.env</summary>

```
# ============================================================================
# HelloAgents 统一环境变量配置文件
# ============================================================================
# 复制此文件为 .env 并填入你的API密钥
# 系统要求：Python 3.10+ （必需）

# ============================================================================
# 🚀 统一配置格式（推荐）- 框架自动检测provider
# ============================================================================
# 只需配置以下4个通用环境变量，框架会自动识别LLM提供商：

# 模型名称
LLM_MODEL_ID=your-model-name

# API密钥
LLM_API_KEY=your-api-key-here

# 服务地址
LLM_BASE_URL=your-api-base-url

# 超时时间（可选，默认60秒）
LLM_TIMEOUT=60

# ============================================================================
# 🛠️ 工具配置（可选）
# ============================================================================

# Tavily搜索（推荐）- 获取API密钥：https://tavily.com/
# TAVILY_API_KEY=tvly-your_tavily_key_here

# SerpApi搜索（备选）- 获取API密钥：https://serpapi.com/
# SERPAPI_API_KEY=your_serpapi_key_here
```

</details>

<details>
<summary>chapter7/example.py</summary>

```python
# 配置好同级文件夹下.env中的大模型API, 可参考code文件夹配套的.env.example，也可以拿前几章的案例的.env文件复用。
from hello_agents import SimpleAgent, HelloAgentsLLM
from dotenv import load_dotenv

# 加载环境变量
load_dotenv()

# 创建LLM实例 - 框架自动检测provider
llm = HelloAgentsLLM()

# 或手动指定provider（可选）
# llm = HelloAgentsLLM(provider="modelscope")

# 创建SimpleAgent
agent = SimpleAgent(
    name="AI助手",
    llm=llm,
    system_prompt="你是一个有用的AI助手"
)

# 基础对话
response = agent.run("你好！请介绍一下自己")
print(response)

# 添加工具功能（可选）
from hello_agents.tools import CalculatorTool
calculator = CalculatorTool()
# 需要实现7.4.1的MySimpleAgent进行调用，后续章节会支持此类调用方式
# agent.add_tool(calculator)

# 现在可以使用工具了
response = agent.run("请帮我计算 2 + 3 * 4")
print(response)

# 查看对话历史
print(f"历史消息数: {len(agent.get_history())}")
```

</details>

### 7.2 **HelloAgentsLLM 扩展**

本节内容将在第 4.1.3 节创建的 HelloAgentsLLM 基础上进行迭代升级。我们将把这个基础客户端，改造为一个更具适应性的模型调用中枢。本次升级主要围绕以下三个目标展开：

1. 多提供商支持：实现对 OpenAI、ModelScope、智谱 AI 等多种主流 LLM 服务商的无缝切换，避免框架与特定供应商绑定。
2. 本地模型集成：引入 VLLM 和 Ollama 这两种高性能本地部署方案。
3. 自动检测机制：建立一套自动识别机制，使框架能根据环境信息智能推断所使用的 LLM 服务类型，简化用户的配置过程。

**7.2.1 支持多提供商**

（1）创建自定义LLM类并**继承**

（2）**重写** `__init__` 方法以支持新供应商

当用户传入 `provider="modelscope"` 时，执行我们自定义的逻辑；否则，就调用父类 `HelloAgentsLLM` 的原始逻辑，使其能够继续支持 OpenAI 等其他内置的供应商。

<details>
<summary>chapter7/my_llm.py</summary>

```python
# my_llm.py
import os
from typing import Optional
from openai import OpenAI
from hello_agents import HelloAgentsLLM

class MyLLM(HelloAgentsLLM):
    """
    一个自定义的LLM客户端，通过继承增加了对ModelScope的支持。
    """
    def __init__(
        self,
        model: Optional[str] = None,
        api_key: Optional[str] = None,
        base_url: Optional[str] = None,
        provider: Optional[str] = "auto",
        **kwargs
    ):
        # 检查provider是否为我们想处理的'modelscope'
        if provider == "modelscope":
            print("正在使用自定义的 ModelScope Provider")
            self.provider = "modelscope"
            
            # 解析 ModelScope 的凭证
            self.api_key = api_key or os.getenv("MODELSCOPE_API_KEY")
            self.base_url = base_url or "https://api-inference.modelscope.cn/v1/"
            
            # 验证凭证是否存在
            if not self.api_key:
                raise ValueError("ModelScope API key not found. Please set MODELSCOPE_API_KEY environment variable.")

            # 设置默认模型和其他参数
            self.model = model or os.getenv("LLM_MODEL_ID") or "Qwen/Qwen2.5-VL-72B-Instruct"
            self.temperature = kwargs.get('temperature', 0.7)
            self.max_tokens = kwargs.get('max_tokens')
            self.timeout = kwargs.get('timeout', 60)
            
            # 使用获取的参数创建OpenAI客户端实例
            self._client = OpenAI(api_key=self.api_key, base_url=self.base_url, timeout=self.timeout)

        else:
            # 如果不是 modelscope, 则完全使用父类的原始逻辑来处理
            super().__init__(model=model, api_key=api_key, base_url=base_url, provider=provider, **kwargs)
```

</details>

（3）测试自定义的 `MyLLM` 类

<details>
<summary>chapter7/my_main.py</summary>

```python
# my_main.py
from dotenv import load_dotenv
from my_llm import MyLLM # 注意:这里导入我们自己的类

# 加载环境变量
load_dotenv()

# 实例化我们重写的客户端，并指定provider
llm = MyLLM(provider="modelscope") 

# 准备消息
messages = [{"role": "user", "content": "你好，请介绍一下你自己。"}]

# 发起调用，think等方法都已从父类继承，无需重写
response_stream = llm.think(messages)

# 打印响应
print("ModelScope Response:")
for chunk in response_stream:
    # chunk在my_llm库中已经打印过一遍，这里只需要pass即可
    # print(chunk, end="", flush=True)
    pass
```

</details>

通过以上步骤，我们就在不修改 `hello-agents` 库源码的前提下，成功为其扩展了新的功能。这种方法不仅保证了代码的整洁和可维护性，也使得未来升级 `hello-agents` 库时，我们的定制化功能不会丢失。

**7.2.2 本地模型调用**

为了在本地实现高性能、生产级的模型推理服务，社区涌现出了 VLLM 和 Ollama 等优秀工具。它们通过连续批处理、PagedAttention 等技术，显著提升了模型的吞吐量和运行效率，并将模型封装为兼容 OpenAI 标准的 API 服务。这意味着，我们可以将它们无缝地集成到 `HelloAgentsLLM` 中。

**VLLM**

VLLM 是一个为 LLM 推理设计的高性能 Python 库。它通过 PagedAttention 等先进技术，可以实现比标准 Transformers 实现高出数倍的吞吐量。下面是在本地部署一个 VLLM 服务的完整步骤：

首先，需要根据你的硬件环境（特别是 CUDA 版本）安装 VLLM。

```bash
pip install vllm
```

安装完成后，使用以下命令即可启动一个兼容 OpenAI 的 API 服务。VLLM 会自动从 Hugging Face Hub 下载指定的模型权重（如果本地不存在）。我们依然以 Qwen1.5-0.5B-Chat 模型为例：

```bash
# 启动 VLLM 服务，并加载 Qwen1.5-0.5B-Chat 模型
python -m vllm.entrypoints.openai.api_server \
    --model Qwen/Qwen1.5-0.5B-Chat \
    --host 0.0.0.0 \
    --port 8000
```

服务启动后，便会在 http://localhost:8000/v1 地址上提供与 OpenAI 兼容的 API。

**Ollama**

Ollama 进一步简化了本地模型的管理和部署，它将模型下载、配置和服务启动等步骤封装到了一条命令中，非常适合快速上手。访问 [Ollama 官方网站](https://ollama.com/)下载并安装适用于你操作系统的客户端。

安装后，打开终端，执行以下命令即可下载并运行一个模型（以 Llama 3 为例）。Ollama 会自动处理模型的下载、服务封装和硬件加速配置。

```bash
# 首次运行会自动下载模型，之后会直接启动服务
ollama run llama3
```

当你在终端看到模型的交互提示时，即表示服务已经成功在后台启动。Ollama 默认会在 `http://localhost:11434/v1` 地址上暴露 OpenAI 兼容的 API 接口。

**接入 `HelloAgentsLLM`**

由于 VLLM 和 Ollama 都遵循了行业标准 API，将它们接入 `HelloAgentsLLM` 的过程非常简单。我们只需在实例化客户端时，将它们视为一个新的 `provider` 即可。

例如，连接本地运行的 VLLM 服务：

```python
llm_client = HelloAgentsLLM(
    provider="vllm",
    model="Qwen/Qwen1.5-0.5B-Chat", # 需与服务启动时指定的模型一致
    base_url="http://localhost:8000/v1",
    api_key="vllm" # 本地服务通常不需要真实API Key，可填任意非空字符串
)
```

或者，通过设置环境变量并让客户端自动检测，实现代码的零修改：

```python
# 在 .env 文件中设置
LLM_BASE_URL="http://localhost:8000/v1"
LLM_API_KEY="vllm"

# Python 代码中直接实例化即可
llm_client = HelloAgentsLLM() # 将自动检测为 vllm
```

同理，连接本地的 Ollama 服务也一样简单：

```python
llm_client = HelloAgentsLLM(
    provider="ollama",
    model="llama3", # 需与 `ollama run` 指定的模型一致
    base_url="http://localhost:11434/v1",
    api_key="ollama" # 本地服务同样不需要真实 Key
)
```

通过这种统一的设计，我们的智能体核心代码无需任何修改，就可以在云端 API 和本地模型之间自由切换。

这为后续应用的开发、部署、成本控制以及保护数据隐私提供了极大的灵活性。

**7.2.3 自动检测机制**

为了尽可能减少用户的配置负担并遵循“约定优于配置”的原则，`HelloAgentsLLM` 内部设计了两个核心辅助方法：`_auto_detect_provider` 和 `_resolve_credentials`。

它们协同工作，`_auto_detect_provider` 负责根据环境信息推断服务商，而 `_resolve_credentials` 则根据推断结果完成具体的参数配置。

`_auto_detect_provider` 方法负责根据环境信息，按照下述优先级顺序，尝试自动推断服务商：

1. **最高优先级**：**检查特定服务商的环境变量**。框架会依次检查 `MODELSCOPE_API_KEY`, `OPENAI_API_KEY`, `ZHIPU_API_KEY` 等环境变量是否存在。一旦发现任何一个，就会立即确定对应的服务商。
2. **次高优先级**：**根据 `base_url` 进行判断** 如果用户没有设置特定服务商的密钥，但设置了通用的 LLM_BASE_URL，框架会转而解析这个 URL。
    - 域名匹配：通过检查 URL 中是否包含 `"api-inference.modelscope.cn"`, `"api.openai.com"` 等特征字符串来识别云服务商。
    - 端口匹配：通过检查 URL 中是否包含 `:11434` (Ollama), `:8000` (VLLM) 等本地服务的标准端口来识别本地部署方案。
3. **辅助判断**：**分析 API 密钥的格式**。可能存在模糊性，因此它的优先级较低，仅作为辅助手段。

其部分关键代码如下：

<details>
<summary>hello_agents/core/llm.py 中 HelloAgentsLLM 类的 _auto_detect_provider 私有函数</summary>


```python
def _auto_detect_provider(self, api_key: Optional[str], base_url: Optional[str]) -> str:
    """
    自动检测LLM提供商
    """
    # 1. 检查特定提供商的环境变量 (最高优先级)
    if os.getenv("MODELSCOPE_API_KEY"): return "modelscope"
    if os.getenv("OPENAI_API_KEY"): return "openai"
    if os.getenv("ZHIPU_API_KEY"): return "zhipu"
    # ... 其他服务商的环境变量检查

    # 获取通用的环境变量
    actual_api_key = api_key or os.getenv("LLM_API_KEY")
    actual_base_url = base_url or os.getenv("LLM_BASE_URL")

    # 2. 根据 base_url 判断
    if actual_base_url:
        base_url_lower = actual_base_url.lower()
        if "api-inference.modelscope.cn" in base_url_lower: return "modelscope"
        if "open.bigmodel.cn" in base_url_lower: return "zhipu"
        if "localhost" in base_url_lower or "127.0.0.1" in base_url_lower:
            if ":11434" in base_url_lower: return "ollama"
            if ":8000" in base_url_lower: return "vllm"
            return "local" # 其他本地端口

    # 3. 根据 API 密钥格式辅助判断
    if actual_api_key:
        if actual_api_key.startswith("ms-"): return "modelscope"
        # ... 其他密钥格式判断

    # 4. 默认返回 'auto'，使用通用配置
    return "auto"
```

</details>

一旦 provider 被确定（无论是用户指定还是自动检测），`_resolve_credentials` 方法便会接手处理服务商的差异化配置。

它会根据 `provider` 的值，去主动查找对应的环境变量，并为其设置默认的 `base_url`。其部分关键实现如下：

<details>
<summary>hello_agents/core/llm.py 中 HelloAgentsLLM 类的 _resolve_credentials 私有函数</summary>

```python
def _resolve_credentials(self, api_key: Optional[str], base_url: Optional[str]) -> tuple[str, str]:
    """根据provider解析API密钥和base_url"""
    if self.provider == "openai":
        resolved_api_key = api_key or os.getenv("OPENAI_API_KEY") or os.getenv("LLM_API_KEY")
        resolved_base_url = base_url or os.getenv("LLM_BASE_URL") or "https://api.openai.com/v1"
        return resolved_api_key, resolved_base_url

    elif self.provider == "modelscope":
        resolved_api_key = api_key or os.getenv("MODELSCOPE_API_KEY") or os.getenv("LLM_API_KEY")
        resolved_base_url = base_url or os.getenv("LLM_BASE_URL") or "https://api-inference.modelscope.cn/v1/"
        return resolved_api_key, resolved_base_url
    
    # ... 其他服务商的逻辑
```

</details>

让我们通过一个简单的例子来感受自动检测带来的便利。假设一个用户想要使用本地的 Ollama 服务，他只需在 .env 文件中进行如下配置：

```
LLM_BASE_URL="http://localhost:11434/v1"
LLM_MODEL_ID="llama3"
```

他完全不需要配置 `LLM_API_KEY` 或在代码中指定 `provider`。然后，在 `Python` 代码中，他只需简单地实例化 `HelloAgentsLLM` 即可：

```python
from dotenv import load_dotenv
from hello_agents import HelloAgentsLLM

load_dotenv()

# 无需传入 provider，框架会自动检测
llm = HelloAgentsLLM() 
# 框架内部日志会显示检测到 provider 为 'ollama'

# 后续调用方式完全不变
messages = [{"role": "user", "content": "你好！"}]
for chunk in llm.think(messages):
    print(chunk, end="")
```

### 7.3 **框架接口实现**

在上节中，我们构建了 HelloAgentsLLM 这一核心组件，解决了与大语言模型通信的关键问题。不过它还需要一系列配套的接口和组件来处理数据流、管理配置、应对异常，并为上层应用的构建提供一个清晰、统一的结构。本节将讲述以下三个核心文件：

- `message.py`： 定义了框架内统一的消息格式，确保了智能体与模型之间信息传递的标准化。
- `config.py`： 提供了一个中心化的配置管理方案，使框架的行为易于调整和扩展。
- `agent.py`： 定义了所有智能体的抽象基类（Agent），为后续实现不同类型的智能体提供了统一的接口和规范。

**7.3.1 Message 类**

在智能体与大语言模型的交互中，对话历史是至关重要的上下文。为了规范地管理这些信息，我们设计了一个简易 `Message` 类。

<details>
<summary>hello_agents/core/message.py</summary>

```python
"""消息系统"""
from typing import Optional, Dict, Any, Literal
from datetime import datetime
from pydantic import BaseModel

# 定义消息角色的类型，限制其取值
MessageRole = Literal["user", "assistant", "system", "tool"]

class Message(BaseModel):
    """消息类"""
    
    content: str
    role: MessageRole
    timestamp: datetime = None
    metadata: Optional[Dict[str, Any]] = None
    
    def __init__(self, content: str, role: MessageRole, **kwargs):
        super().__init__(
            content=content,
            role=role,
            timestamp=kwargs.get('timestamp', datetime.now()),
            metadata=kwargs.get('metadata', {})
        )
    
    def to_dict(self) -> Dict[str, Any]:
        """转换为字典格式（OpenAI API格式）"""
        return {
            "role": self.role,
            "content": self.content
        }
    
    def __str__(self) -> str:
        return f"[{self.role}] {self.content}"
```

</details>


首先，我们通过 `typing.Literal` 将 `role` 字段的取值严格限制为 `"user"`, `"assistant"`, `"system"`, `"tool"` 四种，这直接对应 OpenAI API 的规范，保证了类型安全。

除了 `content` 和 `role` 这两个核心字段外，我们还增加了 `timestamp` 和 `metadata`，为日志记录和未来功能扩展预留了空间。

最后，`to_dict()` 方法是其核心功能之一，负责将内部使用的 `Message` 对象转换为与 OpenAI API 兼容的字典格式，体现了“对内丰富，对外兼容”的设计原则。

**7.3.2 Config 类**

**Config** 类的职责是将代码中硬编码配置参数集中起来，并支持从环境变量中读取。

<details>
<summary>hello_agents/core/config.py</summary>

```python
"""配置管理"""
import os
from typing import Optional, Dict, Any
from pydantic import BaseModel

class Config(BaseModel):
    """HelloAgents配置类"""
    
    # LLM配置
    default_model: str = "gpt-3.5-turbo"
    default_provider: str = "openai"
    temperature: float = 0.7
    max_tokens: Optional[int] = None
    
    # 系统配置
    debug: bool = False
    log_level: str = "INFO"
    
    # 其他配置
    max_history_length: int = 100
    
    @classmethod
    def from_env(cls) -> "Config":
        """从环境变量创建配置"""
        return cls(
            debug=os.getenv("DEBUG", "false").lower() == "true",
            log_level=os.getenv("LOG_LEVEL", "INFO"),
            temperature=float(os.getenv("TEMPERATURE", "0.7")),
            max_tokens=int(os.getenv("MAX_TOKENS")) if os.getenv("MAX_TOKENS") else None,
        )
    
    def to_dict(self) -> Dict[str, Any]:
        """转换为字典"""
        return self.dict()
```

</details>

首先，我们将配置项按逻辑划分为 `LLM配置`、`系统配置` 等，使结构一目了然。

其次，每个配置项都设有合理的默认值，保证了框架在零配置下也能工作。

最核心的是 `from_env()` 类方法，它允许用户通过设置环境变量来覆盖默认配置，无需修改代码，这在部署到不同环境时尤其有用。


**7.3.3 Agent 抽象基类**

`Agent` 类是整个框架的顶层抽象。它定义了一个智能体应该具备的通用行为和属性，但并不关心具体的实现方式。

我们通过 Python 的 `abc` (Abstract Base Classes) 模块来实现它，这强制所有具体的智能体实现（如后续章节的 `SimpleAgent`, `ReActAgent` 等）都必须遵循同一个“接口”。

<details>
<summary>hello_agents/core/agent.py</summary>

```python
"""Agent基类"""
from abc import ABC, abstractmethod
from typing import Optional, Any
from .message import Message
from .llm import HelloAgentsLLM
from .config import Config

class Agent(ABC):
    """Agent基类"""
    
    def __init__(
        self,
        name: str,
        llm: HelloAgentsLLM,
        system_prompt: Optional[str] = None,
        config: Optional[Config] = None
    ):
        self.name = name
        self.llm = llm
        self.system_prompt = system_prompt
        self.config = config or Config()
        self._history: list[Message] = []
    
    @abstractmethod
    def run(self, input_text: str, **kwargs) -> str:
        """运行Agent"""
        pass
    
    def add_message(self, message: Message):
        """添加消息到历史记录"""
        self._history.append(message)
    
    def clear_history(self):
        """清空历史记录"""
        self._history.clear()
    
    def get_history(self) -> list[Message]:
        """获取历史记录"""
        return self._history.copy()
    
    def __str__(self) -> str:
        return f"Agent(name={self.name}, provider={self.llm.provider})"
```

</details>

该类的设计体现了面向对象中的抽象原则。

首先，它通过继承 `ABC` 被定义为一个不能直接实例化的抽象类。其构造函数 `__init__` 清晰地定义了 Agent 的核心依赖：名称、LLM 实例、系统提示词和配置。

最重要的部分是使用 `@abstractmethod` 装饰的 `run` 方法，它强制所有子类必须实现此方法，从而保证了所有智能体都有统一的执行入口。

此外，基类还提供了通用的历史记录管理方法，这些方法与 `Message` 类协同工作，体现了组件间的联系。

至此，我们已经完成了 `HelloAgents` 框架核心基础组件的设计与实现。

### 7.4 **Agent范式的框架化实现**

本节内容将在第四章构建的三种经典Agent范式（ReAct、Plan-and-Solve、Reflection）基础上进行框架化重构，并新增 SimpleAgent 作为基础对话范式。

我们将把这些独立的Agent实现，改造为基于统一架构的框架组件。本次重构主要围绕以下三个核心目标展开：

1. **提示词工程的系统性提升**：对第四章中的提示词进行深度优化，从特定任务导向转向通用化设计，同时增强格式约束和角色定义。
2. **接口与格式的标准化统一**：建立统一的Agent基类和标准化的运行接口，所有Agent都遵循相同的初始化参数、方法签名和历史管理机制。
3. **高度可配置的自定义能力**：支持用户自定义提示词模板、配置参数和执行策略。

**7.4.1 SimpleAgent**

SimpleAgent 是最基础的 Agent 实现，它展示了如何在框架基础上构建一个完整的对话智能体。

我们将通过继承框架中已有的 SimpleAgent 类并重写其核心方法，来实现一个可扩展的版本。

在项目目录中创建一个`my_simple_agent.py`文件：

<details>
<summary>chapter7/my_simple_agent.py</summary>

```python
# my_simple_agent.py
from typing import Optional, Iterator
from hello_agents import SimpleAgent, HelloAgentsLLM, Config, Message,ToolRegistry

class MySimpleAgent(SimpleAgent):
    """
    重写的简单对话Agent
    展示如何基于框架基类构建自定义Agent
    """

    def __init__(
        self,
        name: str,
        llm: HelloAgentsLLM,
        system_prompt: Optional[str] = None,
        config: Optional[Config] = None,
        tool_registry: Optional['ToolRegistry'] = None,
        enable_tool_calling: bool = True
    ):
        super().__init__(name, llm, system_prompt, config)
        self.tool_registry = tool_registry
        self.enable_tool_calling = enable_tool_calling and tool_registry is not None
        print(f"✅ {name} 初始化完成，工具调用: {'启用' if self.enable_tool_calling else '禁用'}")

    def run(self, input_text: str, max_tool_iterations: int = 3, **kwargs) -> str:
        """
        重写的运行方法 - 实现简单对话逻辑，支持可选工具调用
        """
        print(f"🤖 {self.name} 正在处理: {input_text}")

        # 构建消息列表
        messages = []

        # 添加系统消息（可能包含工具信息）
        enhanced_system_prompt = self._get_enhanced_system_prompt()
        messages.append({"role": "system", "content": enhanced_system_prompt})

        # 添加历史消息
        for msg in self._history:
            messages.append({"role": msg.role, "content": msg.content})

        # 添加当前用户消息
        messages.append({"role": "user", "content": input_text})

        # 如果没有启用工具调用，使用简单对话逻辑
        if not self.enable_tool_calling:
            response = self.llm.invoke(messages, **kwargs)
            self.add_message(Message(input_text, "user"))
            self.add_message(Message(response, "assistant"))
            print(f"✅ {self.name} 响应完成")
            return response

        # 支持多轮工具调用的逻辑
        return self._run_with_tools(messages, input_text, max_tool_iterations, **kwargs)

    def _get_enhanced_system_prompt(self) -> str:
        """构建增强的系统提示词，包含工具信息"""
        base_prompt = self.system_prompt or "你是一个有用的AI助手。"

        if not self.enable_tool_calling or not self.tool_registry:
            return base_prompt

        # 获取工具描述
        tools_description = self.tool_registry.get_tools_description()
        if not tools_description or tools_description == "暂无可用工具":
            return base_prompt

        tools_section = "\n\n## 可用工具\n"
        tools_section += "你可以使用以下工具来帮助回答问题:\n"
        tools_section += tools_description + "\n"

        tools_section += "\n## 工具调用格式\n"
        tools_section += "当需要使用工具时，请使用以下格式:\n"
        tools_section += "`[TOOL_CALL:{tool_name}:{parameters}]`\n"
        tools_section += "例如:`[TOOL_CALL:search:Python编程]` 或 `[TOOL_CALL:memory:recall=用户信息]`\n\n"
        tools_section += "工具调用结果会自动插入到对话中，然后你可以基于结果继续回答。\n"

        return base_prompt + tools_section

    def _run_with_tools(self, messages: list, input_text: str, max_tool_iterations: int, **kwargs) -> str:
        """支持工具调用的运行逻辑"""
        current_iteration = 0
        final_response = ""

        while current_iteration < max_tool_iterations:
            # 调用LLM
            response = self.llm.invoke(messages, **kwargs)

            # 检查是否有工具调用
            tool_calls = self._parse_tool_calls(response)

            if tool_calls:
                print(f"🔧 检测到 {len(tool_calls)} 个工具调用")
                # 执行所有工具调用并收集结果
                tool_results = []
                clean_response = response

                for call in tool_calls:
                    result = self._execute_tool_call(call['tool_name'], call['parameters'])
                    tool_results.append(result)
                    # 从响应中移除工具调用标记
                    clean_response = clean_response.replace(call['original'], "")

                # 构建包含工具结果的消息
                messages.append({"role": "assistant", "content": clean_response})

                # 添加工具结果
                tool_results_text = "\n\n".join(tool_results)
                messages.append({"role": "user", "content": f"工具执行结果:\n{tool_results_text}\n\n请基于这些结果给出完整的回答。"})

                current_iteration += 1
                continue

            # 没有工具调用，这是最终回答
            final_response = response
            break

        # 如果超过最大迭代次数，获取最后一次回答
        if current_iteration >= max_tool_iterations and not final_response:
            final_response = self.llm.invoke(messages, **kwargs)

        # 保存到历史记录
        self.add_message(Message(input_text, "user"))
        self.add_message(Message(final_response, "assistant"))
        print(f"✅ {self.name} 响应完成")

        return final_response

    def _parse_tool_calls(self, text: str) -> list:
        """解析文本中的工具调用"""
        pattern = r'\[TOOL_CALL:([^:]+):([^\]]+)\]'
        matches = re.findall(pattern, text)

        tool_calls = []
        for tool_name, parameters in matches:
            tool_calls.append({
                'tool_name': tool_name.strip(),
                'parameters': parameters.strip(),
                'original': f'[TOOL_CALL:{tool_name}:{parameters}]'
            })

        return tool_calls

    def _execute_tool_call(self, tool_name: str, parameters: str) -> str:
        """执行工具调用"""
        if not self.tool_registry:
            return f"❌ 错误:未配置工具注册表"

        try:
            # 智能参数解析
            if tool_name == 'calculator':
                # 计算器工具直接传入表达式
                result = self.tool_registry.execute_tool(tool_name, parameters)
            else:
                # 其他工具使用智能参数解析
                param_dict = self._parse_tool_parameters(tool_name, parameters)
                tool = self.tool_registry.get_tool(tool_name)
                if not tool:
                    return f"❌ 错误:未找到工具 '{tool_name}'"
                result = tool.run(param_dict)

            return f"🔧 工具 {tool_name} 执行结果:\n{result}"

        except Exception as e:
            return f"❌ 工具调用失败:{str(e)}"

    def _parse_tool_parameters(self, tool_name: str, parameters: str) -> dict:
        """智能解析工具参数"""
        param_dict = {}

        if '=' in parameters:
            # 格式: key=value 或 action=search,query=Python
            if ',' in parameters:
                # 多个参数:action=search,query=Python,limit=3
                pairs = parameters.split(',')
                for pair in pairs:
                    if '=' in pair:
                        key, value = pair.split('=', 1)
                        param_dict[key.strip()] = value.strip()
            else:
                # 单个参数:key=value
                key, value = parameters.split('=', 1)
                param_dict[key.strip()] = value.strip()
        else:
            # 直接传入参数，根据工具类型智能推断
            if tool_name == 'search':
                param_dict = {'query': parameters}
            elif tool_name == 'memory':
                param_dict = {'action': 'search', 'query': parameters}
            else:
                param_dict = {'input': parameters}

        return param_dict
    def stream_run(self, input_text: str, **kwargs) -> Iterator[str]:
        """
        自定义的流式运行方法
        """
        print(f"🌊 {self.name} 开始流式处理: {input_text}")

        messages = []

        if self.system_prompt:
            messages.append({"role": "system", "content": self.system_prompt})

        for msg in self._history:
            messages.append({"role": msg.role, "content": msg.content})

        messages.append({"role": "user", "content": input_text})

        # 流式调用LLM
        full_response = ""
        print("📝 实时响应: ", end="")
        for chunk in self.llm.stream_invoke(messages, **kwargs):
            full_response += chunk
            print(chunk, end="", flush=True)
            yield chunk

        print()  # 换行

        # 保存完整对话到历史记录
        self.add_message(Message(input_text, "user"))
        self.add_message(Message(full_response, "assistant"))
        print(f"✅ {self.name} 流式响应完成")

    def add_tool(self, tool) -> None:
        """添加工具到Agent（便利方法）"""
        if not self.tool_registry:
            from hello_agents import ToolRegistry
            self.tool_registry = ToolRegistry()
            self.enable_tool_calling = True

        self.tool_registry.register_tool(tool)
        print(f"🔧 工具 '{tool.name}' 已添加")

    def has_tools(self) -> bool:
        """检查是否有可用工具"""
        return self.enable_tool_calling and self.tool_registry is not None
    
    def remove_tool(self, tool_name: str) -> bool:
        """移除工具（便利方法）"""
        if self.tool_registry:
            self.tool_registry.unregister(tool_name)
            return True
        return False
    
    def list_tools(self) -> list:
        """列出所有可用工具"""
        if self.tool_registry:
            return self.tool_registry.list_tools()
        return []
```

</details>

创建一个测试文件`test_simple_agent.py`：

<details>
<summary>chapter7/my_simple_agent.py</summary>

```python
# test_simple_agent.py
from dotenv import load_dotenv
from hello_agents import HelloAgentsLLM, ToolRegistry
from hello_agents.tools import CalculatorTool
from my_simple_agent import MySimpleAgent

# 加载环境变量
load_dotenv()

# 创建LLM实例
llm = HelloAgentsLLM()

# 测试1:基础对话Agent（无工具）
print("=== 测试1:基础对话 ===")
basic_agent = MySimpleAgent(
    name="基础助手",
    llm=llm,
    system_prompt="你是一个友好的AI助手，请用简洁明了的方式回答问题。"
)

response1 = basic_agent.run("你好，请介绍一下自己")
print(f"基础对话响应: {response1}\n")

# 测试2:带工具的Agent
print("=== 测试2:工具增强对话 ===")
tool_registry = ToolRegistry()
calculator = CalculatorTool()
tool_registry.register_tool(calculator)

enhanced_agent = MySimpleAgent(
    name="增强助手",
    llm=llm,
    system_prompt="你是一个智能助手，可以使用工具来帮助用户。",
    tool_registry=tool_registry,
    enable_tool_calling=True
)

response2 = enhanced_agent.run("请帮我计算 15 * 8 + 32")
print(f"工具增强响应: {response2}\n")

# 测试3:流式响应
print("=== 测试3:流式响应 ===")
print("流式响应: ", end="")
for chunk in basic_agent.stream_run("请解释什么是人工智能"):
    pass  # 内容已在stream_run中实时打印

# 测试4:动态添加工具
print("\n=== 测试4:动态工具管理 ===")
print(f"添加工具前: {basic_agent.has_tools()}")
basic_agent.add_tool(calculator)
print(f"添加工具后: {basic_agent.has_tools()}")
print(f"可用工具: {basic_agent.list_tools()}")

# 查看对话历史
print(f"\n对话历史: {len(basic_agent.get_history())} 条消息")
```

</details>

在本节中，我们通过继承 Agent 基类，成功构建了一个功能完备且遵循框架规范的基础对话智能体 MySimpleAgent。它不仅支持基础对话，还具备可选的工具调用能力、流式响应和便利的工具管理方法。

**7.4.2 ReActAgent**

框架化的 ReActAgent 在保持核心逻辑不变的同时，提升了代码的组织性和可维护性，主要是通过提示词优化和与框架工具系统的集成。

（1）提示词模板的改进

保持了原有的格式要求，强调"每次只能执行一个步骤"，避免混乱，并明确了两种Action的使用场景。

（2）重写 ReActAgent 的完整实现

<details>
<summary>chapter7/my_react_agent.py</summary>

```python
MY_REACT_PROMPT = """你是一个具备推理和行动能力的AI助手。你可以通过思考分析问题，然后调用合适的工具来获取信息，最终给出准确的答案。

## 可用工具
{tools}

## 工作流程
请严格按照以下格式进行回应，每次只能执行一个步骤：

Thought: 你的思考过程，用于分析问题、拆解任务和规划下一步行动。
Action: 你决定采取的行动，必须是以下格式之一：
- `{{tool_name}}[{{tool_input}}]` - 调用指定工具
- `Finish[最终答案]` - 当你有足够信息给出最终答案时

## 重要提醒
1. 每次回应必须包含Thought和Action两部分
2. 工具调用的格式必须严格遵循：工具名[参数]
3. 只有当你确信有足够信息回答问题时，才使用Finish
4. 如果工具返回的信息不够，继续使用其他工具或相同工具的不同参数

## 当前任务
**Question:** {question}

## 执行历史
{history}

现在开始你的推理和行动：
"""

import re
from typing import Optional, List, Tuple
from hello_agents import ReActAgent, HelloAgentsLLM, Config, Message, ToolRegistry

class MyReActAgent(ReActAgent):
    """
    重写的ReAct Agent - 推理与行动结合的智能体
    """

    def __init__(
        self,
        name: str,
        llm: HelloAgentsLLM,
        tool_registry: ToolRegistry,
        system_prompt: Optional[str] = None,
        config: Optional[Config] = None,
        max_steps: int = 5,
        custom_prompt: Optional[str] = None
    ):
        super().__init__(name, llm, system_prompt, config)
        self.tool_registry = tool_registry
        self.max_steps = max_steps
        self.current_history: List[str] = []
        self.prompt_template = custom_prompt if custom_prompt else MY_REACT_PROMPT
        print(f"✅ {name} 初始化完成，最大步数: {max_steps}")

    def run(self, input_text: str, **kwargs) -> str:
        """运行ReAct Agent"""
        self.current_history = []
        current_step = 0

        print(f"\n🤖 {self.name} 开始处理问题: {input_text}")

        while current_step < self.max_steps:
            current_step += 1
            print(f"\n--- 第 {current_step} 步 ---")

            # 1. 构建提示词
            tools_desc = self.tool_registry.get_tools_description()
            history_str = "\n".join(self.current_history)
            prompt = self.prompt_template.format(
                tools=tools_desc,
                question=input_text,
                history=history_str
            )

            # 2. 调用LLM
            messages = [{"role": "user", "content": prompt}]
            response_text = self.llm.invoke(messages, **kwargs)

            # 3. 解析输出
            thought, action = self._parse_output(response_text)

            # 4. 检查完成条件
            if action and action.startswith("Finish"):
                final_answer = self._parse_action_input(action)
                self.add_message(Message(input_text, "user"))
                self.add_message(Message(final_answer, "assistant"))
                return final_answer

            # 5. 执行工具调用
            if action:
                tool_name, tool_input = self._parse_action(action)
                observation = self.tool_registry.execute_tool(tool_name, tool_input)
                self.current_history.append(f"Action: {action}")
                self.current_history.append(f"Observation: {observation}")

        # 达到最大步数
        final_answer = "抱歉，我无法在限定步数内完成这个任务。"
        self.add_message(Message(input_text, "user"))
        self.add_message(Message(final_answer, "assistant"))
        return final_answer
```

</details>

通过以上重构，我们将 ReAct 范式成功地集成到了框架中。

核心改进在于利用了统一的 `ToolRegistry` 接口，并通过一个可配置、格式更严谨的提示词模板，提升了智能体执行思考-行动循环的稳定性。

对于 ReAct 的测试案例，由于需要调用工具，所以统一放在文末提供测试代码。

**7.4.3 ReflectionAgent**

<details>
<summary>chapter7/my_reflection_agent.py</summary>

```python
略
```

</details>

**7.4.4 PlanAndSolveAgent**

<details>
<summary>chapter7/my_plan_solve_agent.py</summary>

```python
略
```

</details>

通过这种框架化的重构，我们不仅保持了第四章中各种Agent范式的核心功能，还大幅提升了代码的组织性、可维护性和扩展性。

所有Agent现在都共享统一的基础架构，同时保持了各自的特色和优势。

![Agent不同章节实现对比.png](images%2FAgent%E4%B8%8D%E5%90%8C%E7%AB%A0%E8%8A%82%E5%AE%9E%E7%8E%B0%E5%AF%B9%E6%AF%94.png)

**7.4.5 FunctionCallAgent**

FunctionCallAgent 是 hello-agents 在 0.2.8 之后引入的 Agent，它基于 OpenAI 原生函数调用机制的 Agent，展示了如何使用 OpenAI 的函数调用机制来构建 Agent。 

支持以下功能：

* `_build_tool_schemas`：通过工具的 description 构建 OpenAI 的 function calling schema
* `_extract_message_content`：从 OpenAI 的响应中提取文本
* `_parse_function_call_arguments`: 解析模型返回的 JSON 字符串参数
* `_convert_parameter_types`: 转换参数类型

这些功能可以使其具备原生的 OpenAI Function Calling 的能力，对比使用 prompt 约束的方式，具备更强的鲁棒性。

<details>
<summary>点击展开完整代码</summary>

```python
def _invoke_with_tools(self, messages: list[dict[str, Any]], tools: list[dict[str, Any]], tool_choice: Union[str, dict], **kwargs):
        """调用底层OpenAI客户端执行函数调用"""
        client = getattr(self.llm, "_client", None)
        if client is None:
            raise RuntimeError("HelloAgentsLLM 未正确初始化客户端，无法执行函数调用。")

        client_kwargs = dict(kwargs)
        client_kwargs.setdefault("temperature", self.llm.temperature)
        if self.llm.max_tokens is not None:
            client_kwargs.setdefault("max_tokens", self.llm.max_tokens)

        return client.chat.completions.create(
            model=self.llm.model,
            messages=messages,
            tools=tools,
            tool_choice=tool_choice,
            **client_kwargs,
        )

#内部逻辑是对Openai 原生的functioncall作再封装
#OpenAI 原生functioncall示例
from openai import OpenAI
client = OpenAI()

tools = [
  {
    "type": "function",
    "function": {
      "name": "get_current_weather",
      "description": "Get the current weather in a given location",
      "parameters": {
        "type": "object",
        "properties": {
          "location": {
            "type": "string",
            "description": "The city and state, e.g. San Francisco, CA",
          },
          "unit": {"type": "string", "enum": ["celsius", "fahrenheit"]},
        },
        "required": ["location"],
      },
    }
  }
]
messages = [{"role": "user", "content": "What's the weather like in Boston today?"}]
completion = client.chat.completions.create(
  model="gpt-5",
  messages=messages,
  tools=tools,
  tool_choice="auto"
)

print(completion)
```

</details>

### 7.5 **工具系统**

本节内容将在前面构建的Agent基础架构上，深入探讨工具系统的设计与实现。我们将从基础设施建设开始，逐步深入到自定义开发设计。本节的学习目标围绕以下三个核心方面展开：

* **统一的工具抽象与管理**：建立标准化的Tool基类和ToolRegistry注册机制，为工具的开发、注册、发现和执行提供统一的基础设施。
* **实战驱动的工具开发**：以数学计算工具为案例，展示如何设计和实现自定义工具，让读者掌握工具开发的完整流程。
* **高级整合与优化策略**：通过多源搜索工具的设计，展示如何整合多个外部服务，实现智能后端选择、结果合并和容错处理，体现工具系统在复杂场景下的设计思维。

**7.5.1 工具基类与注册机制设计**

在构建可扩展的工具系统时，我们需要首先建立一套标准化的基础设施。这套基础设施包括Tool基类、ToolRegistry注册表，以及工具管理机制。

（1）`Tool` 基类的抽象设计

`Tool` 基类是整个工具系统的核心抽象，它定义了所有工具必须遵循的**接口规范**：

<details>
<summary>hello_agents/tools/base.py 中的 Tool 类</summary>

```python
class Tool(ABC):
    """工具基类"""

    def __init__(self, name: str, description: str):
        self.name = name
        self.description = description

    @abstractmethod
    def run(self, parameters: Dict[str, Any]) -> str:
        """执行工具"""
        pass

    @abstractmethod
    def get_parameters(self) -> List[ToolParameter]:
        """获取工具参数定义"""
        pass
```

</details>

这个设计体现了面向对象设计的核心思想：通过统一的`run`方法接口，所有工具都能以一致的方式执行，接受字典参数并返回字符串结果，确保了框架的一致性。

同时，工具具备了自描述能力，通过`get_parameters`方法能够清晰地告诉调用者自己需要什么参数，这种内省机制为自动化文档生成和参数验证提供了基础。

而`name`和`description`等元数据的设计，则让工具系统具备了良好的可发现性和可理解性。

（2）`ToolParameter` 参数定义系统

为了支持复杂的参数验证和文档生成，我们设计了 `ToolParameter` 类：

<details>
<summary>hello_agents/tools/base.py 中的 ToolParameter 类</summary>

```python
class ToolParameter(BaseModel):
    """工具参数定义"""
    name: str
    type: str
    description: str
    required: bool = True
    default: Any = None
```

</details>

这种设计让工具能够精确描述自己的参数需求，支持类型检查、默认值设置和文档自动生成。

（3）`ToolRegistry` 注册表的实现

`ToolRegistry` 是工具系统的管理中枢，它提供了**工具的注册、发现、执行**等核心功能，在这一节我们主要用到以下功能：

<details>
<summary>hello_agents/tools/registry.py 中的 ToolRegistry 类</summary>

```python
class ToolRegistry:
    """HelloAgents工具注册表"""

    def __init__(self):
        self._tools: dict[str, Tool] = {}
        self._functions: dict[str, dict[str, Any]] = {}

    def register_tool(self, tool: Tool):
        """注册Tool对象"""
        if tool.name in self._tools:
            print(f"⚠️ 警告:工具 '{tool.name}' 已存在，将被覆盖。")
        self._tools[tool.name] = tool
        print(f"✅ 工具 '{tool.name}' 已注册。")
        
    def register_function(self, name: str, description: str, func: Callable[[str], str]):
        """
        直接注册函数作为工具（简便方式）

        Args:
            name: 工具名称
            description: 工具描述
            func: 工具函数，接受字符串参数，返回字符串结果
        """
        if name in self._functions:
            print(f"⚠️ 警告:工具 '{name}' 已存在，将被覆盖。")

        self._functions[name] = {
            "description": description,
            "func": func
        }
        print(f"✅ 工具 '{name}' 已注册。")
```

</details>

`ToolRegistry` 支持两种注册方式：

1. **`Tool` 对象注册**：适合复杂工具，支持完整的参数定义和验证
2. **函数直接注册**：适合简单工具，快速集成现有函数

（4）工具发现与管理机制

注册表提供了丰富的工具管理功能：

<details>
<summary>hello_agents/tools/registry.py 中 ToolRegistry 类的 get_tools_description 函数</summary>

```python
def get_tools_description(self) -> str:
    """获取所有可用工具的格式化描述字符串"""
    descriptions = []

    # Tool对象描述
    for tool in self._tools.values():
        descriptions.append(f"- {tool.name}: {tool.description}")

    # 函数工具描述
    for name, info in self._functions.items():
        descriptions.append(f"- {name}: {info['description']}")

    return "\n".join(descriptions) if descriptions else "暂无可用工具"
```

</details>

这个方法生成的描述字符串可以直接用于构建 Agent 的提示词，让 Agent 了解可用的工具。

<details>
<summary>点击展开完整代码</summary>

```python
def to_openai_schema(self) -> Dict[str, Any]:
        """转换为 OpenAI function calling schema 格式

        用于 FunctionCallAgent，使工具能够被 OpenAI 原生 function calling 使用

        Returns:
            符合 OpenAI function calling 标准的 schema
        """
        parameters = self.get_parameters()

        # 构建 properties
        properties = {}
        required = []

        for param in parameters:
            # 基础属性定义
            prop = {
                "type": param.type,
                "description": param.description
            }

            # 如果有默认值，添加到描述中（OpenAI schema 不支持 default 字段）
            if param.default is not None:
                prop["description"] = f"{param.description} (默认: {param.default})"

            # 如果是数组类型，添加 items 定义
            if param.type == "array":
                prop["items"] = {"type": "string"}  # 默认字符串数组

            properties[param.name] = prop

            # 收集必需参数
            if param.required:
                required.append(param.name)

        return {
            "type": "function",
            "function": {
                "name": self.name,
                "description": self.description,
                "parameters": {
                    "type": "object",
                    "properties": properties,
                    "required": required
                }
            }
        }
```

</details>

这个方法生成的 schema 可以直接用于原生的 OpenAI SDK 的工具调用。

**7.5.2 自定义工具开发**

有了基础设施后，我们来看看如何开发一个完整的自定义工具。数学计算工具是一个很好的例子，因为它简单直观，最直接的方式是使用 `ToolRegistry` 的函数注册功能。

让我们创建一个自定义的数学计算工具。首先，在你的项目目录中创建`my_calculator_tool.py`：

<details>
<summary>chapter7/my_calculator_tool.py</summary>

```python
# my_calculator_tool.py
import ast
import operator
import math
from hello_agents import ToolRegistry

def my_calculate(expression: str) -> str:
    """简单的数学计算函数"""
    if not expression.strip():
        return "计算表达式不能为空"

    # 支持的基本运算
    operators = {
        ast.Add: operator.add,      # +
        ast.Sub: operator.sub,      # -
        ast.Mult: operator.mul,     # *
        ast.Div: operator.truediv,  # /
    }

    # 支持的基本函数
    functions = {
        'sqrt': math.sqrt,
        'pi': math.pi,
    }

    try:
        node = ast.parse(expression, mode='eval')
        result = _eval_node(node.body, operators, functions)
        return str(result)
    except:
        return "计算失败，请检查表达式格式"

def _eval_node(node, operators, functions):
    """简化的表达式求值"""
    if isinstance(node, ast.Constant):
        return node.value
    elif isinstance(node, ast.BinOp):
        left = _eval_node(node.left, operators, functions)
        right = _eval_node(node.right, operators, functions)
        op = operators.get(type(node.op))
        return op(left, right)
    elif isinstance(node, ast.Call):
        func_name = node.func.id
        if func_name in functions:
            args = [_eval_node(arg, operators, functions) for arg in node.args]
            return functions[func_name](*args)
    elif isinstance(node, ast.Name):
        if node.id in functions:
            return functions[node.id]

def create_calculator_registry():
    """创建包含计算器的工具注册表"""
    registry = ToolRegistry()

    # 注册计算器函数
    registry.register_function(
        name="my_calculator",
        description="简单的数学计算工具，支持基本运算(+,-,*,/)和sqrt函数",
        func=my_calculate
    )

    return registry
```

</details>

工具不仅支持基本的四则运算，还涵盖了常用的数学函数和常数，满足了大多数计算场景的需求。你也可以自己扩展这个文件，制作一个更加完备的计算函数。我们提供一个测试文件`test_my_calculator.py`帮助你验证功能实现：

<details>
<summary>chapter7/test_my_calculator.py</summary>

```python
# test_my_calculator.py
from dotenv import load_dotenv
from my_calculator_tool import create_calculator_registry

# 加载环境变量
load_dotenv()

def test_calculator_tool():
    """测试自定义计算器工具"""

    # 创建包含计算器的注册表
    registry = create_calculator_registry()

    print("🧪 测试自定义计算器工具\n")

    # 简单测试用例
    test_cases = [
        "2 + 3",           # 基本加法
        "10 - 4",          # 基本减法
        "5 * 6",           # 基本乘法
        "15 / 3",          # 基本除法
        "sqrt(16)",        # 平方根
    ]

    for i, expression in enumerate(test_cases, 1):
        print(f"测试 {i}: {expression}")
        result = registry.execute_tool("my_calculator", expression)
        print(f"结果: {result}\n")

def test_with_simple_agent():
    """测试与SimpleAgent的集成"""
    from hello_agents import HelloAgentsLLM

    # 创建LLM客户端
    llm = HelloAgentsLLM()

    # 创建包含计算器的注册表
    registry = create_calculator_registry()

    print("🤖 与SimpleAgent集成测试:")

    # 模拟SimpleAgent使用工具的场景
    user_question = "请帮我计算 sqrt(16) + 2 * 3"

    print(f"用户问题: {user_question}")

    # 使用工具计算
    calc_result = registry.execute_tool("my_calculator", "sqrt(16) + 2 * 3")
    print(f"计算结果: {calc_result}")

    # 构建最终回答
    final_messages = [
        {"role": "user", "content": f"计算结果是 {calc_result}，请用自然语言回答用户的问题:{user_question}"}
    ]

    print("\n🎯 SimpleAgent的回答:")
    response = llm.think(final_messages)
    for chunk in response:
        print(chunk, end="", flush=True)
    print("\n")

if __name__ == "__main__":
    test_calculator_tool()
    test_with_simple_agent()
```

</details>

通过这个简化的数学计算工具案例，我们学会了如何快速开发自定义工具：编写一个简单的计算函数，通过`ToolRegistry`注册，然后与`SimpleAgent`集成使用。

这里给出了基于`HelloAgents`的`SimpleAgent`运行工作流

![基于HelloAgents的SimpleAgent运行工作流.png](images%2F%E5%9F%BA%E4%BA%8EHelloAgents%E7%9A%84SimpleAgent%E8%BF%90%E8%A1%8C%E5%B7%A5%E4%BD%9C%E6%B5%81.png)

**7.5.3 多源搜索工具**

在实际应用中，我们经常需要整合多个外部服务来提供更强大的功能。搜索工具就是一个典型的例子，它整合多个搜索引擎，能提供更加完备的真实信息。

在第一章我们使用过Tavily的搜索API，在第四章我们使用过SerpApi的搜索API。因此这次我们使用这两个API来实现多源搜索功能。

如果没安装对应的python依赖可以运行下面这条脚本：

```bash
pip install "hello-agents[search]==0.1.1"
```

（1）搜索工具的统一接口设计

HelloAgents 框架内置的`SearchTool`展示了如何设计一个高级的多源搜索工具：

<details>
<summary>hello_agents/tools/builtin/search.py</summary>

```python
class SearchTool(Tool):
    """
    智能混合搜索工具

    支持多种搜索引擎后端，智能选择最佳搜索源:
    1. 混合模式 (hybrid) - 智能选择TAVILY或SERPAPI
    2. Tavily API (tavily) - 专业AI搜索
    3. SerpApi (serpapi) - 传统Google搜索
    """

    def __init__(self, backend: str = "hybrid", tavily_key: Optional[str] = None, serpapi_key: Optional[str] = None):
        super().__init__(
            name="search",
            description="一个智能网页搜索引擎。支持混合搜索模式，自动选择最佳搜索源。"
        )
        self.backend = backend
        self.tavily_key = tavily_key or os.getenv("TAVILY_API_KEY")
        self.serpapi_key = serpapi_key or os.getenv("SERPAPI_API_KEY")
        self.available_backends = []
        self._setup_backends()
```
</details>

这个设计的核心思想是根据可用的API密钥和依赖库，自动选择最佳的搜索后端。

（2）TAVILY 与 SERPAPI 搜索源的整合策略

<details>
<summary>SearchTool 类的 _search_hybrid 私有函数</summary>

```python
def _search_hybrid(self, query: str) -> str:
    """混合搜索 - 智能选择最佳搜索源"""
    # 优先使用Tavily（AI优化的搜索）
    if "tavily" in self.available_backends:
        try:
            return self._search_tavily(query)
        except Exception as e:
            print(f"⚠️ Tavily搜索失败: {e}")
            # 如果Tavily失败，尝试SerpApi
            if "serpapi" in self.available_backends:
                print("🔄 切换到SerpApi搜索")
                return self._search_serpapi(query)

    # 如果Tavily不可用，使用SerpApi
    elif "serpapi" in self.available_backends:
        try:
            return self._search_serpapi(query)
        except Exception as e:
            print(f"⚠️ SerpApi搜索失败: {e}")

    # 如果都不可用，提示用户配置API
    return "❌ 没有可用的搜索源，请配置TAVILY_API_KEY或SERPAPI_API_KEY环境变量"
```

</details>

这种设计体现了高可用系统的核心理念：通过降级机制，系统能够从最优的搜索源逐步降级到可用的备选方案。

当所有搜索源都不可用时，明确提示用户配置正确的 API 密钥。

（3）搜索结果的统一格式化

不同搜索引擎返回的结果格式不同，框架通过统一的格式化方法来处理：

<details>
<summary>SearchTool 类的 _search_tavily 私有函数</summary>

```python
def _search_tavily(self, query: str) -> str:
    """使用Tavily搜索"""
    response = self.tavily_client.search(
        query=query,
        search_depth="basic",
        include_answer=True,
        max_results=3
    )

    result = f"🎯 Tavily AI搜索结果:{response.get('answer', '未找到直接答案')}\n\n"

    for i, item in enumerate(response.get('results', [])[:3], 1):
        result += f"[{i}] {item.get('title', '')}\n"
        result += f"    {item.get('content', '')[:200]}...\n"
        result += f"    来源: {item.get('url', '')}\n\n"

    return result
```

</details>

基于框架的设计思想，我们可以创建自己的高级搜索工具。这次我们使用类的方式来展示不同的实现方法，创建`my_advanced_search.py`：

<details>
<summary>chapter7/my_advanced_search.py</summary>

```python
# my_advanced_search.py
import os
from typing import Optional, List, Dict, Any
from hello_agents import ToolRegistry

class MyAdvancedSearchTool:
    """
    自定义高级搜索工具类
    展示多源整合和智能选择的设计模式
    """

    def __init__(self):
        self.name = "my_advanced_search"
        self.description = "智能搜索工具，支持多个搜索源，自动选择最佳结果"
        self.search_sources = []
        self._setup_search_sources()

    def _setup_search_sources(self):
        """设置可用的搜索源"""
        # 检查Tavily可用性
        if os.getenv("TAVILY_API_KEY"):
            try:
                from tavily import TavilyClient
                self.tavily_client = TavilyClient(api_key=os.getenv("TAVILY_API_KEY"))
                self.search_sources.append("tavily")
                print("✅ Tavily搜索源已启用")
            except ImportError:
                print("⚠️ Tavily库未安装")

        # 检查SerpApi可用性
        if os.getenv("SERPAPI_API_KEY"):
            try:
                import serpapi
                self.search_sources.append("serpapi")
                print("✅ SerpApi搜索源已启用")
            except ImportError:
                print("⚠️ SerpApi库未安装")

        if self.search_sources:
            print(f"🔧 可用搜索源: {', '.join(self.search_sources)}")
        else:
            print("⚠️ 没有可用的搜索源，请配置API密钥")

    def search(self, query: str) -> str:
        """执行智能搜索"""
        if not query.strip():
            return "❌ 错误:搜索查询不能为空"

        # 检查是否有可用的搜索源
        if not self.search_sources:
            return """❌ 没有可用的搜索源，请配置以下API密钥之一:

1. Tavily API: 设置环境变量 TAVILY_API_KEY
   获取地址: https://tavily.com/

2. SerpAPI: 设置环境变量 SERPAPI_API_KEY
   获取地址: https://serpapi.com/

配置后重新运行程序。"""

        print(f"🔍 开始智能搜索: {query}")

        # 尝试多个搜索源，返回最佳结果
        for source in self.search_sources:
            try:
                if source == "tavily":
                    result = self._search_with_tavily(query)
                    if result and "未找到" not in result:
                        return f"📊 Tavily AI搜索结果:\n\n{result}"

                elif source == "serpapi":
                    result = self._search_with_serpapi(query)
                    if result and "未找到" not in result:
                        return f"🌐 SerpApi Google搜索结果:\n\n{result}"

            except Exception as e:
                print(f"⚠️ {source} 搜索失败: {e}")
                continue

        return "❌ 所有搜索源都失败了，请检查网络连接和API密钥配置"

    def _search_with_tavily(self, query: str) -> str:
        """使用Tavily搜索"""
        response = self.tavily_client.search(query=query, max_results=3)

        if response.get('answer'):
            result = f"💡 AI直接答案:{response['answer']}\n\n"
        else:
            result = ""

        result += "🔗 相关结果:\n"
        for i, item in enumerate(response.get('results', [])[:3], 1):
            result += f"[{i}] {item.get('title', '')}\n"
            result += f"    {item.get('content', '')[:150]}...\n\n"

        return result

    def _search_with_serpapi(self, query: str) -> str:
        """使用SerpApi搜索"""
        import serpapi

        search = serpapi.GoogleSearch({
            "q": query,
            "api_key": os.getenv("SERPAPI_API_KEY"),
            "num": 3
        })

        results = search.get_dict()

        result = "🔗 Google搜索结果:\n"
        if "organic_results" in results:
            for i, res in enumerate(results["organic_results"][:3], 1):
                result += f"[{i}] {res.get('title', '')}\n"
                result += f"    {res.get('snippet', '')}\n\n"

        return result

def create_advanced_search_registry():
    """创建包含高级搜索工具的注册表"""
    registry = ToolRegistry()

    # 创建搜索工具实例
    search_tool = MyAdvancedSearchTool()

    # 注册搜索工具的方法作为函数
    registry.register_function(
        name="advanced_search",
        description="高级搜索工具，整合Tavily和SerpAPI多个搜索源，提供更全面的搜索结果",
        func=search_tool.search
    )

    return registry
```

</details>

接下来可以测试我们自己编写的工具，创建`test_advanced_search.py`：

<details>
<summary>chapter7/test_advanced_search.py</summary>

```python
# test_advanced_search.py
from dotenv import load_dotenv
from my_advanced_search import create_advanced_search_registry, MyAdvancedSearchTool

# 加载环境变量
load_dotenv()

def test_advanced_search():
    """测试高级搜索工具"""

    # 创建包含高级搜索工具的注册表
    registry = create_advanced_search_registry()

    print("🔍 测试高级搜索工具\n")

    # 测试查询
    test_queries = [
        "Python编程语言的历史",
        "人工智能的最新发展",
        "2024年科技趋势"
    ]

    for i, query in enumerate(test_queries, 1):
        print(f"测试 {i}: {query}")
        result = registry.execute_tool("advanced_search", query)
        print(f"结果: {result}\n")
        print("-" * 60 + "\n")

def test_api_configuration():
    """测试API配置检查"""
    print("🔧 测试API配置检查:")

    # 直接创建搜索工具实例
    search_tool = MyAdvancedSearchTool()

    # 如果没有配置API，会显示配置提示
    result = search_tool.search("机器学习算法")
    print(f"搜索结果: {result}")

def test_with_agent():
    """测试与Agent的集成"""
    print("\n🤖 与Agent集成测试:")
    print("高级搜索工具已准备就绪，可以与Agent集成使用")

    # 显示工具描述
    registry = create_advanced_search_registry()
    tools_desc = registry.get_tools_description()
    print(f"工具描述:\n{tools_desc}")

if __name__ == "__main__":
    test_advanced_search()
    test_api_configuration()
    test_with_agent()
```

</details>

通过这个高级搜索工具的设计实践，我们学会了如何使用类的方式来构建复杂的工具系统。相比函数方式，类方式更适合需要维护状态（如 API 客户端、配置信息）的工具。

**7.5.4 工具系统的高级特性**

在掌握了基础的工具开发和多源整合后，我们来探讨工具系统的高级特性。这些特性能够让工具系统在复杂的生产环境中稳定运行，并为 Agent 提供更强大的能力。

（1）工具链式调用机制

在实际应用中，Agent 经常需要组合使用多个工具来完成复杂任务。我们可以设计一个工具链管理器来支持这种场景，这里借鉴了第六章中提到的图的概念：

<details>
<summary>hello_agents/tools/chain.py</summary>

```python
# tool_chain_manager.py
from typing import List, Dict, Any, Optional
from hello_agents import ToolRegistry

class ToolChain:
    """工具链 - 支持多个工具的顺序执行"""

    def __init__(self, name: str, description: str):
        self.name = name
        self.description = description
        self.steps: List[Dict[str, Any]] = []

    def add_step(self, tool_name: str, input_template: str, output_key: str = None):
        """
        添加工具执行步骤

        Args:
            tool_name: 工具名称
            input_template: 输入模板，支持变量替换
            output_key: 输出结果的键名，用于后续步骤引用
        """
        self.steps.append({
            "tool_name": tool_name,
            "input_template": input_template,
            "output_key": output_key or f"step_{len(self.steps)}_result"
        })

    def execute(self, registry: ToolRegistry, initial_input: str, context: Dict[str, Any] = None) -> str:
        """执行工具链"""
        context = context or {}
        context["input"] = initial_input

        print(f"🔗 开始执行工具链: {self.name}")

        for i, step in enumerate(self.steps, 1):
            tool_name = step["tool_name"]
            input_template = step["input_template"]
            output_key = step["output_key"]

            # 替换模板中的变量
            try:
                tool_input = input_template.format(**context)
            except KeyError as e:
                return f"❌ 工具链执行失败:模板变量 {e} 未找到"

            print(f"  步骤 {i}: 使用 {tool_name} 处理 '{tool_input[:50]}...'")

            # 执行工具
            result = registry.execute_tool(tool_name, tool_input)
            context[output_key] = result

            print(f"  ✅ 步骤 {i} 完成，结果长度: {len(result)} 字符")

        # 返回最后一步的结果
        final_result = context[self.steps[-1]["output_key"]]
        print(f"🎉 工具链 '{self.name}' 执行完成")
        return final_result

class ToolChainManager:
    """工具链管理器"""

    def __init__(self, registry: ToolRegistry):
        self.registry = registry
        self.chains: Dict[str, ToolChain] = {}

    def register_chain(self, chain: ToolChain):
        """注册工具链"""
        self.chains[chain.name] = chain
        print(f"✅ 工具链 '{chain.name}' 已注册")

    def execute_chain(self, chain_name: str, input_data: str, context: Dict[str, Any] = None) -> str:
        """执行指定的工具链"""
        if chain_name not in self.chains:
            return f"❌ 工具链 '{chain_name}' 不存在"

        chain = self.chains[chain_name]
        return chain.execute(self.registry, input_data, context)

    def list_chains(self) -> List[str]:
        """列出所有工具链"""
        return list(self.chains.keys())

# 使用示例
def create_research_chain() -> ToolChain:
    """创建一个研究工具链:搜索 -> 计算 -> 总结"""
    chain = ToolChain(
        name="research_and_calculate",
        description="搜索信息并进行相关计算"
    )

    # 步骤1:搜索信息
    chain.add_step(
        tool_name="search",
        input_template="{input}",
        output_key="search_result"
    )

    # 步骤2:基于搜索结果进行计算（如果需要）
    chain.add_step(
        tool_name="my_calculator",
        input_template="根据以下信息计算相关数值:{search_result}",
        output_key="calculation_result"
    )

    return chain
```

</details>

（2）异步工具执行支持

对于耗时的工具操作，我们可以提供异步执行支持：

<details>
<summary>hello_agents/tools/async_executor.py</summary>

```python
# async_tool_executor.py
import asyncio
import concurrent.futures
from typing import Dict, Any, List, Callable
from hello_agents import ToolRegistry

class AsyncToolExecutor:
    """异步工具执行器"""

    def __init__(self, registry: ToolRegistry, max_workers: int = 4):
        self.registry = registry
        self.executor = concurrent.futures.ThreadPoolExecutor(max_workers=max_workers)

    async def execute_tool_async(self, tool_name: str, input_data: str) -> str:
        """异步执行单个工具"""
        loop = asyncio.get_event_loop()

        def _execute():
            return self.registry.execute_tool(tool_name, input_data)

        result = await loop.run_in_executor(self.executor, _execute)
        return result

    async def execute_tools_parallel(self, tasks: List[Dict[str, str]]) -> List[str]:
        """并行执行多个工具"""
        print(f"🚀 开始并行执行 {len(tasks)} 个工具任务")

        # 创建异步任务
        async_tasks = []
        for task in tasks:
            tool_name = task["tool_name"]
            input_data = task["input_data"]
            async_task = self.execute_tool_async(tool_name, input_data)
            async_tasks.append(async_task)

        # 等待所有任务完成
        results = await asyncio.gather(*async_tasks)

        print(f"✅ 所有工具任务执行完成")
        return results

    def __del__(self):
        """清理资源"""
        if hasattr(self, 'executor'):
            self.executor.shutdown(wait=True)

# 使用示例
async def test_parallel_execution():
    """测试并行工具执行"""
    from hello_agents import ToolRegistry

    registry = ToolRegistry()
    # 假设已经注册了搜索和计算工具

    executor = AsyncToolExecutor(registry)

    # 定义并行任务
    tasks = [
        {"tool_name": "search", "input_data": "Python编程"},
        {"tool_name": "search", "input_data": "机器学习"},
        {"tool_name": "my_calculator", "input_data": "2 + 2"},
        {"tool_name": "my_calculator", "input_data": "sqrt(16)"},
    ]

    # 并行执行
    results = await executor.execute_tools_parallel(tasks)

    for i, result in enumerate(results):
        print(f"任务 {i+1} 结果: {result[:100]}...")
```

</details>


基于以上的设计和实现经验，我们可以总结出工具系统开发的核心理念：

* 在设计层面，每个工具都应该遵循单一职责原则，专注于特定功能的同时保持接口的统一性，并将完善的异常处理和安全优先的输入验证作为基本要求。
* 在性能优化方面，利用异步执行提高并发处理能力，同时合理管理外部连接和系统资源。

### 7.6 **本章小结**

回顾本章，我们完成了一项富有挑战的任务：一步步构建了一个基础的智能体框架——HelloAgents。这个过程始终遵循着“分层解耦、职责单一、接口统一”的核心原则。

在框架的具体实现中，我们再次实现了**四种经典的 Agent 范式**。

* SimpleAgent 的基础对话模式
* ReActAgent 的推理与行动结合
* ReflectionAgent 的自我反思与迭代优化
* PlanAndSolveAgent 的分解规划与逐步执行

而**工具系统**作为 Agent 能力延伸的核心，其构建过程则是一次完整的工程实践。

我们所建立的**统一 LLM 接口**、**标准化消息系统**、**工具注册机制**，共同构成了一个完备的技术底座。

第八章的记忆与 RAG 系统将基于此扩展 Agent 的能力边界

第九章的上下文工程将深入我们已经建立的消息处理机制

第十章的智能体协议则需要扩展新的工具。

## 八、记忆与检索

在前面的章节中，我们构建了 HelloAgents 框架的基础架构，实现了多种智能体范式和工具系统。

不过，我们的框架还缺少一个关键能力：**记忆**。如果智能体无法记住之前的交互内容，也无法从历史经验中学习，那么在连续对话或复杂任务中，其表现将受到极大限制。

本章将在第七章构建的框架基础上，为 HelloAgents 增加两个核心能力：**记忆系统（Memory System）**和**检索增强生成（Retrieval-Augmented Generation, RAG）**。

### 8.1 **从认知科学到智能体记忆**

**8.1.1 人类记忆系统的启发**

根据认知心理学的研究，人类记忆可以分为以下几个层次：

1. **感觉记忆（Sensory Memory）**：持续时间极短（0.5-3秒），容量巨大，负责暂时保存感官接收到的所有信息
2. **工作记忆（Working Memory）**：持续时间短（15-30秒），容量有限（7±2个项目），负责当前任务的信息处理
3. **长期记忆（Long-term Memory）**：持续时间长（可达终生），容量几乎无限，进一步分为：
    - **程序性记忆**：技能和习惯（如骑自行车）
    - **陈述性记忆**：可以用语言表达的知识

**8.1.2 为何智能体需要记忆与RAG**

对于基于LLM的智能体而言，通常面临两个根本性局限：对话状态的遗忘和内置知识的局限。

（1）局限一：无状态导致的对话遗忘

当前的大语言模型虽然强大，但设计上是无状态的。这意味着，每一次用户请求（或API调用）都是一次独立的、无关联的计算。模型本身不会自动“记住”上一次对话的内容。这带来了几个问题：

1. **上下文丢失**：在长对话中，早期的重要信息可能会因为上下文窗口限制而丢失
2. **个性化缺失**：Agent无法记住用户的偏好、习惯或特定需求
3. **学习能力受限**：无法从过往的成功或失败经验中学习改进
4. **一致性问题**：在多轮对话中可能出现前后矛盾的回答

让我们通过一个具体例子来理解这个问题：

```python
# 第七章的Agent使用方式
from hello_agents import SimpleAgent, HelloAgentsLLM

agent = SimpleAgent(name="学习助手", llm=HelloAgentsLLM())

# 第一次对话
response1 = agent.run("我叫张三，正在学习Python，目前掌握了基础语法")
print(response1)  # "很好！Python基础语法是编程的重要基础..."
 
# 第二次对话（新的会话，例如重启程序后重新创建Agent）
agent = SimpleAgent(name="学习助手", llm=HelloAgentsLLM())
response2 = agent.run("你还记得我的学习进度吗？")
print(response2)  # "抱歉，我不知道您的学习进度..."
```

需要注意的是，第七章中的 `SimpleAgent` 会在同一个实例的 `_history` 中暂存当前对话，因此同一进程、同一实例内的连续对话可以携带最近上下文。但这种历史只是临时消息列表，不会跨会话持久化，也不能进行长期检索、遗忘和整合。

要解决这个问题，我们的框架需要引入**记忆系统**。

（2）局限二：模型内置知识的局限性

除了遗忘对话历史，LLM 的另一个核心局限在于其知识是静态的、有限的。这些知识完全来自于它的训练数据，并因此带来一系列问题：

1. **知识时效性**：大模型的训练数据有时间截止点，无法获取最新信息
2. **专业领域知识**：通用模型在特定领域的深度知识可能不足
3. **事实准确性**：通过检索验证，减少模型的幻觉问题
4. **可解释性**：提供信息来源，增强回答的可信度

为了克服这一局限，**RAG** 技术应运而生。它的核心思想是在模型生成回答之前，先从一个外部知识库（如文档、数据库、API）中检索出最相关的信息，并将这些信息作为上下文一同提供给模型。

**8.1.3 记忆与RAG系统架构设计**

基于第七章建立的框架基础和认知科学的启发，我们设计了一个分层的记忆与 RAG 系统架构。

这个架构不仅借鉴了人类记忆系统的层次结构，还充分考虑了工程实现的可扩展性。

在实现上，我们将记忆和 RAG 设计为两个独立的工具：

- `memory_tool`负责存储和维护对话过程中的交互信息
- `rag_tool`则负责从用户提供的知识库中检索相关信息作为上下文，并可将重要的检索结果自动存储到记忆系统中。

![HelloAgents记忆与RAG系统整体架构.png](images%2FHelloAgents%E8%AE%B0%E5%BF%86%E4%B8%8ERAG%E7%B3%BB%E7%BB%9F%E6%95%B4%E4%BD%93%E6%9E%B6%E6%9E%84.png)

记忆系统采用了四层架构设计：

```
HelloAgents记忆系统
├── 基础设施层 (Infrastructure Layer)
│   ├── MemoryManager - 记忆管理器（统一调度和协调）
│   ├── MemoryItem - 记忆数据结构（标准化记忆项）
│   ├── MemoryConfig - 配置管理（系统参数设置）
│   └── BaseMemory - 记忆基类（通用接口定义）
├── 记忆类型层 (Memory Types Layer)
│   ├── WorkingMemory - 工作记忆（临时信息，TTL管理）
│   ├── EpisodicMemory - 情景记忆（具体事件，时间序列）
│   ├── SemanticMemory - 语义记忆（抽象知识，图谱关系）
│   └── PerceptualMemory - 感知记忆（多模态数据）
├── 存储后端层 (Storage Backend Layer)
│   ├── QdrantVectorStore - 向量存储（高性能语义检索）
│   ├── Neo4jGraphStore - 图存储（知识图谱管理）
│   └── SQLiteDocumentStore - 文档存储（结构化持久化）
└── 嵌入服务层 (Embedding Service Layer)
    ├── DashScopeEmbedding - 通义千问嵌入（云端API）
    ├── LocalTransformerEmbedding - 本地嵌入（离线部署）
    └── TFIDFEmbedding - TFIDF嵌入（轻量级兜底）
```

RAG系统专注于外部知识的获取和利用：

```
HelloAgents RAG系统
├── 文档处理层 (Document Processing Layer)
│   ├── DocumentProcessor - 文档处理器（多格式解析）
│   ├── Document - 文档对象（元数据管理）
│   └── Pipeline - RAG管道（端到端处理）
├── 嵌入表示层 (Embedding Layer)
│   └── 统一嵌入接口 - 复用记忆系统的嵌入服务
├── 向量存储层 (Vector Storage Layer)
│   └── QdrantVectorStore - 向量数据库（命名空间隔离）
└── 智能问答层 (Intelligent Q&A Layer)
    ├── 多策略检索 - 向量检索 + MQE + HyDE
    ├── 上下文构建 - 智能片段合并与截断
    └── LLM增强生成 - 基于上下文的准确问答
```

**8.1.4 本章学习目标与快速体验**

```
hello-agents/
├── hello_agents/
│   ├── memory/                   # 记忆系统模块
│   │   ├── base.py               # 基础数据结构（MemoryItem, MemoryConfig, BaseMemory）
│   │   ├── manager.py            # 记忆管理器（统一协调调度）
│   │   ├── embedding.py          # 统一嵌入服务（DashScope/Local/TFIDF）
│   │   ├── types/                # 记忆类型实现
│   │   │   ├── working.py        # 工作记忆（TTL管理，纯内存）
│   │   │   ├── episodic.py       # 情景记忆（事件序列，SQLite+Qdrant）
│   │   │   ├── semantic.py       # 语义记忆（知识图谱，Qdrant+Neo4j）
│   │   │   └── perceptual.py     # 感知记忆（多模态，SQLite+Qdrant）
│   │   ├── storage/              # 存储后端实现
│   │   │   ├── qdrant_store.py   # Qdrant向量存储（高性能向量检索）
│   │   │   ├── neo4j_store.py    # Neo4j图存储（知识图谱管理）
│   │   │   └── document_store.py # SQLite文档存储（结构化持久化）
│   │   └── rag/                  # RAG系统
│   │       ├── pipeline.py       # RAG管道（端到端处理）
│   │       └── document.py       # 文档处理器（多格式解析）
│   └── tools/builtin/            # 扩展内置工具
│       ├── memory_tool.py        # 记忆工具（Agent记忆能力）
│       └── rag_tool.py           # RAG工具（智能问答能力）
└──
```

**快速开始：安装HelloAgents框架**

可以通过以下命令安装本章对应的版本：

```bash
# 0.2.0版本若遇到模型不可用，查看issue#320或切换0.2.9版本进行测试
pip install "hello-agents[all]==0.2.0"
python -m spacy download zh_core_web_sm
python -m spacy download en_core_web_sm
```

除此之外，还需要在`.env`配置图数据库，向量数据库，LLM 以及 Embedding 方案的 API，若没有 API 可切换为本地部署模型方案。本教程中：

- 向量数据库采用 Qdrant
- 图数据库采用Neo4J
- Embedding 首选百炼平台

<details>
<summary>chapter8/.env</summary>

```python
# ================================
# Qdrant 向量数据库配置 - 获取API密钥：https://cloud.qdrant.io/
# ================================
# 使用Qdrant云服务 (推荐)
QDRANT_URL=https://your-cluster.qdrant.tech:6333
QDRANT_API_KEY=your_qdrant_api_key_here

# 或使用本地Qdrant (需要Docker)
# QDRANT_URL=http://localhost:6333
# QDRANT_API_KEY=

# Qdrant集合配置
QDRANT_COLLECTION=hello_agents_vectors
QDRANT_VECTOR_SIZE=384
QDRANT_DISTANCE=cosine
QDRANT_TIMEOUT=30

# ================================
# Neo4j 图数据库配置 - 获取API密钥：https://neo4j.com/cloud/aura/
# ================================
# 使用Neo4j Aura云服务 (推荐)
NEO4J_URI=neo4j+s://your-instance.databases.neo4j.io
NEO4J_USERNAME=neo4j
NEO4J_PASSWORD=your_neo4j_password_here

# 或使用本地Neo4j (需要Docker)
# NEO4J_URI=bolt://localhost:7687
# NEO4J_USERNAME=neo4j
# NEO4J_PASSWORD=hello-agents-password

# Neo4j连接配置
NEO4J_DATABASE=neo4j
NEO4J_MAX_CONNECTION_LIFETIME=3600
NEO4J_MAX_CONNECTION_POOL_SIZE=50
NEO4J_CONNECTION_TIMEOUT=60

# ==========================
# 嵌入（Embedding）配置示例 - 可从阿里云控制台获取：https://dashscope.aliyun.com/
# ==========================
# - 若为空，dashscope 默认 text-embedding-v3；local 默认 sentence-transformers/all-MiniLM-L6-v2
EMBED_MODEL_TYPE=dashscope
EMBED_MODEL_NAME=
EMBED_API_KEY=
EMBED_BASE_URL=
```

</details>

遵循第七章确立的设计原则，我们将记忆和 RAG 能力封装为标准工具，而不是创建新的 Agent 类。

在开始之前，体验使用 Hello-agents 构建具有记忆和 RAG 能力的智能体！

<details>
<summary>chapter8/example</summary>

```python
# 配置好同级文件夹下.env中的大模型API
from hello_agents import SimpleAgent, HelloAgentsLLM, ToolRegistry
from hello_agents.tools import MemoryTool, RAGTool

# 创建LLM实例
llm = HelloAgentsLLM()

# 创建工具注册表
tool_registry = ToolRegistry()

# 添加记忆工具
memory_tool = MemoryTool(user_id="user123")
tool_registry.register_tool(memory_tool)

# 添加RAG工具
rag_tool = RAGTool(knowledge_base_path="./knowledge_base")
tool_registry.register_tool(rag_tool)

# 创建Agent并配置工具
agent = SimpleAgent(
    name="智能助手",
    llm=llm,
    system_prompt="你是一个有记忆和知识检索能力的AI助手",
    tool_registry=tool_registry
)

# 开始对话
response = agent.run("你好！请记住我叫张三，我是一名Python开发者")
print(response)
```

</details>

如果一切配置完毕，可以看到以下内容。

<details>
<summary>点击展开运行结果</summary>

```
[OK] SQLite 数据库表和索引创建完成
[OK] SQLite 文档存储初始化完成: ./memory_data\memory.db
INFO:hello_agents.memory.storage.qdrant_store:✅ 成功连接到Qdrant云服务: https://0c517275-2ad0-4442-8309-11c36dc7e811.us-east-1-1.aws.cloud.qdrant.io:6333
INFO:hello_agents.memory.storage.qdrant_store:✅ 使用现有Qdrant集合: hello_agents_vectors
INFO:hello_agents.memory.types.semantic:✅ 嵌入模型就绪，维度: 1024
INFO:hello_agents.memory.types.semantic:✅ Qdrant向量数据库初始化完成
INFO:hello_agents.memory.storage.neo4j_store:✅ 成功连接到Neo4j云服务: neo4j+s://851b3a28.databases.neo4j.io      NFO:hello_agents.memory.types.semantic:✅ Neo4j图数据库初始化完成
INFO:hello_agents.memory.storage.neo4j_store:✅ Neo4j索引创建完成
INFO:hello_agents.memory.types.semantic:✅ Neo4j图数据库初始化完成
INFO:hello_agents.memory.types.semantic:🏥 数据库健康状态: Qdrant=✅, Neo4j=✅
INFO:hello_agents.memory.types.semantic:✅ 加载中文spaCy模型: zh_core_web_sm
INFO:hello_agents.memory.types.semantic:✅ 加载英文spaCy模型: en_core_web_sm
INFO:hello_agents.memory.types.semantic:📚 可用语言模型: 中文, 英文
INFO:hello_agents.memory.types.semantic:增强语义记忆初始化完成（使用Qdrant+Neo4j专业数据库）
INFO:hello_agents.memory.manager:MemoryManager初始化完成，启用记忆类型: ['working', 'episodic', 'semantic']      
✅ 工具 'memory' 已注册。
INFO:hello_agents.memory.storage.qdrant_store:✅ 成功连接到Qdrant云服务: https://0c517275-2ad0-4442-8309-11c36dc7eNFO:hello_agents.memory.storage.qdrant_store:✅ 使用现有Qdrant集合: rag_knowledge_base
811.us-east-1-1.aws.cloud.qdrant.io:6333
INFO:hello_agents.memory.storage.qdrant_store:✅ 使用现有Qdrant集合: rag_knowledge_base
✅ RAG工具初始化成功: namespace=default, collection=rag_knowledge_base
✅ 工具 'rag' 已注册。
你好，张三！很高兴认识你。作为一名Python开发者，你一定对编程很有热情。如果你有任何技术问题或者需要讨论Python相关 
的话题，随时可以找我。我会尽力帮助你。有什么我现在就能帮到你的吗？
```

</details>

### 8.2 **记忆系统：让智能体拥有记忆**

**8.2.1 记忆系统的工作流程**

在进入代码实现阶段前，我们需要先定义记忆系统的工作流程。

该流程参考了认知科学中的记忆模型，并将每个认知阶段映射为具体的技术组件和操作。理解这一映射关系，有助于我们后续的代码实现。

![记忆形成的认知过程.png](images%2F%E8%AE%B0%E5%BF%86%E5%BD%A2%E6%88%90%E7%9A%84%E8%AE%A4%E7%9F%A5%E8%BF%87%E7%A8%8B.png)

根据认知科学的研究，人类记忆的形成经历以下几个阶段：

1. **编码（Encoding）**：将感知到的信息转换为可存储的形式
2. **存储（Storage）**：将编码后的信息保存在记忆系统中
3. **检索（Retrieval）**：根据需要从记忆中提取相关信息
4. **整合（Consolidation）**：将短期记忆转化为长期记忆
5. **遗忘（Forgetting）**：删除不重要或过时的信息

基于该启发，为 HelloAgents 设计了一套完整的记忆系统。

其核心思想是模仿人类大脑处理不同类型信息的方式，将记忆划分为多个专门的模块，并建立一套智能化的管理机制。

![HelloAgents记忆系统的完整工作流程.png](images%2FHelloAgents%E8%AE%B0%E5%BF%86%E7%B3%BB%E7%BB%9F%E7%9A%84%E5%AE%8C%E6%95%B4%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png)

记忆系统由四种不同类型的记忆模块构成，每种模块都针对特定的应用场景和生命周期进行了优化：

1. 工作记忆 (Working Memory)：存储智能体“短期记忆”，主要用于存储当前对话的上下文信息。为确保高速访问和响应，其容量被有意限制（例如，默认50条），并且生命周期与单个会话绑定，会话结束后便会自动清理。
2. 情景记忆 (Episodic Memory)：长期存储具体的交互事件和智能体的学习经历。与工作记忆不同，情景记忆包含了丰富的上下文信息，并支持按时间序列或主题进行回顾式检索，是智能体“复盘”和学习过往经验的基础。
3. 语义记忆 (Semantic Memory)：存储的是更为抽象的知识、概念和规则。例如，通过对话了解到的用户偏好、需要长期遵守的指令或领域知识点，都适合存放在这里。这部分记忆具有高度的持久性和重要性，是智能体形成“知识体系”和进行关联推理的核心。
4. 感知记忆 (Perceptual Memory)：专门处理图像、音频等多模态信息，并支持跨模态检索。其生命周期会根据信息的重要性和可用存储空间进行动态管理。

**8.2.2 快速上手记忆功能**

<details>
<summary>chapter8/memory_example</summary>

```python
from hello_agents import SimpleAgent, HelloAgentsLLM, ToolRegistry
from hello_agents.tools import MemoryTool

# 创建LLM实例
llm = HelloAgentsLLM()

# 创建记忆工具
memory_tool = MemoryTool(user_id="user123")
tool_registry = ToolRegistry()
tool_registry.register_tool(memory_tool)

# 创建具有记忆能力的Agent
agent = SimpleAgent(name="记忆助手", llm=llm, tool_registry=tool_registry)
 
# 体验记忆功能
print("=== 添加多个记忆 ===")

# 添加第一个记忆
result1 = memory_tool.run("add", content="用户张三是一名Python开发者，专注于机器学习和数据分析", memory_type="semantic", importance=0.8)
print(f"记忆1: {result1}")

# 添加第二个记忆
result2 = memory_tool.run("add", content="李四是前端工程师，擅长React和Vue.js开发", memory_type="semantic", importance=0.7)
print(f"记忆2: {result2}")

# 添加第三个记忆
result3 = memory_tool.run("add", content="王五是产品经理，负责用户体验设计和需求分析", memory_type="semantic", importance=0.6)
print(f"记忆3: {result3}")

print("\n=== 搜索特定记忆 ===")
# 搜索前端相关的记忆
print("🔍 搜索 '前端工程师':")
result = memory_tool.run("search", query="前端工程师", limit=3)
print(result)

print("\n=== 记忆摘要 ===")
result = memory_tool.run("summary")
print(result)
```

</details>

**8.2.3 MemoryTool详解**

现在让我们采用自顶向下的方式，从 MemoryTool 支持的具体操作开始，逐步深入到底层实现。

MemoryTool 作为记忆系统的统一接口，其设计遵循了"统一入口，分发处理"的架构模式：

<details>
<summary>hello_agents/tools/builtin/memory_tool.py 中 MemoryTool 类的 execute 函数</summary>

```python
def execute(self, action: str, **kwargs) -> str:
    """执行记忆操作

    支持的操作：
    - add: 添加记忆（支持4种类型: working/episodic/semantic/perceptual）
    - search: 搜索记忆
    - summary: 获取记忆摘要
    - stats: 获取统计信息
    - update: 更新记忆
    - remove: 删除记忆
    - forget: 遗忘记忆（多种策略）
    - consolidate: 整合记忆（短期→长期）
    - clear_all: 清空所有记忆
    """

    if action == "add":
        return self._add_memory(**kwargs)
    elif action == "search":
        return self._search_memory(**kwargs)
    elif action == "summary":
        return self._get_summary(**kwargs)
    # ... 其他操作
```

</details>

这种统一的`execute`接口设计简化了 Agent 的调用方式，通过`action`参数指定具体操作，使用`**kwargs`允许每个操作有不同的参数需求。在这里我们会将比较重要的几个操作罗列出来：

（1）操作1：add

`add`操作是记忆系统的基础，它模拟了人类大脑将感知信息编码为记忆的过程。在实现中，我们不仅要存储记忆内容，还要为每个记忆添加丰富的上下文信息，这些信息将在后续的检索和管理中发挥重要作用。

<details>
<summary>hello_agents/tools/builtin/memory_tool.py 中 MemoryTool 类的 _add_memory 私有函数</summary>

```python
def _add_memory(
    self,
    content: str = "",
    memory_type: str = "working",
    importance: float = 0.5,
    file_path: str = None,
    modality: str = None,
    **metadata
) -> str:
    """添加记忆"""
    try:
        # 确保会话ID存在
        if self.current_session_id is None:
            self.current_session_id = f"session_{datetime.now().strftime('%Y%m%d_%H%M%S')}"

        # 感知记忆文件支持
        if memory_type == "perceptual" and file_path:
            inferred = modality or self._infer_modality(file_path)
            metadata.setdefault("modality", inferred)
            metadata.setdefault("raw_data", file_path)

        # 添加会话信息到元数据
        metadata.update({
            "session_id": self.current_session_id,
            "timestamp": datetime.now().isoformat()
        })

        memory_id = self.memory_manager.add_memory(
            content=content,
            memory_type=memory_type,
            importance=importance,
            metadata=metadata,
            auto_classify=False
        )

        return f"✅ 记忆已添加 (ID: {memory_id[:8]}...)"

    except Exception as e:
        return f"❌ 添加记忆失败: {str(e)}"
```

</details>

这里主要实现了三个关键任务：

* 会话ID的自动管理（确保每个记忆都有明确的会话归属）
* 多模态数据的智能处理（自动推断文件类型并保存相关元数据）
* 以及上下文信息的自动补充（为每个记忆添加时间戳和会话信息）

其中，importance参数（默认0.5）用于标记记忆的重要程度，取值范围0.0-1.0，这个机制模拟了人类大脑对不同信息重要性的评估。这种设计让 Agent 能够自动区分不同时间段的对话，并为后续的检索和管理提供丰富的上下文信息。

其中，对每个记忆类型，我们提供了不同的使用示例：

<details>
<summary>不同类型记忆的使用示例</summary>

```python
# 1. 工作记忆 - 临时信息，容量有限
memory_tool.run("add",
    content="用户刚才问了关于Python函数的问题",
    memory_type="working",
    importance=0.6
)

# 2. 情景记忆 - 具体事件和经历
memory_tool.run("add",
    content="2024年3月15日，用户张三完成了第一个Python项目",
    memory_type="episodic",
    importance=0.8,
    event_type="milestone",
    location="在线学习平台"
)

# 3. 语义记忆 - 抽象知识和概念
memory_tool.run("add",
    content="Python是一种解释型、面向对象的编程语言",
    memory_type="semantic",
    importance=0.9,
    knowledge_type="factual"
)

# 4. 感知记忆 - 多模态信息
memory_tool.run("add",
    content="用户上传了一张Python代码截图，包含函数定义",
    memory_type="perceptual",
    importance=0.7,
    modality="image",
    file_path="./uploads/code_screenshot.png"
)
```

</details>

（2）操作2：search

`search`操作是记忆系统的核心功能，它需要在大量记忆中快速找到与查询最相关的内容。它涉及语义理解、相关性计算和结果排序等多个环节。

<details>
<summary>hello_agents/tools/builtin/memory_tool.py 中 MemoryTool 类的 _search_memory 私有函数</summary>

```python
def _search_memory(
    self,
    query: str,
    limit: int = 5,
    memory_types: List[str] = None,
    memory_type: str = None,
    min_importance: float = 0.1
) -> str:
    """搜索记忆"""
    try:
        # 参数标准化处理
        if memory_type and not memory_types:
            memory_types = [memory_type]

        results = self.memory_manager.retrieve_memories(
            query=query,
            limit=limit,
            memory_types=memory_types,
            min_importance=min_importance
        )

        if not results:
            return f"🔍 未找到与 '{query}' 相关的记忆"

        # 格式化结果
        formatted_results = []
        formatted_results.append(f"🔍 找到 {len(results)} 条相关记忆:")

        for i, memory in enumerate(results, 1):
            memory_type_label = {
                "working": "工作记忆",
                "episodic": "情景记忆", 
                "semantic": "语义记忆",
                "perceptual": "感知记忆"
            }.get(memory.memory_type, memory.memory_type)

            content_preview = memory.content[:80] + "..." if len(memory.content) > 80 else memory.content
            formatted_results.append(
                f"{i}. [{memory_type_label}] {content_preview} (重要性: {memory.importance:.2f})"
            )

        return "\n".join(formatted_results)

    except Exception as e:
        return f"❌ 搜索记忆失败: {str(e)}"
```

</details>

搜索操作在设计上支持单数和复数两种参数形式（`memory_type`和`memory_types`），让用户以最自然的方式表达需求。

其中，`min_importance`参数（默认0.1）用于过滤低质量记忆。

<details>
<summary>搜索功能的使用示例</summary>

```python
# 基础搜索
result = memory_tool.execute("search", query="Python编程", limit=5)

# 指定记忆类型搜索
result = memory_tool.execute("search",
    query="学习进度",
    memory_type="episodic",
    limit=3
)

# 多类型搜索
result = memory_tool.execute("search",
    query="函数定义",
    memory_types=["semantic", "episodic"],
    min_importance=0.5
)
```

</details>

（3）操作3：forget

遗忘机制是最具认知科学色彩的功能，它模拟人类大脑的选择性遗忘过程，支持三种策略：

* 基于重要性（删除不重要的记忆）
* 基于时间（删除过时的记忆）
* 基于容量（当存储接近上限时删除最不重要的记忆）

<details>
<summary>hello_agents/tools/builtin/memory_tool.py 中 MemoryTool 类的 _forget 私有函数</summary>

```python
def _forget(self, strategy: str = "importance_based", threshold: float = 0.1, max_age_days: int = 30) -> str:
    """遗忘记忆（支持多种策略）"""
    try:
        count = self.memory_manager.forget_memories(
            strategy=strategy,
            threshold=threshold,
            max_age_days=max_age_days
        )
        return f"🧹 已遗忘 {count} 条记忆（策略: {strategy}）"
    except Exception as e:
        return f"❌ 遗忘记忆失败: {str(e)}"
```

</details>

<details>
<summary>三种遗忘策略的使用示例</summary>

```python
# 1. 基于重要性的遗忘 - 删除重要性低于阈值的记忆
memory_tool.execute("forget",
    strategy="importance_based",
    threshold=0.2
)

# 2. 基于时间的遗忘 - 删除超过指定天数的记忆
memory_tool.execute("forget",
    strategy="time_based",
    max_age_days=30
)

# 3. 基于容量的遗忘 - 当记忆数量超限时删除最不重要的
memory_tool.execute("forget",
    strategy="capacity_based",
    threshold=0.3
)
```

</details>

（4）操作4：consolidate

<details>
<summary>hello_agents/tools/builtin/memory_tool.py 中 MemoryTool 类的 _consolidate 私有函数</summary>

```python
def _consolidate(self, from_type: str = "working", to_type: str = "episodic", importance_threshold: float = 0.7) -> str:
    """整合记忆（将重要的短期记忆提升为长期记忆）"""
    try:
        count = self.memory_manager.consolidate_memories(
            from_type=from_type,
            to_type=to_type,
            importance_threshold=importance_threshold,
        )
        return f"🔄 已整合 {count} 条记忆为长期记忆（{from_type} → {to_type}，阈值={importance_threshold}）"
    except Exception as e:
        return f"❌ 整合记忆失败: {str(e)}"
```

</details>

consolidate 操作借鉴了神经科学中的记忆固化概念，模拟人类大脑将短期记忆转化为长期记忆的过程。默认设置是将重要性超过0.7的工作记忆转换为情景记忆，这个阈值确保只有真正重要的信息才会被长期保存。

整个过程是自动化的，用户无需手动选择具体的记忆，系统会智能地识别符合条件的记忆并执行类型转换。

<details>
<summary>记忆整合的使用示例</summary>

```python
# 将重要的工作记忆转为情景记忆
memory_tool.execute("consolidate",
    from_type="working",
    to_type="episodic",
    importance_threshold=0.7
)

# 将重要的情景记忆转为语义记忆
memory_tool.execute("consolidate",
    from_type="episodic",
    to_type="semantic",
    importance_threshold=0.8
)
```

</details>

通过以上几个核心操作协作，MemoryTool 构建了一个完整的记忆生命周期管理体系。从记忆的创建、检索、摘要到遗忘、整合和管理，形成了一个闭环的智能记忆管理系统，让 Agent 真正具备了类人的记忆能力。

**8.2.4 MemoryManager详解**

理解了 MemoryTool 的接口设计后，让我们深入到底层实现，看看 MemoryTool 是如何与 MemoryManager 协作的。

这种分层设计体现了软件工程中的关注点分离原则，MemoryTool 专注于用户接口和参数处理，而 MemoryManager 则负责核心的记忆管理逻辑。

MemoryTool 在初始化时会创建一个 MemoryManager 实例，并根据配置启用不同类型的记忆模块。这种设计让用户可以根据具体需求选择启用哪些记忆类型，既保证了功能的完整性，又避免了不必要的资源消耗。

<details>
<summary>hello_agents/tools/builtin/memory_tool.py 中的 MemoryTool 类</summary>

```python
class MemoryTool(Tool):
    """记忆工具 - 为Agent提供记忆功能"""
    
    def __init__(
        self,
        user_id: str = "default_user",
        memory_config: MemoryConfig = None,
        memory_types: List[str] = None
    ):
        super().__init__(
            name="memory",
            description="记忆工具 - 可以存储和检索对话历史、知识和经验"
        )
        
        # 初始化记忆管理器
        self.memory_config = memory_config or MemoryConfig()
        self.memory_types = memory_types or ["working", "episodic", "semantic"]
        
        self.memory_manager = MemoryManager(
            config=self.memory_config,
            user_id=user_id,
            enable_working="working" in self.memory_types,
            enable_episodic="episodic" in self.memory_types,
            enable_semantic="semantic" in self.memory_types,
            enable_perceptual="perceptual" in self.memory_types
        )
```

</details>

MemoryManager 作为记忆系统的核心协调者，负责管理不同类型的记忆模块，并提供统一的操作接口。具体的存储与检索能力由各记忆类型在内部实现。

<details>
<summary>hello_agents/memory/manager.py 中的 MemoryManager 类</summary>

```python
class MemoryManager:
    """记忆管理器 - 统一的记忆操作接口"""

    def __init__(
        self,
        config: Optional[MemoryConfig] = None,
        user_id: str = "default_user",
        enable_working: bool = True,
        enable_episodic: bool = True,
        enable_semantic: bool = True,
        enable_perceptual: bool = False
    ):
        self.config = config or MemoryConfig()
        self.user_id = user_id

        # 存储和检索功能由各记忆类型内部实现

        # 初始化各类型记忆
        self.memory_types = {}

        if enable_working:
            self.memory_types['working'] = WorkingMemory(self.config)

        if enable_episodic:
            self.memory_types['episodic'] = EpisodicMemory(self.config)

        if enable_semantic:
            self.memory_types['semantic'] = SemanticMemory(self.config)

        if enable_perceptual:
            self.memory_types['perceptual'] = PerceptualMemory(self.config)
```

</details>

**8.2.5 四种记忆类型**

记忆类的抽象基类 BaseMemory 参考 `hello_agents/memory/base.py`

（1）工作记忆（WorkingMemory）

工作记忆是记忆系统中最活跃的部分，它负责存储当前对话会话中的临时信息。工作记忆的设计重点在于快速访问和自动清理，这种设计确保了系统的响应速度和资源效率。

工作记忆采用了纯内存存储方案，配合TTL（Time To Live）机制进行自动清理。这种设计的优势在于访问速度极快，但也意味着工作记忆的内容在系统重启后会丢失。这种特性正好符合工作记忆的定位，存储临时的、易变的信息。

<details>
<summary>hello_agents/memory/types/working.py 中的 WorkingMemory 类</summary>

```python
class WorkingMemory(BaseMemory):
    """工作记忆实现
    
    特点：
    - 容量有限（通常10-20条记忆）
    - 时效性强（会话级别）
    - 优先级管理
    - 自动清理过期记忆
    """
    
    def __init__(self, config: MemoryConfig, storage_backend=None):
        super().__init__(config, storage_backend)
        
        # 工作记忆特定配置
        self.max_capacity = self.config.working_memory_capacity
        self.max_tokens = self.config.working_memory_tokens
        # 纯内存TTL（分钟），可通过在 MemoryConfig 上挂载 working_memory_ttl_minutes 覆盖
        self.max_age_minutes = getattr(self.config, 'working_memory_ttl_minutes', 120)
        self.current_tokens = 0
        self.session_start = datetime.now()
        
        # 内存存储（工作记忆不需要持久化）
        self.memories: List[MemoryItem] = []
        
        # 使用优先级队列管理记忆
        self.memory_heap = []  # (priority, timestamp, memory_item)
    
    def add(self, memory_item: MemoryItem) -> str:
        """添加工作记忆"""
        # 过期清理
        self._expire_old_memories()
        # 计算优先级（重要性 + 时间衰减）
        priority = self._calculate_priority(memory_item)
        
        # 添加到堆中
        heapq.heappush(self.memory_heap, (-priority, memory_item.timestamp, memory_item))
        self.memories.append(memory_item)
        
        # 更新token计数
        self.current_tokens += len(memory_item.content.split())
        
        # 检查容量限制
        self._enforce_capacity_limits()
        
        return memory_item.id
    
    def retrieve(self, query: str, limit: int = 5, user_id: str = None, **kwargs) -> List[MemoryItem]:
        """检索工作记忆 - 混合语义向量检索和关键词匹配"""
    ...
```

</details>

（2）情景记忆（EpisodicMemory）

情景记忆负责存储具体的事件和经历，它的设计重点在于保持事件的完整性和时间序列关系。

情景记忆采用了 SQLite+Qdrant 的混合存储方案，SQLite 负责结构化数据的存储和复杂查询，Qdrant 负责高效的向量检索。

情景记忆的检索实现展现了复杂的多因素评分机制。它不仅考虑了语义相似度，还加入了时间近因性的考量，最终通过重要性权重进行调节。

评分公式为：`(向量相似度 × 0.8 + 时间近因性 × 0.2) × (0.8 + 重要性 × 0.4)`，确保检索结果既语义相关又时间相关。

（3）语义记忆（SemanticMemory）

语义记忆是记忆系统中最复杂的部分，它负责存储抽象的概念、规则和知识。

语义记忆的设计重点在于知识的结构化表示和智能推理能力。

语义记忆采用了 Neo4j 图数据库和 Qdrant 向量数据库的混合架构，这种设计让系统既能进行快速的语义检索，又能利用知识图谱进行复杂的关系推理。

语义记忆的评分公式为：`(向量相似度 × 0.7 + 图相似度 × 0.3) × (0.8 + 重要性 × 0.4)`。这种设计的核心思想是：

* **向量检索权重（0.7）**：语义相似度是主要因素，确保检索结果与查询语义相关
* **图检索权重（0.3）**：关系推理作为补充，发现概念间的隐含关联
* **重要性权重范围[0.8, 1.2]**：避免重要性过度影响相似度排序，保持检索的准确性


（4）感知记忆（PerceptualMemory）

**感知记忆**支持**文本、图像、音频等多种模态的数据存储和检索**。

采用模态分离的存储策略，为不同模态的数据创建独立的向量集合，这种设计避免了维度不匹配的问题，同时保证了检索的准确性：

**感知记忆的检索**支持**同模态**和**跨模态**两种模式。同模态检索利用专业的编码器进行精确匹配，而跨模态检索则需要更复杂的语义对齐机制：

**感知记忆的评分公式**为：`(向量相似度 × 0.8 + 时间近因性 × 0.2) × (0.8 + 重要性 × 0.4)`。

感知记忆的评分机制还支持跨模态检索，通过统一的向量空间实现文本、图像、音频等不同模态数据的语义对齐。

当进行跨模态检索时，系统会自动调整评分权重，确保检索结果的多样性和准确性。此外，感知记忆中的时间近因性计算采用了指数衰减模型：

这种时间衰减模型模拟了人类记忆中的遗忘曲线，确保了感知记忆系统能够优先检索到时间上更相关的记忆内容。

### 8.3 **RAG系统：知识检索增强**

**8.3.1 RAG的基础知识**

（1）什么是RAG？

检索增强生成（Retrieval-Augmented Generation，RAG）是一种结合了信息检索和文本生成的技术。

它的核心思想是：在生成回答之前，先从外部知识库中检索相关信息，然后将检索到的信息作为上下文提供给大语言模型，从而生成更准确、更可靠的回答。

因此，检索增强生成可以拆分为三个词汇：

- **检索**：指从知识库中查询相关内容；
- **增强**：将检索结果融入提示词，辅助模型生成；
- **生成**：输出兼具准确性与透明度的答案。

（2）基本工作流程

一个完整的 RAG 应用流程主要分为两大核心环节：

1. 在数据准备阶段，系统通过数据提取、文本分割和向量化，将外部知识构建成一个可检索的数据库
2. 随后在应用阶段，系统会响应用户的提问，从数据库中检索相关信息，将其注入 Prompt，并最终驱动大语言模型生成答案。

（3）发展历程

- 朴素RAG（Naive RAG, 2020-2021）
- 高级RAG（Advanced RAG, 2022-2023）
- 模块化RAG（Modular RAG, 2023-至今）

**8.3.2 RAG系统工作原理**

![RAG系统的核心工作原理.png](images%2FRAG%E7%B3%BB%E7%BB%9F%E7%9A%84%E6%A0%B8%E5%BF%83%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.png)

RAG系统的两个主要工作模式：

1. 数据处理流程：处理和存储知识文档，在这里我们采取工具 Markitdown，设计思路是将传入的一切外部知识源统一转化为 Markdown 格式进行处理。
2. 查询与生成流程：根据查询检索相关信息并生成回答。

**8.3.3 快速上手 RAG 功能**

**8.3.4 RAG系统架构设计**

在这一节中，我们采取与记忆系统不同的方式讲解。因为 `Memory_tool` 是系统性的实现，而 RAG 在我们的设计中被定义为一种工具，可以梳理为一条 pipeline。

RAG系统的核心架构可以概括为"五层七步"的设计模式：

```
用户层：RAGTool统一接口
  ↓
应用层：智能问答、搜索、管理
  ↓  
处理层：文档解析、分块、向量化
  ↓
存储层：向量数据库、文档存储
  ↓
基础层：嵌入模型、LLM、数据库
```

这种分层设计的优势在于每一层都可以独立优化和替换，同时保持整体系统的稳定性。

例如，可以轻松地将嵌入模型从 sentence-transformers 切换到百炼 API，而不影响上层的业务逻辑。

同样的，这些处理的流程代码是完全可复用的，也可以选取自己需要的部分放进自己的项目中。RAGTool 作为 RAG 系统的统一入口，提供了简洁的 API 接口。

<details>
<summary>hello_agents/tools/builtin/rag_tool.py</summary>

```python
class RAGTool(Tool):
    """RAG工具
    
    提供完整的 RAG 能力：
    - 添加多格式文档（PDF、Office、图片、音频等）
    - 智能检索与召回
    - LLM 增强问答
    - 知识库管理
    """
    
    def __init__(
        self,
        knowledge_base_path: str = "./knowledge_base",
        qdrant_url: str = None,
        qdrant_api_key: str = None,
        collection_name: str = "rag_knowledge_base",
        rag_namespace: str = "default"
    ):
        # 初始化RAG管道
        self._pipelines: Dict[str, Dict[str, Any]] = {}
        self.llm = HelloAgentsLLM()
        
        # 创建默认管道
        default_pipeline = create_rag_pipeline(
            qdrant_url=self.qdrant_url,
            qdrant_api_key=self.qdrant_api_key,
            collection_name=self.collection_name,
            rag_namespace=self.rag_namespace
        )
        self._pipelines[self.rag_namespace] = default_pipeline
```

</details>

整个处理流程如下所示：

```
任意格式文档 → MarkItDown转换 → Markdown文本 → 智能分块 → 向量化 → 存储检索
```

（1）多模态文档载入

RAG 系统的核心优势之一是其强大的多模态文档处理能力。系统使用 MarkItDown 作为统一的文档转换引擎，支持几乎所有常见的文档格式。

MarkItDown 是微软开源的通用文档转换工具，它是 HelloAgents RAG 系统的核心组件，负责将任意格式的文档统一转换为结构化的 Markdown 文本。

无论输入是 PDF、Word、Excel、图片还是音频，最终都会转换为标准的 Markdown 格式，然后进入统一的分块、向量化和存储流程。

<details>
<summary>hello_agents/memory/rag/pipeline.py 中的 _convert_to_markdown 函数</summary>

```python
def _convert_to_markdown(path: str) -> str:
    """
    Universal document reader using MarkItDown with enhanced PDF processing.
    核心功能：将任意格式文档转换为Markdown文本
    
    支持格式：
    - 文档：PDF、Word、Excel、PowerPoint
    - 图像：JPG、PNG、GIF（通过OCR）
    - 音频：MP3、WAV、M4A（通过转录）
    - 文本：TXT、CSV、JSON、XML、HTML
    - 代码：Python、JavaScript、Java等
    """
    if not os.path.exists(path):
        return ""
    
    # 对PDF文件使用增强处理
    ext = (os.path.splitext(path)[1] or '').lower()
    if ext == '.pdf':
        return _enhanced_pdf_processing(path)
    
    # 其他格式使用MarkItDown统一转换
    md_instance = _get_markitdown_instance()
    if md_instance is None:
        return _fallback_text_reader(path)
    
    try:
        result = md_instance.convert(path)
        markdown_text = getattr(result, "text_content", None)
        if isinstance(markdown_text, str) and markdown_text.strip():
            print(f"[RAG] MarkItDown转换成功: {path} -> {len(markdown_text)} chars Markdown")
            return markdown_text
        return ""
    except Exception as e:
        print(f"[WARNING] MarkItDown转换失败 {path}: {e}")
        return _fallback_text_reader(path)
```

</details>

（2）智能分块策略

经过 MarkItDown 转换后，所有文档都统一为标准的 Markdown 格式。这为后续的智能分块提供了结构化的基础。

HelloAgents 实现了专门针对 Markdown 格式的智能分块策略，充分利用 Markdown 的结构化特性进行精确分割。

Markdown 结构感知的分块流程：

```
标准Markdown文本 → 标题层次解析 → 段落语义分割 → Token计算分块 → 重叠策略优化 → 向量化准备
       ↓                ↓              ↓            ↓           ↓            ↓
   统一格式          #/##/###        语义边界      大小控制     信息连续性    嵌入向量
   结构清晰          层次识别        完整性保证    检索优化     上下文保持    相似度匹配
```

由于所有文档都已转换为 Markdown 格式，系统可以利用 Markdown 的标题结构（#、##、###等）进行精确的**语义分割**：

<details>
<summary>hello_agents/memory/rag/pipeline.py 中的 _split_paragraphs_with_headings 函数</summary>

```python
def _split_paragraphs_with_headings(text: str) -> List[Dict]:
    """根据标题层次分割段落，保持语义完整性"""
    lines = text.splitlines()
    heading_stack: List[str] = []
    paragraphs: List[Dict] = []
    buf: List[str] = []
    char_pos = 0
    
    def flush_buf(end_pos: int):
        if not buf:
            return
        content = "\n".join(buf).strip()
        if not content:
            return
        paragraphs.append({
            "content": content,
            "heading_path": " > ".join(heading_stack) if heading_stack else None,
            "start": max(0, end_pos - len(content)),
            "end": end_pos,
        })
    
    for ln in lines:
        raw = ln
        if raw.strip().startswith("#"):
            # 处理标题行
            flush_buf(char_pos)
            level = len(raw) - len(raw.lstrip('#'))
            title = raw.lstrip('#').strip()
            
            if level <= 0:
                level = 1
            if level <= len(heading_stack):
                heading_stack = heading_stack[:level-1]
            heading_stack.append(title)
            
            char_pos += len(raw) + 1
            continue
        
        # 段落内容累积
        if raw.strip() == "":
            flush_buf(char_pos)
            buf = []
        else:
            buf.append(raw)
        char_pos += len(raw) + 1
    
    flush_buf(char_pos)
    
    if not paragraphs:
        paragraphs = [{"content": text, "heading_path": None, "start": 0, "end": len(text)}]
    
    return paragraphs
```

</details>

在 Markdown 段落分割的基础上，系统进一步根据 Token 数量进行**智能分块**。

由于输入已经是结构化的 Markdown 文本，系统可以更精确地控制分块边界，确保每个分块既适合向量化处理，又保持 Markdown 结构的完整性：

<details>
<summary>hello_agents/memory/rag/pipeline.py 中的 _chunk_paragraphs 函数</summary>

```python
def _chunk_paragraphs(paragraphs: List[Dict], chunk_tokens: int, overlap_tokens: int) -> List[Dict]:
    """基于Token数量的智能分块"""
    chunks: List[Dict] = []
    cur: List[Dict] = []
    cur_tokens = 0
    i = 0
    
    while i < len(paragraphs):
        p = paragraphs[i]
        p_tokens = _approx_token_len(p["content"]) or 1
        
        if cur_tokens + p_tokens <= chunk_tokens or not cur:
            cur.append(p)
            cur_tokens += p_tokens
            i += 1
        else:
            # 生成当前分块
            content = "\n\n".join(x["content"] for x in cur)
            start = cur[0]["start"]
            end = cur[-1]["end"]
            heading_path = next((x["heading_path"] for x in reversed(cur) if x.get("heading_path")), None)
            
            chunks.append({
                "content": content,
                "start": start,
                "end": end,
                "heading_path": heading_path,
            })
            
            # 构建重叠部分
            if overlap_tokens > 0 and cur:
                kept: List[Dict] = []
                kept_tokens = 0
                for x in reversed(cur):
                    t = _approx_token_len(x["content"]) or 1
                    if kept_tokens + t > overlap_tokens:
                        break
                    kept.append(x)
                    kept_tokens += t
                cur = list(reversed(kept))
                cur_tokens = kept_tokens
            else:
                cur = []
                cur_tokens = 0
    
    # 处理最后一个分块
    if cur:
        content = "\n\n".join(x["content"] for x in cur)
        start = cur[0]["start"]
        end = cur[-1]["end"]
        heading_path = next((x["heading_path"] for x in reversed(cur) if x.get("heading_path")), None)
        
        chunks.append({
            "content": content,
            "start": start,
            "end": end,
            "heading_path": heading_path,
        })
    
    return chunks
```

</details>

同时为了兼容不同语言，系统实现了**针对中英文混合文本的 Token 估算算法**，这对于准确控制分块大小至关重要：

<details>
<summary>hello_agents/memory/rag/pipeline.py 中的 _approx_token_len 函数</summary>

```python
def _approx_token_len(text: str) -> int:
    """近似估计Token长度，支持中英文混合"""
    # CJK字符按1 token计算
    cjk = sum(1 for ch in text if _is_cjk(ch))
    # 其他字符按空白分词计算
    non_cjk_tokens = len([t for t in text.split() if t])
    return cjk + non_cjk_tokens

def _is_cjk(ch: str) -> bool:
    """判断是否为CJK字符"""
    code = ord(ch)
    return (
        0x4E00 <= code <= 0x9FFF or  # CJK统一汉字
        0x3400 <= code <= 0x4DBF or  # CJK扩展A
        0x20000 <= code <= 0x2A6DF or # CJK扩展B
        0x2A700 <= code <= 0x2B73F or # CJK扩展C
        0x2B740 <= code <= 0x2B81F or # CJK扩展D
        0x2B820 <= code <= 0x2CEAF or # CJK扩展E
        0xF900 <= code <= 0xFAFF      # CJK兼容汉字
    )
```

</details>

（3）统一嵌入与向量存储

嵌入模型是 RAG 系统的核心，它负责将文本转换为高维向量，使得计算机能够理解和比较文本的语义相似性。RAG 系统的检索能力很大程度上取决于嵌入模型的质量和向量存储的效率。

HelloAgents 实现了统一的嵌入接口。在这里为了演示，使用百炼 API，如果尚未配置可以切换为本地的 all-MiniLM-L6-v2 模型，如果两种方案都不支持，也配置了 TF-IDF 算法来兜底。实际使用可以替换为自己想要的模型或者 API，也可以尝试去扩展框架内容~

<details>
<summary>hello_agents/memory/rag/pipeline.py 中的 index_chunks 函数</summary>

```python
def index_chunks(
    store = None, 
    chunks: List[Dict] = None, 
    cache_db: Optional[str] = None, 
    batch_size: int = 64,
    rag_namespace: str = "default"
) -> None:
    """
    Index markdown chunks with unified embedding and Qdrant storage.
    Uses百炼 API with fallback to sentence-transformers.
    """
    if not chunks:
        print("[RAG] No chunks to index")
        return
    
    # 使用统一嵌入模型
    embedder = get_text_embedder()
    dimension = get_dimension(384)
    
    # 创建默认Qdrant存储
    if store is None:
        store = _create_default_vector_store(dimension)
        print(f"[RAG] Created default Qdrant store with dimension {dimension}")
    
    # 预处理Markdown文本以获得更好的嵌入质量
    processed_texts = []
    for c in chunks:
        raw_content = c["content"]
        processed_content = _preprocess_markdown_for_embedding(raw_content)
        processed_texts.append(processed_content)
    
    print(f"[RAG] Embedding start: total_texts={len(processed_texts)} batch_size={batch_size}")
    
    # 批量编码
    vecs: List[List[float]] = []
    for i in range(0, len(processed_texts), batch_size):
        part = processed_texts[i:i+batch_size]
        try:
            # 使用统一嵌入器（内部处理缓存）
            part_vecs = embedder.encode(part)
            
            # 标准化为List[List[float]]格式
            if not isinstance(part_vecs, list):
                if hasattr(part_vecs, "tolist"):
                    part_vecs = [part_vecs.tolist()]
                else:
                    part_vecs = [list(part_vecs)]
            
            # 处理向量格式和维度
            for v in part_vecs:
                try:
                    if hasattr(v, "tolist"):
                        v = v.tolist()
                    v_norm = [float(x) for x in v]
                    
                    # 维度检查和调整
                    if len(v_norm) != dimension:
                        print(f"[WARNING] 向量维度异常: 期望{dimension}, 实际{len(v_norm)}")
                        if len(v_norm) < dimension:
                            v_norm.extend([0.0] * (dimension - len(v_norm)))
                        else:
                            v_norm = v_norm[:dimension]
                    
                    vecs.append(v_norm)
                except Exception as e:
                    print(f"[WARNING] 向量转换失败: {e}, 使用零向量")
                    vecs.append([0.0] * dimension)
                    
        except Exception as e:
            print(f"[WARNING] Batch {i} encoding failed: {e}")
            # 实现重试机制
            # ... 重试逻辑 ...
        
        print(f"[RAG] Embedding progress: {min(i+batch_size, len(processed_texts))}/{len(processed_texts)}")
```

</details>

**8.3.5 高级检索策略**

RAG 系统的检索能力是其核心竞争力。在实际应用中，用户的查询表述与文档中的实际内容可能存在用词差异，导致相关文档无法被检索到。

为了解决这个问题，HelloAgents 实现了三种互补的高级检索策略：

- 多查询扩展（MQE）
- 假设文档嵌入（HyDE）
- 统一的扩展检索框架

（1）多查询扩展（MQE）

多查询扩展（Multi-Query Expansion）是一种通过生成语义等价的多样化查询来提高检索召回率的技术。

这种方法的核心洞察是：同一个问题可以有多种不同的表述方式，而不同的表述可能匹配到不同的相关文档。

例如，"如何学习Python"可以扩展为"Python入门教程"、"Python学习方法"、"Python编程指南"等多个查询。

通过并行执行这些扩展查询并合并结果，系统能够覆盖更广泛的相关文档，避免因用词差异而遗漏重要信息。

MQE 的优势在于它能够自动理解用户查询的多种可能含义，特别是对于模糊查询或专业术语查询效果显著。系统使用 LLM 生成扩展查询，确保扩展的多样性和语义相关性：

<details>
<summary>hello_agents/memory/rag/pipeline.py 中的 _prompt_mqe 函数</summary>

```python
def _prompt_mqe(query: str, n: int) -> List[str]:
    """使用LLM生成多样化的查询扩展"""
    try:
        from ...core.llm import HelloAgentsLLM
        llm = HelloAgentsLLM()
        prompt = [
            {"role": "system", "content": "你是检索查询扩展助手。生成语义等价或互补的多样化查询。使用中文，简短，避免标点。"},
            {"role": "user", "content": f"原始查询：{query}\n请给出{n}个不同表述的查询，每行一个。"}
        ]
        text = llm.invoke(prompt)
        lines = [ln.strip("- \t") for ln in (text or "").splitlines()]
        outs = [ln for ln in lines if ln]
        return outs[:n] or [query]
    except Exception:
        return [query]
```

</details>

（2）假设文档嵌入（HyDE）

假设文档嵌入（Hypothetical Document Embeddings，HyDE）是一种创新的检索技术，它的核心思想是"用答案找答案"。

传统的检索方法是用问题去匹配文档，但问题和答案在语义空间中的分布往往存在差异——问题通常是疑问句，而文档内容是陈述句。

HyDE 通过**让 LLM 先生成一个假设性的答案段落**，然后用这个答案段落去检索真实文档，从而缩小了查询和文档之间的语义鸿沟。

<details>
<summary>hello_agents/memory/rag/pipeline.py 中的 _prompt_hyde 函数</summary>

```python
def _prompt_hyde(query: str) -> Optional[str]:
    """生成假设性文档用于改善检索"""
    try:
        from ...core.llm import HelloAgentsLLM
        llm = HelloAgentsLLM()
        prompt = [
            {"role": "system", "content": "根据用户问题，先写一段可能的答案性段落，用于向量检索的查询文档（不要分析过程）。"},
            {"role": "user", "content": f"问题：{query}\n请直接写一段中等长度、客观、包含关键术语的段落。"}
        ]
        return llm.invoke(prompt)
    except Exception:
        return None
```

</details>

（3）扩展检索框架

HelloAgents 将 MQE 和 HyDE 两种策略整合到统一的扩展检索框架中。

系统通过 enable_mqe 和 enable_hyde 参数让用户可以根据具体场景选择启用哪些策略：对于需要高召回率的场景可以同时启用两种策略，对于性能敏感的场景可以只使用基础检索。

扩展检索的核心机制是"扩展-检索-合并"三步流程

- 首先，系统根据原始查询生成多个扩展查询（包括MQE生成的多样化查询和HyDE生成的假设文档）
- 然后，对每个扩展查询并行执行向量检索，获取候选文档池；
- 最后，通过去重和分数排序合并所有结果，返回最相关的top-k文档。

这种设计的巧妙之处在于，它通过`candidate_pool_multiplier`参数（默认为4）扩大候选池，确保有足够的候选文档进行筛选，同时通过智能去重避免返回重复内容。

<details>
<summary>hello_agents/memory/rag/pipeline.py 中的 search_vectors_expanded 函数</summary>

```python
def search_vectors_expanded(
    store = None,
    query: str = "",
    top_k: int = 8,
    rag_namespace: Optional[str] = None,
    only_rag_data: bool = True,
    score_threshold: Optional[float] = None,
    enable_mqe: bool = False,
    mqe_expansions: int = 2,
    enable_hyde: bool = False,
    candidate_pool_multiplier: int = 4,
) -> List[Dict]:
    """
    Search with query expansion using unified embedding and Qdrant.
    """
    if not query:
        return []
    
    # 创建默认存储
    if store is None:
        store = _create_default_vector_store()
    
    # 查询扩展
    expansions: List[str] = [query]
    
    if enable_mqe and mqe_expansions > 0:
        expansions.extend(_prompt_mqe(query, mqe_expansions))
    if enable_hyde:
        hyde_text = _prompt_hyde(query)
        if hyde_text:
            expansions.append(hyde_text)

    # 去重和修剪
    uniq: List[str] = []
    for e in expansions:
        if e and e not in uniq:
            uniq.append(e)
    expansions = uniq[: max(1, len(uniq))]

    # 分配候选池
    pool = max(top_k * candidate_pool_multiplier, 20)
    per = max(1, pool // max(1, len(expansions)))

    # 构建RAG数据过滤器
    where = {"memory_type": "rag_chunk"}
    if only_rag_data:
        where["is_rag_data"] = True
        where["data_source"] = "rag_pipeline"
    if rag_namespace:
        where["rag_namespace"] = rag_namespace

    # 收集所有扩展查询的结果
    agg: Dict[str, Dict] = {}
    for q in expansions:
        qv = embed_query(q)
        hits = store.search_similar(
            query_vector=qv, 
            limit=per, 
            score_threshold=score_threshold, 
            where=where
        )
        for h in hits:
            mid = h.get("metadata", {}).get("memory_id", h.get("id"))
            s = float(h.get("score", 0.0))
            if mid not in agg or s > float(agg[mid].get("score", 0.0)):
                agg[mid] = h
    
    # 按分数排序返回
    merged = list(agg.values())
    merged.sort(key=lambda x: float(x.get("score", 0.0)), reverse=True)
    return merged[:top_k]
```

</details>

实际应用中，这三种策略的组合使用效果最佳 ，MQE 擅长处理用词多样性问题，HyDE 擅长处理语义鸿沟问题，而统一框架则确保了结果的质量和多样性。

对于一般查询，建议启用 MQE；对于专业领域查询，建议同时启用 MQE 和 HyDE；对于性能敏感场景，可以只使用基础检索或仅启用 MQE。

### 8.4 **构建智能文档问答助手**

**8.4.1 案例背景与目标**