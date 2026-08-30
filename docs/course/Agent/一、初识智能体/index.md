# **一、初识智能体**

在本章，让我们一起探讨几个问题：智能体是什么？它有哪些主要的类型？它又是如何与我们所处的世界进行交互的？

## 1.1 **什么是智能体？**

**智能体的定义**：智能体是任何能够通过**传感器**感知其所处**环境**以获得**观测（Observation）信息，**并**自主**地通过**执行器**采取**行动（Action）**以达成特定目标的实体。

- **环境（Environment）**：智能体所处的外部世界
- **传感器（Sensors）**：摄像头、麦克风、雷达或各类应用程序编程接口（API）
- **执行器（Actuators）**：物理设备（如机械臂、方向盘）或虚拟工具（如执行一段代码、调用一个服务）
- **自主性（Autonomy）**：智能体并非只是被动响应外部刺激或严格执行预设指令的程序，它能够基于其感知和内部状态进行独立决策，以达成其设计目标


### **1.1.1 传统视角下的智能体**：

在当前**大语言模型（Large Language Model, LLM）**的热潮出现之前，人工智能的先驱们已经对“智能体”这一概念进行了数十年的探索与构建。

这些如今我们称之为“传统智能体”的范式，并非单一的静态概念，而是经历了一条从简单到复杂、从被动反应到主动学习的清晰演进路线。

1. **反射智能体（Simple Reflex Agent）**：
    - 由明确设计的“条件-动作”规则构成
    - 完全依赖于当前的感知输入，不具备记忆或预测能力
    - 难以应对决策依据不足的环境
2. **基于模型的反射智能体（Model-Based Reflex Agent）**：
    - 引入了”状态“的概念
    - 通过**世界模型（World Model）**追踪和理解环境中无法被直接感知的方面
3. **基于目标的智能体（Goal-Based Agent）**：
    - 主动地、有预见性地选择能够导向某个特定未来状态的行动
4. **基于效用的智能体（Utility-Based Agent）**：
    - 不再是简单地达成某个特定状态，而是最大化期望效用
5. **学习型智能体（Learning Agent）**：
    - 不依赖预设，而是通过与环境的互动自主学习
    - **强化学习（Reinforcement Learning, RL）**是实现这一思想最具代表性的路径
    - 一个学习型智能体包含一个性能元件（即我们前面讨论的各类智能体）和一个学习元件。学习元件通过观察性能元件在环境中的行动所带来的结果来不断修正性能元件的决策策略

### **1.1.2 大语言模型驱动的新范式**：

以 **GPT（Generative Pre-trained Transformer）**为代表的大语言模型的出现，正在显著改变智能体的构建方法与能力边界。

由大语言模型驱动的 LLM 智能体，其核心决策机制与传统智能体存在本质区别，从而赋予了其一系列全新的特性。

![表 1.1 传统智能体与 LLM 驱动智能体的核心对比.png](..%2Fimages%2F%E8%A1%A8%201.1%20%E4%BC%A0%E7%BB%9F%E6%99%BA%E8%83%BD%E4%BD%93%E4%B8%8E%20LLM%20%E9%A9%B1%E5%8A%A8%E6%99%BA%E8%83%BD%E4%BD%93%E7%9A%84%E6%A0%B8%E5%BF%83%E5%AF%B9%E6%AF%94.png)

- 传统智能体的能力源于工程师的显式编程与知识构建，其行为模式是确定且有边界的
- LLM 智能体则通过在海量数据上的预训练，获得了隐式的世界模型与强大的涌现能力，使其能够以更灵活、更通用的方式应对复杂任务

这种差异使得 LLM 智能体可以直接处理**高层级、模糊且充满上下文信息**的自然语言指令。

总而言之，我们正从开发专用自动化工具转向构建能自主解决问题的系统。核心不再是编写代码，而是**引导一个通用的“大脑”去规划、行动和学习**。

### **1.1.3 智能体的类型**：

- **基于内部决策架构的分类**：
    - 从简单的反应式智能体，到引入内部模型的模型式智能体，再到更具前瞻性的基于目标和基于效用的智能体
    - 此外，学习能力则是一种可赋予上述所有类型的元能力，使其能通过经验自我改进
- **基于时间与反应性的分类**：追求速度的反应性（Reactivity）与追求最优解的规划性（Deliberation）之间如何权衡？
    - 反应式智能体：速度快、计算开销低
    - 规划式智能体：基于目标和基于效用的智能体是典型的规划式智能体
    - 混合式智能体：在一个“思考-行动-观察”的循环中运作
- **基于知识表示的分类**：
    - 亚符号主义 AI：连接主义
    - 符号主义 AI：传统人工智能
    - 神经符号主义 AI：目标是创造出既能像神经网络一样从数据中学习，又能像符号系统一样进行逻辑推理的混合智能体

## 1.2 **智能体的构成与运行原理**

### **1.2.1 任务环境定义（PEAS 模型）**：

- 性能度量（Performance）
- 环境（Environment）
- 执行器（Actuators）
- 传感器（Sensors）

智能旅行助手的 PEAS 描述：

![表 1.2 智能旅行助手的 PEAS 描述.png](..%2Fimages%2F%E8%A1%A8%201.2%20%E6%99%BA%E8%83%BD%E6%97%85%E8%A1%8C%E5%8A%A9%E6%89%8B%E7%9A%84%20PEAS%20%E6%8F%8F%E8%BF%B0.png)

在实践中，LLM 智能体所处的数字环境展现出若干复杂特性，这些特性直接影响着智能体的设计

- **部分可观察的**：要求智能体必须具备记忆（记住已查询过的航线）和探索（尝试不同的查询日期）能力
- **确定性或随机性**：要求智能体必须具备处理不确定性、监控变化并及时决策的能力
- **多智能体**：其他智能体的行动（例如，订走最后一张特价票）会直接改变所处环境的状态，这对智能体的快速响应和策略选择提出了更高要求
- **序贯且动态**：“序贯”意味着当前动作会影响未来；而“动态”则意味着环境自身可能在智能体决策时发生变化。这就要求智能体的“感知-思考-行动-观察”循环必须能够快速、灵活地适应持续变化的世界。

### **1.2.2 智能体的运行机制**：

智能体并非一次性完成任务，而是通过一个持续的循环与环境进行交互，这个核心机制被称为**智能体循环（Agent Loop）**。

![图 1.5 智能体与环境交互的基本循环.png](..%2Fimages%2F%E5%9B%BE%201.5%20%E6%99%BA%E8%83%BD%E4%BD%93%E4%B8%8E%E7%8E%AF%E5%A2%83%E4%BA%A4%E4%BA%92%E7%9A%84%E5%9F%BA%E6%9C%AC%E5%BE%AA%E7%8E%AF.png)

这个循环主要包含以下几个相互关联的阶段：

1. **感知（Perception）**：智能体通过其传感器（例如，API 的监听端口、用户输入接口）接收来自环境的输入信息。这些信息，即**观察（Observation）**，既可以是用户的初始指令，也可以是上一步行动所导致的环境状态变化反馈。
2. **思考（Thought）**：接收到观察信息后，智能体进入其核心决策阶段。对于 LLM 智能体而言，通常是由大语言模型驱动的内部推理过程，可进一步细分为两个关键环节：
    - **规划（Planning）**：智能体基于当前的观察和其内部记忆，更新对任务和环境的理解，并制定或调整一个行动计划。
    - **工具选择（Tool Selection）**：根据当前计划，智能体从其可用的工具库中，选择最适合执行下一步骤的工具，并确定调用该工具所需的具体参数。
3. **行动（Action）**：决策完成后，智能体通过其执行器（Actuators）执行具体的行动。这通常表现为调用一个选定的工具（如代码解释器、搜索引擎 API），从而对环境施加影响，意图改变环境的状态。

行动并非循环的终点。智能体的行动会引起环境的**状态变化（State Change）**，环境随即会产生一个新的**观察**作为结果反馈。

这个新的观察又会在下一轮循环中被智能体的感知系统捕获，形成一个持续的“感知-思考-行动-观察”的闭环。智能体正是通过不断重复这一循环，逐步推进任务，从初始状态向目标状态演进。

### **1.2.3 智能体的感知与行动**：

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


## 1.3 **第一个智能体**

在前面的小节，我们学习了智能体的任务环境、核心运行机制以及 `Thought-Action-Observation` 交互范式。

在本节中，我们将引导您使用几行简单的 Python 代码，从零开始构建一个可以工作的智能旅行助手。

这个过程将遵循我们刚刚学到的理论循环，让您直观地感受到一个智能体是如何“思考”并与外部“工具”互动的。

### **1.3.1 准备工作**

- `requests`：HTTP 库，从 Python 程序中访问网络 API
- `tavily-python`：AI 搜索 API 客户端，用于获取实时的网络搜索结果
- `openai`：OpenAI 官方提供的 Python SDK，用于调用 GPT 等大语言模型服务

请先通过以下命令安装它们：

```bash
pip install requests tavily-python openai
```

（1）指令模板

驱动真实 LLM 的关键在于**提示工程（Prompt Engineering）**。

我们需要设计一个“指令模板”，告诉 LLM 它应该扮演什么角色、拥有哪些工具、以及如何格式化它的思考和行动。这是我们智能体的“说明书”，它将作为`system_prompt`传递给 LLM。

```python
AGENT_SYSTEM_PROMPT = """
你是一个智能旅行助手。你的任务是分析用户的请求，并使用可用工具一步步地解决问题。

# 可用工具:
- `get_weather(city: str)`: 查询指定城市的实时天气。
- `get_attraction(city: str, weather: str)`: 根据城市和天气搜索推荐的旅游景点。

# 输出格式要求:
你的每次回复必须严格遵循以下格式，包含一对Thought和Action：

Thought: [你的思考过程和下一步计划]
Action: [你要执行的具体行动]

Action的格式必须是以下之一：
1. 调用工具：function_name(arg_name="arg_value")
2. 结束任务：Finish[最终答案]

# 重要提示:
- 每次只输出一对Thought-Action
- Action必须在同一行，不要换行
- 当收集到足够信息可以回答用户问题时，必须使用 Action: Finish[最终答案] 格式结束

请开始吧！
"""
```

（2）工具 1：查询真实天气

我们将使用免费的天气查询服务 `wttr.in`，它能以 JSON 格式返回指定城市的天气数据。

```python
import requests

def get_weather(city: str) -> str:
    """
    通过调用 wttr.in API 查询真实的天气信息。
    """
    # API端点，我们请求JSON格式的数据
    url = f"https://wttr.in/{city}?format=j1"
    
    try:
        # 发起网络请求
        response = requests.get(url)
        # 检查响应状态码是否为200 (成功)
        response.raise_for_status() 
        # 解析返回的JSON数据
        data = response.json()
        
        # 提取当前天气状况
        current_condition = data['current_condition'][0]
        weather_desc = current_condition['weatherDesc'][0]['value']
        temp_c = current_condition['temp_C']
        
        # 格式化成自然语言返回
        return f"{city}当前天气:{weather_desc}，气温{temp_c}摄氏度"
        
    except requests.exceptions.RequestException as e:
        # 处理网络错误
        return f"错误:查询天气时遇到网络问题 - {e}"
    except (KeyError, IndexError) as e:
        # 处理数据解析错误
        return f"错误:解析天气数据失败，可能是城市名称无效 - {e}"
```

（3）工具 2：搜索并推荐旅游景点

我们将定义一个新工具 `get_attraction`，它会根据城市和天气状况，在互联网上搜索合适的景点：

```python
import os
from tavily import TavilyClient

def get_attraction(city: str, weather: str) -> str:
    """
    根据城市和天气，使用Tavily Search API搜索并返回优化后的景点推荐。
    """
    # 1. 从环境变量中读取API密钥
    api_key = os.environ.get("TAVILY_API_KEY")
    if not api_key:
        return "错误:未配置TAVILY_API_KEY环境变量。"

    # 2. 初始化Tavily客户端
    tavily = TavilyClient(api_key=api_key)
    
    # 3. 构造一个精确的查询
    query = f"'{city}' 在'{weather}'天气下最值得去的旅游景点推荐及理由"
    
    try:
        # 4. 调用API，include_answer=True会返回一个综合性的回答
        response = tavily.search(query=query, search_depth="basic", include_answer=True)
        
        # 5. Tavily返回的结果已经非常干净，可以直接使用
        # response['answer'] 是一个基于所有搜索结果的总结性回答
        if response.get("answer"):
            return response["answer"]
        
        # 如果没有综合性回答，则格式化原始结果
        formatted_results = []
        for result in response.get("results", []):
            formatted_results.append(f"- {result['title']}: {result['content']}")
        
        if not formatted_results:
             return "抱歉，没有找到相关的旅游景点推荐。"

        return "根据搜索，为您找到以下信息:\n" + "\n".join(formatted_results)

    except Exception as e:
        return f"错误:执行Tavily搜索时出现问题 - {e}"
```

最后，我们将所有工具函数放入一个字典，供主循环调用：

```python
# 将所有工具函数放入一个字典，方便后续调用
available_tools = {
    "get_weather": get_weather,
    "get_attraction": get_attraction,
}
```

### **1.3.2 接入大语言模型**

当前，许多 LLM 服务提供商（包括 OpenAI、Azure、以及众多开源模型服务框架如 Ollama、vLLM 等）都遵循了与 OpenAI API 相似的接口规范。这种标准化为开发者带来了极大的便利。

智能体的自主决策能力来源于 LLM。我们将实现一个通用的客户端 `OpenAICompatibleClient`，它可以连接到任何兼容 OpenAI 接口规范的 LLM 服务。

```python
from openai import OpenAI

class OpenAICompatibleClient:
    """
    一个用于调用任何兼容OpenAI接口的LLM服务的客户端。
    """
    def __init__(self, model: str, api_key: str, base_url: str):
        self.model = model
        self.client = OpenAI(api_key=api_key, base_url=base_url)

    def generate(self, prompt: str, system_prompt: str) -> str:
        """调用LLM API来生成回应。"""
        print("正在调用大语言模型...")
        try:
            messages = [
                {'role': 'system', 'content': system_prompt},
                {'role': 'user', 'content': prompt}
            ]
            response = self.client.chat.completions.create(
                model=self.model,
                messages=messages,
                stream=False
            )
            answer = response.choices[0].message.content
            print("大语言模型响应成功。")
            return answer
        except Exception as e:
            print(f"调用LLM API时发生错误: {e}")
            return "错误:调用语言模型服务时出错。"
```

要实例化此类，需要提供三个信息：`API_KEY`、`BASE_URL` 和 `MODEL_ID`，具体值取决于您使用的服务商（如 OpenAI 官方、Azure、或 Ollama 等本地模型）。

### **1.3.3 执行行动循环**

```python
import re

# --- 1. 配置LLM客户端 ---
# 请根据您使用的服务，将这里替换成对应的凭证和地址
API_KEY = "YOUR_API_KEY"
BASE_URL = "YOUR_BASE_URL"
MODEL_ID = "YOUR_MODEL_ID"
TAVILY_API_KEY="YOUR_Tavily_KEY"
os.environ['TAVILY_API_KEY'] = "YOUR_TAVILY_API_KEY"

llm = OpenAICompatibleClient(
    model=MODEL_ID,
    api_key=API_KEY,
    base_url=BASE_URL
)

# --- 2. 初始化 ---
user_prompt = "你好，请帮我查询一下今天北京的天气，然后根据天气推荐一个合适的旅游景点。"
prompt_history = [f"用户请求: {user_prompt}"]

print(f"用户输入: {user_prompt}\n" + "="*40)

# --- 3. 运行主循环 ---
for i in range(5): # 设置最大循环次数
    print(f"--- 循环 {i+1} ---\n")
    
    # 3.1. 构建Prompt
    full_prompt = "\n".join(prompt_history)
    
    # 3.2. 调用LLM进行思考
    llm_output = llm.generate(full_prompt, system_prompt=AGENT_SYSTEM_PROMPT)
    # 模型可能会输出多余的Thought-Action，需要截断
    match = re.search(r'(Thought:.*?Action:.*?)(?=\n\s*(?:Thought:|Action:|Observation:)|\Z)', llm_output, re.DOTALL)
    if match:
        truncated = match.group(1).strip()
        if truncated != llm_output.strip():
            llm_output = truncated
            print("已截断多余的 Thought-Action 对")
    print(f"模型输出:\n{llm_output}\n")
    prompt_history.append(llm_output)
    
    # 3.3. 解析并执行行动
    action_match = re.search(r"Action: (.*)", llm_output, re.DOTALL)
    if not action_match:
        observation = "错误: 未能解析到 Action 字段。请确保你的回复严格遵循 'Thought: ... Action: ...' 的格式。"
        observation_str = f"Observation: {observation}"
        print(f"{observation_str}\n" + "="*40)
        prompt_history.append(observation_str)
        continue
    action_str = action_match.group(1).strip()

    if action_str.startswith("Finish"):
        final_answer = re.match(r"Finish\[(.*)\]", action_str).group(1)
        print(f"任务完成，最终答案: {final_answer}")
        break
    
    tool_name = re.search(r"(\w+)\(", action_str).group(1)
    args_str = re.search(r"\((.*)\)", action_str).group(1)
    kwargs = dict(re.findall(r'(\w+)="([^"]*)"', args_str))

    if tool_name in available_tools:
        observation = available_tools[tool_name](**kwargs)
    else:
        observation = f"错误:未定义的工具 '{tool_name}'"

    # 3.4. 记录观察结果
    observation_str = f"Observation: {observation}"
    print(f"{observation_str}\n" + "="*40)
    prompt_history.append(observation_str)
```

通过以上步骤，我们构建了一个完整的、由真实 LLM 驱动的智能体。其核心在于“工具”和“提示工程”的结合，这正是当前主流智能体框架（如 LangChain、LlamaIndex 等）的设计精髓。

### **1.3.4 运行案例分析**

以下输出完整地展示了一个成功的智能体执行流程。通过对这个三轮循环的分析，我们可以清晰地看到智能体解决问题的核心能力。

```
用户输入: 你好，请帮我查询一下今天北京的天气，然后根据天气推荐一个合适的旅游景点。
========================================
--- 循环 1 ---

正在调用大语言模型...
大语言模型响应成功。
模型输出:
Thought: 首先需要获取北京今天的天气情况，之后再根据天气情况来推荐旅游景点。
Action: get_weather(city="北京")

Observation: 北京当前天气:Sunny，气温26摄氏度
========================================      
--- 循环 2 ---

正在调用大语言模型...
大语言模型响应成功。
模型输出:
Thought: 现在已经知道了北京今天的天气是晴朗且温度适中，接下来可以基于这个信息来推荐一个适合的旅游景点了。
Action: get_attraction(city="北京", weather="Sunny")

Observation: 北京在晴天最值得去的旅游景点是颐和园，因其美丽的湖景和古建筑。另一个推荐是长城，因其壮观的景观和历史意义。
========================================
--- 循环 3 ---

正在调用大语言模型...
大语言模型响应成功。
模型输出:
Thought: 已经获得了两个适合晴天游览的景点建议，现在可以根据这些信息给用户提供满意的答复。
Action: Finish[今天北京的天气是晴朗的，气温26摄氏度，非常适合外出游玩。我推荐您去颐和园欣赏美丽的湖景和古建筑，或者前往长城体验其壮观的景观和深厚的历史意义。希望您有一个愉快的旅行！]

任务完成，最终答案: 今天北京的天气是晴朗的，气温26摄氏度，非常适合外出游玩。我推荐您去颐和园欣赏美丽的湖景和古建筑，或者前往长城体验其壮观的景观和深厚的历史意义。希望您有一个愉快的旅行！
```

这个简单的旅行助手案例，集中演示了基于`Thought-Action-Observation`范式的智能体所具备的四项基本能力：

- 任务分解
- 工具调用
- 上下文理解
- 结果合成

正是通过这个循环的不断迭代，智能体才得以将一个模糊的用户意图，转化为一系列具体、可执行的步骤，并最终达成目标。

## 1.4 **智能体应用的协作模式**

### **1.4.1 作为开发者工具的智能体**：

在这种模式下，智能体被深度**集成到开发者的工作流**中，作为一种强大的辅助工具。它增强而非取代开发者的角色，通过自动化处理繁琐、重复的任务，让开发者能更专注于创造性的核心工作。这种人机协同的方式，极大地提升了软件开发的效率与质量。

目前，市场上涌现了多款优秀的 AI 编程辅助工具，它们虽然均能提升开发效率，但在实现路径和功能侧重上各有千秋：

- **GitHub Copilot**: 作为该领域最具影响力的产品之一，Copilot 由 GitHub 与 OpenAI 联合开发。它深度集成于 Visual Studio Code 等主流编辑器中，以其强大的代码自动补全能力而闻名。开发者在编写代码时，Copilot 能实时提供整行甚至整个函数块的建议。近年来，它也通过 Copilot Chat 扩展了对话式编程的能力，允许开发者在编辑器内通过聊天解决编程问题。
- **Claude Code**: Claude Code 是由 Anthropic 开发的 AI 编程助手，旨在通过自然语言指令帮助开发者在终端中高效地完成编码任务。它能够理解完整的代码库结构，执行代码编辑、测试和调试等操作，支持从描述功能到代码实现的全流程开发。Claude Code 还提供了无交互（headless）模式，适用于 CI、pre-commit hooks、构建脚本和其他自动化场景，为开发者提供了强大的命令行编程体验。
- **Trae**: 作为新兴的 AI 编程工具，Trae 专注于为开发者提供智能化的代码生成和优化服务。它通过深度学习技术分析代码模式，能够为开发者提供精准的代码建议和自动化重构方案。Trae 的特色在于其轻量级的设计和快速响应能力，特别适合需要频繁迭代和快速原型开发的场景。
- **Cursor**: 与上述主要作为插件或集成功能存在的工具不同，Cursor 则选择了一条更具整合性的路径，它本身就是一个 AI 原生的代码编辑器。它并非在现有编辑器上增加 AI 功能，而是在设计之初就将 AI 交互作为核心。除了具备顶级的代码生成和聊天能力外，它更强调让 AI 理解整个代码库的上下文，从而实现更深层次的问答、重构和调试。

### **1.4.2 作为自主协作者的智能体**：

在这种模式下，智能体能够独立地进行规划、推理、执行和反思，直到最终交付成果。

当前，实现这种自主协作的思路百花齐放，涌现了大量优秀的框架和产品，从早期的 BabyAGI、AutoGPT，到如今更为成熟的 CrewAI、AutoGen、MetaGPT、LangGraph 等优秀框架，共同推动着这一领域的高速发展。虽然具体实现千差万别，但它们的架构范式大致可以归纳为几个主流方向：

1. **单智能体自主循环**：这是早期的典型范式，如 AgentGPT 所代表的模式。其核心是一个通用智能体通过“思考-规划-执行-反思”的闭环，不断进行自我提示和迭代，以完成一个开放式的高层级目标。
2. **多智能体协作**：这是当前最主流的探索方向，旨在通过模拟人类团队的协作模式来解决复杂问题。它又可细分为不同模式： 
     - **角色扮演式对话**：如 **CAMEL** 框架，通过为两个智能体（例如，“程序员”和“产品经理”）设定明确的角色和沟通协议，让它们在一个结构化的对话中协同完成任务。 
     - **组织化工作流**：如 **MetaGPT** 和 **CrewAI**，它们模拟一个分工明确的“虚拟团队”（如软件公司或咨询小组）。每个智能体都有预设的职责和工作流程（SOP），通过层级化或顺序化的方式协作，产出高质量的复杂成果（如完整的代码库或研究报告）。**AutoGen** 和 **AgentScope** 则提供了更灵活的对话模式，允许开发者自定义智能体间的复杂交互网络。
3. 高级控制流架构：诸如 **LangGraph** 等框架，则更侧重于为智能体提供更强大的底层工程基础。它将智能体的执行过程建模为状态图（State Graph），从而能更灵活、更可靠地实现循环、分支、回溯以及人工介入等复杂流程。


### **1.4.3 Workflow 和 Agent 的差异**：

它们都旨在实现任务自动化，但其底层逻辑、核心特征和适用场景却截然不同。

简单来说，**Workflow 是让 AI 按部就班地执行指令**，而 **Agent 则是赋予 AI 自由度去自主达成目标**。

![图 1.6 Workflow 和 Agent 的差异.png](..%2Fimages%2F%E5%9B%BE%201.6%20Workflow%20%E5%92%8C%20Agent%20%E7%9A%84%E5%B7%AE%E5%BC%82.png)

- **Workflow**：工作流是一种传统的自动化范式，对一系列任务或步骤进行预先定义的、结构化的编排
- **Agent**：基于大型语言模型的智能体是一个具备自主性的、以目标为导向的系统

一个典型的例子，便是在 1.3 节中写的智能旅行助手。当我们向它下达一个新指令，例如：“你好，请帮我查询一下今天北京的天气，然后根据天气推荐一个合适的旅游景点。” 它的处理过程充分展现了其自主性

在这个过程中，没有任何写死的`if天气=晴天 then 推荐颐和园`的规则。如果天气是“雨天”，Agent 会自主推理并推荐国家博物馆、首都博物馆等室内场所。这种基于实时信息进行动态推理和决策的能力，正是 Agent 的核心价值所在。

## 1.5 **本章小结**

在本章中，我们共同踏上了探索智能体的初识之旅。我们的旅程从最基本的问题开始：

* **什么是大语言模型驱动的智能体？** 我们首先明确了其定义，理解了现代智能体是具备了能力的实体。它不再仅仅是执行预设程序的脚本，而是能够自主推理和使用工具的决策者。
* **智能体如何工作？** 我们深入探讨了智能体与环境交互的运行机制。我们了解到，这个持续的闭环是智能体处理信息、做出决策、影响环境并根据反馈调整自身行为的基础。
* **如何构建智能体？** 这是本章的实践核心。我们以“智能旅行助手”为例，亲手构建了一个完整的、由真实 LLM 驱动的智能体。
* **智能体有哪些主流的应用范式？** 最后，我们将视野投向了更广阔的应用领域。我们探讨了两种主流的智能体交互模式：一是以 GitHub Copilot 和 Cursor 等为代表的、增强人类工作流的“开发者工具”；二是以 CrewAI、MetaGPT 和 AgentScope 等框架为代表的、能够独立完成高层级目标的“自主协作者”。同时讲解了 Workflow 与 Agent 的差异。

