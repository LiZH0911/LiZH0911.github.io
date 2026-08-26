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

![ReAct工作原理](..%2Fimages/ReAct工作原理.png)

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

![Plan-and-Solve工作原理](..%2Fimages/Plan-and-Solve工作原理.png)

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

![Reflection工作原理](..%2Fimages/Reflection工作原理.png)


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