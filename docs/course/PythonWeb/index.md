# PythonWeb

该笔记参考的课程链接：

- [黑马程序员 Python+AI](https://www.bilibili.com/video/BV1sHU9BmEne?spm_id_from=333.788.videopod.episodes&vd_source=46f99c7c1ed609a31f70615a4551767f&p=173)

[//]: # (- [黑马程序员 PythonWeb 开发]&#40;https://www.bilibili.com/video/BV1zV2QBtE39/?spm_id_from=333.1007.top_right_bar_window_custom_collection.content.click&vd_source=46f99c7c1ed609a31f70615a4551767f&#41;)

## 一、Web 初识

**Web**：Web网站由三个核心部分组成：前端程序、服务端程序、数据库。

- 前端程序：负责界面展示
- 服务端程序：负责业务逻辑处理
- 数据库：负责数据存储和管理

**前端程序**：由HTML、CSS、JavaScript组成

- HTML：超文本标记语言，用于创建网页结构
- CSS：层叠样式表，用于创建网页样式
- JavaScript：脚本语言，用于创建网页交互

**服务端程序**：可以基于各种语言和框架实现，例如 Python 的 Django、Flask、FastAPI，Java 的 Spring，PHP 的 Laravel 等。

## 二、FastAPI 基础

### 2.1 **概述**

**FastAPI 介绍**：FastAPI 是一个用于构建 API 的现代、快速（高性能）的 Web 框架，基于标准 Python 类型提示。

- [FastAPI 网站](https://fastapi.org.cn/)
- [FastAPI 教程](https://fastapi.org.cn/tutorial/)

**API 接口**：应用程序编程接口（Application Programming Interface），即对外提供的功能入口，供别人来调用。

### 2.2 **入门**

**FastAPI 使用步骤**：

```Python
# 1. 导入 FastAPI
from fastapi import FastAPI

# 2. 创建 FastAPI 实例对象
app = FastAPI()

# 3. 创建路径操作函数，定义访问路径
@app.get("/") # 接口的访问路径为 /，请求方式为GET
def read_root():
    return {"message": "Hello World"}
    
# 4. 运行 FastAPI 服务
# 方式1 命令行
>>fastapi dev "xxxx.py" 
# 方式2 命令行
>>uvicorn xxxx:app --reload 
# 方式3 .py文件运行（推荐方式）
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=8000)  # host="127.0.0.1" 表示只允许本机访问
```

## 三、AI汉字迷盒案例

### 2.1 **开发规范**

**Restful**：Restful 指的是遵循 REST 架构风格的 API 接口服务。REST（REpresentational State Transfer，表述性状态转换）是一种软件架构风格。

**传统风格 URL（RPC 远程过程调用风格）**：

```
GET https://localhost:8000/users/getInfo?userId=123
```

- <span style="color: red;">动词写在路径里，用问号传参</span>
- 优点：简单直观，浏览器地址栏直接输入就能访问，调试方便，对新手友好
- 缺点：接口数量会爆炸（增删改查各需要一个不同路径）；无法充分利用 HTTP 协议语义；时间久了 URL 混乱，难以维护


**REST 风格 URL**：

```
GET https://localhost:8000/users/1 查询用户
DELETE https://localhost:8000/users/1 删除用户
POST https://localhost:8000/users 新增用户
PUT https://localhost:8000/users 修改用户
```

- <span style="color: red;">URL 定位资源，HTTP 动词描述操作</span>
- 简洁、规范、优雅

>注意：REST是风格，是约定方式，约定不是规定，可以打破。

>描述功能模块通常使用复数形式（加s），表示此类资源，而非单个资源。如：users、books、items

### 2.2 **基础环境搭建**

**步骤**：

1. 创建项目文件夹，创建静态文件目录 static，用于存放 HTML、CSS、JS等静态资源
2. 编写 Python 文件，挂载静态文件目录，定义路径操作函数，访问前端 HTML 页面

```Python
from fastapi import FastAPI
from starlette.responses import FileResponse
from fastapi.staticfiles import StaticFiles

# 创建FastAPI实例
app = FastAPI(title="汉字谜盒")

# 挂载静态文件的存放目录，当用户访问 /static 路径时，去 static 文件夹里找文件
app.mount("/static", StaticFiles(directory="static"), name="static")

# 定义路径操作函数 ---> http://localhost:8000/
@app.get("/")
def root():
    return FileResponse("static/index.html")

# 启动项目
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000) # access_log=False : 关闭访问日志
```

**挂载静态文件**：在特定路径中添加一个完整的“独立”应用程序，然后它将负责处理所有子路径。主应用程序的 OpenAPI 和文档不会包含挂载应用程序中的任何内容。

- [FastAPI 教程-静态文件](https://fastapi.org.cn/tutorial/static-files/)


### 2.3 **核心功能开发**

**功能1——新建会话**：在打开该项目页面时，如果之前没有会话，则要自动创建一个会话。

```Python
import os
import json
# 创建会话存放的目录 sessions
if not os.path.exists("sessions"):
    os.mkdir("sessions")

# 新建会话
@app.post("/api/sessions")
def create_session() -> ApiResponse:

    # 1. 生成会话的标识（名字）
    session_id = generate_session_id()

    # 2. 组装会话信息, 保存到文件
    session_data = {
        "current_session": session_id,
        "messages": []
    }
    # with open(os.path.join("sessions", session_id + ".json"), "w") as f:
    with open(f"sessions/{session_id}.json", "w", encoding="utf-8") as f:
        json.dump(session_data, f, ensure_ascii=False, indent=2) # ensure_ascii=False 表示中文不转义，indent=2 表示缩进为2个空格

    # 3. 返回数据
    # return {"code": 200, "message": "创建会话成功", "data": session_id} # 直接返回字典容易把 key 写错，建议使用响应模型返回封装好的对象
    return ApiResponse(code=200, message="创建会话成功", data=session_id)

```

**响应模型**：

```Python
from pydantic import BaseModel
from typing import Any
# 定义响应数据模型
class ApiResponse(BaseModel):
    code: int
    message: str
    data: Any  # 任意类型的数据
```

>BaseModel：是Pydantic库提供的父类（FastAPI 深度集成了 Pydantic），用于定义 FastAPI 数据模型和数据验证规则。

>返回的数据会被 Pydantic 自动序列化为 JSON 格式。Pydantic 是用 Rust 编写的，因此速度会快得多。

- [FastAPI 教程-响应模型-返回类型](https://fastapi.org.cn/tutorial/response-model/)

**功能2——与AI交互**：在会话页面，用户输入问题或者答案后，请求服务端与AI进行交互。

**获取请求数据**：在与AI进行交互时，前端通过 POST 请求体向服务端传递 json 格式数据（本案例中为 session_id 与 message）。

```Python
# 定义请求数据模型
class ChatRequest(BaseModel):
    session_id: str
    message: str
# 与AI交互（获取请求数据）
@app.post("/api/chat")
def chat(request: ChatRequest) -> ApiResponse:
    print(f"与AI交互的请求数据: {request.session_id} : {request.message}")
    return ApiResponse(code=200, message="请求成功", data="AI大模型返回的数据")
```

**与AI交互的逻辑**：

1. 加载 json 文件会话数据
2. 构建消息列表
3. 调用 Deepseek
4. 获取响应数据
5. 更新新的消息列表
6. 保存消息到 json 文件
7. 响应数据

```Python
from openai import OpenAI
# 创建与AI大模型交互的客户端对象（提前在环境变量中新建DEEPSEEK_API_KEY，其值设置为DeepSeek的API_KEY）
client = OpenAI(api_key=os.environ.get('DEEPSEEK_API_KEY'), base_url="https://api.deepseek.com")

# 根据session_id获取文件名
def get_session_file_name(session_id):
    return f"sessions/{session_id}.json"

# 与AI交互（逻辑实现）
@app.post("/api/chat")
def chat(request: ChatRequest) -> ApiResponse:

    # 1. 加载json文件中的会话数据
    session_path = get_session_file_name(request.session_id)
    with open(session_path, "r", encoding="utf-8") as f:
        session_data = json.load(f)

    # 2. 构建消息列表 messages（列表的每个元素为字典）
    messages = [{"role": "system", "content": SYSTEM_PROMPT}] # 系统提示词
    for message in session_data["messages"]: # 历史消息内容
        messages.append(message)
    messages.append({"role": "user", "content": request.message}) # 本次消息内容

    # 3. 调用AI大模型 DeepSeek
    response = client.chat.completions.create(
        model="deepseek-v4-flash", # 调用的模型
        messages=messages,
        stream=False, # 非流式响应
        temperature=1.0 # 温度参数，即模型生成结果的随机性、多样性默认1.0，范围 [0.0, 2.0]，不同使用场景可调；思考模式下不生效
    )

    # 4. 获取响应的数据
    ai_response = response.choices[0].message.content

    # 5. 更新新的消息列表，移除系统提示词并添加AI的响应
    messages.pop(0)
    messages.append({"role": "assistant", "content": ai_response})
    session_data["messages"] = messages

    # 6. 保存会话信息到json文件中
    with open(session_path, "w", encoding="utf-8") as f:
        json.dump(session_data, f, ensure_ascii=False, indent=2)

    # 7. 返回数据
    return ApiResponse(code=200, message="请求成功", data=ai_response)
```

**功能3——会话列表**：将所有会话名称展示在页面的左侧侧边栏，并且根据时间倒序排序。

```Python
# 获取会话列表
@app.get("/api/sessions")
def get_sessions() -> ApiResponse:
    
    # 1. 获取 sessions 目录下的所有文件名
    session_files = os.listdir("sessions")

    # 2. 获取文件名中的会话ID
    session_ids = [file.split(".")[0] for file in session_files]
    session_ids.sort(reverse=True) # 排序, 降序排列

    # 3. 返回数据
    return ApiResponse(code=200, message="获取会话列表成功", data=session_ids)
```

**功能4——加载指定会话**：在点击左侧的会话名称之后，就要查询出该会话对应的会话信息，并在消息展示栏将其展示出来。

```Python
# 获取指定的会话信息 ---> /api/sessions/2026-04-21_22-20-30 , /api/sessions/2026-04-21_22-20-55 [路径参数]
@app.get("/api/sessions/{session_id}")
def get_session(session_id: str) -> ApiResponse:

    # 1. 获取会话文件名
    session_file = get_session_file_name(session_id)

    # 2. 读取会话文件
    with open(session_file, "r", encoding="utf-8") as f:
        session_data = json.load(f)

    # 3. 返回数据
    return ApiResponse(code=200, message="获取会话信息成功", data=session_data)
```

**功能5——删除会话**：在点击左侧会话名称之后的x，就要将当前的会话信息直接删除掉。

```Python
# 删除指定的会话
@app.delete("/api/sessions/{session_id}")
def delete_session(session_id: str) -> ApiResponse:

    # 1. 获取会话文件名
    session_file = get_session_file_name(session_id)

    # 2. 删除会话文件
    if os.path.exists(session_file):
        os.remove(session_file)

    # 3. 返回数据
    return ApiResponse(code=200, message="删除会话成功", data=None)
```

### 2.4 **程序优化**

**日志记录**：为了能够灵活的控制项目中日志的输出，我们可以通过官方提供的 logging 模块来输出日志。

```Python
import logging
# 配置日志的基本信息
# %(asctime)s :  时间 ;  %(levelname)s: 日志级别； %(filename)s: 文件名; %(lineno)d: 行数;  %(message)s: 日志信息
logging.basicConfig(
    level=logging.ERROR,  # 日志级别，level=logging.ERROR表示现在只想看ERROR及以上级别的日志
    format="%(asctime)s - %(levelname)s - %(filename)s:%(lineno)d - %(message)s" # 日志格式
)
# 定义路径操作函数
@app.get("/")
def root():
    logging.info("访问项目首页")
    return FileResponse("static/index.html")
```

>日志级别：给日志信息贴上的"重要性标签"，常见的级别有:DEBUG、INFO、WARNING、ERROR、FATAL（日志级别依次升高）

**异常处理**：

```Python
# 定义异常处理器, 捕获所有异常 ---> 返回的对象的类型得是 Response
@app.exception_handler(Exception)
def handle_exception(request: Request, exc: Exception):
    logging.error(f"处理异常, 请求路径: {request.url},  捕获到异常: {exc}")
    return JSONResponse(content={"code": 500, "message": "服务器内部错误, 请联系管理员~", "data": None})
```

- [FastAPI 教程-异常处理](https://fastapi.org.cn/tutorial/handling-errors/)