# PythonWeb

该笔记参考的课程链接：

- [黑马程序员Python+AI](https://www.bilibili.com/video/BV1sHU9BmEne?spm_id_from=333.788.videopod.episodes&vd_source=46f99c7c1ed609a31f70615a4551767f&p=173)
- [黑马程序员PythonWeb开发](https://www.bilibili.com/video/BV1zV2QBtE39/?spm_id_from=333.1007.top_right_bar_window_custom_collection.content.click&vd_source=46f99c7c1ed609a31f70615a4551767f)

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

- [FastAPI官方网站](https://fastapi.org.cn/)
- [FastAPI用户指南](https://fastapi.org.cn/tutorial/)

**API 接口**：应用程序编程接口（Application Programming Interface），即对外提供的功能入口，供别人来调用。

### 2.2 **入门**

**FastAPI 使用步骤**：

```
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

**Restful**：Restful 指的是遵循 REST 架构风格的 API 接口服务

**REST**：REST（REpresentational State Transfer，表述性状态转换）是一种软件架构风格

**传统风格 URL（RPC 风格）**：远程过程调用风格

```
GET /user/getInfo?userId=123
POST /product/delete?productId=456
```

- URL 的设计核心是“执行某个操作”
- 用问号传参，动词写在路径里
- 优点：简单直观，浏览器地址栏直接输入就能访问，调试方便，对新手友好
- 缺点：接口数量会爆炸（增删改查各需要一个不同路径）；无法充分利用 HTTP 协议语义；时间久了 URL 混乱，难以维护


**REST 风格 URL**：用 HTTP 方法做动词，路径只表示资源