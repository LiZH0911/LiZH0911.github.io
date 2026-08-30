# **六、框架开发实践**

**本章目标**：探讨如何利用业界主流的一些智能体框架，来高效、规范地构建可靠的智能体应用

**框架的本质**：将所有智能体共有的、重复性的工作（如主循环、状态管理、工具调用、日志记录等）进行抽象和封装

## 6.1 **从手动实现到框架开发**

### **6.1.1 为何需要智能体框架**

- 提升代码复用与开发效率：这是最直接的价值。一个好的框架会提供一个通用的 Agent 基类或执行器，它封装了智能体运行的核心循环（Agent Loop）。无论是 ReAct 还是 Plan-and-Solve，都可以基于框架提供的标准组件快速搭建，从而避免重复劳动。
- 实现核心组件的解耦与可扩展性：
     - 模型层 (Model Layer)：负责与大语言模型交互，可以轻松替换不同的模型（OpenAI, Anthropic, 本地模型）
     - 工具层 (Tool Layer)：提供标准化的工具定义、注册和执行接口，添加新工具不会影响其他代码
     - 记忆层 (Memory Layer)：处理短期和长期记忆，可以根据需求切换不同的记忆策略（如滑动窗口、摘要记忆）
- 标准化复杂的状态管理：我们在`ReflectionAgent`中实现的`Memory`类只是一个简单的开始。在真实的、长时运行的智能体应用中，状态管理是一个巨大的挑战，它需要处理上下文窗口限制、历史信息持久化、多轮对话状态跟踪等问题。一个框架可以提供一套强大而通用的状态管理机制，开发者无需每次都重新处理这些复杂问题。
- 简化可观测性与调试过程：当智能体的行为变得复杂时，理解其决策过程变得至关重要。一个精心设计的框架可以内置强大的可观测性能力。例如，通过引入事件回调机制（Callbacks），我们可以在智能体生命周期的关键节点（如 `on_llm_start`, `on_tool_end`, `on_agent_finish`）自动触发日志记录或数据上报，从而轻松地追踪和调试智能体的完整运行轨迹。这远比在代码中手动添加`print`语句要高效和系统化。

### **6.1.2 主流框架的选型与对比**

![四种智能体框架对比.png](..%2Fimages%2F%E5%9B%9B%E7%A7%8D%E6%99%BA%E8%83%BD%E4%BD%93%E6%A1%86%E6%9E%B6%E5%AF%B9%E6%AF%94.png)

## 6.2 **框架一：微软 AutoGen**——企业级多智能体协作框架

### **6.2.1 AutoGen 的核心机制**

![AutoGen架构图.png](..%2Fimages%2FAutoGen%E6%9E%B6%E6%9E%84%E5%9B%BE.png)

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

### **6.2.2 软件开发团队**

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

### **6.2.3 核心代码实现**

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

![AutoGen 运行记录.png](..%2Fimages%2FAutoGen%20%E8%BF%90%E8%A1%8C%E8%AE%B0%E5%BD%95.png)


整个协作过程展现了 AutoGen 框架的优势：<strong>自然的对话驱动协作</strong>、<strong>角色专业化分工</strong>、<strong>流程自动化管理</strong>和<strong>完整的开发闭环</strong>。

### **6.2.4 AutoGen 的优势与局限性分析**

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


## 6.3 **框架二：阿里 AgentScope**——消息驱动架构

### **6.3.1 AgentScope 的设计**

（1）分层架构体系

![AgentScope架构图.png](..%2Fimages%2FAgentScope%E6%9E%B6%E6%9E%84%E5%9B%BE.png)

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


### **6.3.2 三国狼人杀游戏**


## 6.4 **框架三：KAUST CAMEL**——双智能体协作框架

与 AutoGen 和 AgentScope 这样功能全面的框架不同，CAMEL最初的核心目标是探索如何在最少的人类干预下，让两个智能体通过“角色扮演”自主协作解决复杂任务。

[camel 仓库](https://github.com/camel-ai/camel)

### **6.4.1 CAMEL 的自主协作**

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

### **6.4.2 案例-AI科普电子书**

略

### **6.4.3 CAMEL 的优势与局限性分析**

（1）优势

- 轻架构、重提示

（2）主要局限性

- 对提示工程的高度依赖
- 协作规模的限制
- 任务适用性的边界


## 6.5 **框架四：LangChain AI LangGraph**——状态化多智能体编排框架

### **6.5.1 LangGraph 的结构梳理**

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

### **6.5.2 案例-三步问答助手**

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


### **6.5.3 LangGraph 的优势与局限性分析**

（1）优势

- LangGraph 将一个完整的实时问答流程，显式地定义为一个由状态、节点和边构成的“流程图”。这种设计的最大优势是高度的可控性与可预测性。
- 此外，由于每个节点都是一个独立的 Python 函数，这带来了高度的模块化。
- 同时，在流程中插入一个等待人类审核的节点也变得非常直接，为实现可靠的“人机协作”（Human-in-the-loop）提供了坚实的基础。

（2）局限性

- 与基于对话的框架相比，LangGraph 需要开发者编写更多的前期代码（Boilerplate）。定义状态、节点、边等一系列操作，使得对于简单任务而言，开发过程显得更为繁琐。开发者需要更多地思考“如何控制流程（how）”，而不仅仅是“做什么（what）”。由于工作流是预先定义的，LangGraph 的行为虽然可控，但也缺少了对话式智能体那种动态的、“涌现”式的交互。它的强项在于执行一个确定的、可靠的流程，而非模拟开放式的、不可预测的社会性协作。

- 调试过程同样存在挑战。虽然流程比对话历史更清晰，但问题可能出在多个环节：某个节点内部的逻辑错误、在节点间传递的状态数据发生异变，或是边跳转的条件判断失误。这要求开发者对整个图的运行机制有全局性的理解。