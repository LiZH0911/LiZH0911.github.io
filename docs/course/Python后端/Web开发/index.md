# Web 开发

相关链接：

- [小白学 Python - 学完之后做什么：Python 的三条出路](https://walter201230.github.io/Python/PythonBasis/python28/1/)
- [FastAPI 教程](https://fastapi.org.cn/tutorial/)

## 一、为什么选择 Web 后端开发

**Web 后端** 负责处理请求、读写数据库、调用外部服务并返回 JSON。前端框架换了一茬又一茬（Vue、React、Svelte），App 端从原生到混合再到跨平台，但后端始终在那里：处理请求、读数据库、调外部服务、返回 JSON。

Web 后端不仅不会消失，反而越来越重要，原因有三点：

1. **AI 应用本质上是 Web 应用**：ChatGPT、Claude、Cursor 等产品，前端是聊天框，后端是一堆调用模型的 API。LLM 工程师 90% 的工作其实是写后端，只是多了一个「调模型」的环节。
2. **微服务持续普及**：一个大型应用被拆成几十个小服务，每个服务都是一个 Web 后端项目。Python 在这种小服务场景下开发效率非常高。
3. **招聘面最广**：搜索「Python 后端」，全国一线城市每天新挂岗位都有几百个，比 AI 岗位多，门槛也相对更低。

**Python 相比 Java、Go、Node 的优势**：

- **开发速度快**：写一个 CRUD API，Python 大约 100 行，Java 同样的功能至少 300 行。
- **与 AI 生态无缝衔接**：后端要调用 LLM 处理用户输入，Python 一行 `import` 即可完成。
- **工程化能力已追上**：`fastapi + pydantic + sqlmodel + uv` 这套现代栈，类型安全、文档自动生成、性能也跟得上，与 Go 相比不再被吊打。

## 二、技术栈清单

### 2.1 **技术栈总览**

Web 后端开发的常用库按角色分类如下：

| 角色 | 代表库 | 用途 |
|------|--------|------|
| Web 框架 | fastapi、starlette | 编写 API，主流首选 |
| 数据校验 | pydantic | 请求体、响应体的类型校验，与 fastapi 深度集成 |
| ASGI 服务器 | uvicorn、hypercorn、granian | 运行 fastapi 的服务器，生产环境一般是 uvicorn + gunicorn |
| ORM | sqlalchemy 2.x、sqlmodel | 操作数据库，sqlmodel 是 fastapi 作者写的 SQLAlchemy 简化版 |
| 数据库迁移 | alembic | 表结构变更管理，如加字段、改类型 |
| 缓存 | redis-py、aiocache | Redis 客户端、内存缓存 |
| 异步任务 | celery、dramatiq、arq | 后台执行邮件、爬虫、定时任务 |
| 认证 | python-jose、passlib、authlib | JWT、密码哈希、OAuth |
| HTTP 客户端 | httpx | 调用外部 API，同步异步都支持 |
| 测试 | pytest、httpx.AsyncClient | 接口测试 |

### 2.2 **Web 框架：FastAPI 与 Django**

Web 后端有两大主流框架，代表两种不同的设计哲学：

- **FastAPI**：积木式框架。从空白开始，需要什么装什么，适合 API、微服务、AI 后端。
- **Django**：全家桶式框架。Admin 后台、ORM、模板、表单、用户系统全部自带，适合一个团队从零做一个完整网站（内容站、电商、企业内部系统）。

> 如果目标是 AI 后端、微服务、移动 App 后端，FastAPI 是默认答案；如果要做内容站、电商、企业内部系统，Django 依然是很好的选择。

### 2.3 **数据校验：Pydantic**

**Pydantic** 负责请求体与响应体的类型校验，与 FastAPI 深度集成。通过 Python 类型提示即可自动完成参数校验、数据序列化与接口文档生成。

### 2.4 **ASGI 服务器：Uvicorn**

**ASGI 服务器**用于运行 FastAPI 等异步 Web 框架。

- **uvicorn**：最常用的 ASGI 服务器，开发环境直接使用。
- **hypercorn、granian**：同为 ASGI 服务器，可按需选择。
- 生产环境通常是 `uvicorn` 配合 `gunicorn`（多进程管理）一起使用。

### 2.5 **ORM 与数据库迁移**

**ORM（对象关系映射）**负责把数据库表映射为 Python 对象，避免手写 SQL。

- **SQLAlchemy 2.x**：Python 最成熟、功能最全的 ORM。
- **SQLModel**：FastAPI 作者基于 SQLAlchemy 打造的简化版，与 FastAPI、Pydantic 无缝集成。

**Alembic** 负责数据库迁移，管理表结构变更（加字段、改类型等），保证开发、测试、生产环境的数据库结构一致。

### 2.6 **缓存与异步任务**

- **缓存**：`redis-py` 是 Redis 客户端，`aiocache` 提供内存缓存。用于热点数据加速、会话存储、限流等场景。
- **异步任务**：`celery`、`dramatiq`、`arq` 用于把「发邮件」「生成 PDF」「爬虫」等耗时任务丢到后台执行，避免阻塞 API 响应。

### 2.7 **认证与安全**

- **python-jose**：JWT（JSON Web Token）的编码与解码。
- **passlib**：密码哈希（如 bcrypt、argon2）。
- **authlib**：OAuth 2.0 / OpenID Connect 协议支持。

### 2.8 **HTTP 客户端与测试**

- **httpx**：调用外部 API，同步、异步都支持，可与 FastAPI 的 `AsyncClient` 配合做接口测试。
- **pytest**：Python 最主流的测试框架，配合 `httpx.AsyncClient` 编写接口测试。

## 三、学习路径

技术栈清单是「地图」，不是「清单」。真正学习时建议从下往上分四个阶段：

1. **第一阶段：单文件 API**。安装 fastapi 和 uvicorn，写一个 50 行的 `main.py` 跑起来，用浏览器打开 `http://localhost:8000/docs` 查看自动生成的 Swagger 文档。这一步 30 分钟搞定，立马上手。
2. **第二阶段：加数据库**。安装 sqlmodel，使用 SQLite 数据库（Python 自带，无需单独安装），写一组 CRUD 接口（增删改查）。这一步走通，已经能写一个像样的 API 了。
3. **第三阶段：加鉴权**。安装 python-jose 和 passlib，添加 `/login` 接口签发 JWT，再用 `Depends(get_current_user)` 保护其他接口。这一步完成，API 就是「企业级」的了。
4. **第四阶段：加异步任务**。安装 celery 或更轻量的 dramatiq，把「发邮件」「生成 PDF」等耗时任务丢到后台执行。这一步进入「真生产」级别。

## 四、三个起点项目

### 4.1 **项目 1：todo API（CRUD + SQLModel）**

- **做什么**：写一个待办事项 API，接口包括「创建 todo」「列出所有 todo」「按 ID 查 todo」「更新 todo」「删除 todo」。数据存 SQLite，用 SQLModel 做 ORM，用 pytest 写 5 个接口测试。
- **难度**：★☆☆☆☆
- **代码量**：200-300 行
- **学到啥**：fastapi 路由、pydantic 模型、SQLModel ORM 的 `select / session.add / session.commit`、pytest 写接口测试、Swagger 文档用法。

### 4.2 **项目 2：给 todo API 加 JWT 鉴权**

- **做什么**：在项目 1 基础上加用户系统——加一张 User 表、一个 `/register` 注册接口、一个 `/login` 登录接口（返回 JWT），其他接口全部加 `Depends(get_current_user)` 保护起来。每个 todo 关联一个 `user_id`，只能查/改自己的。
- **难度**：★★★☆☆
- **代码量**：300-500 行
- **学到啥**：JWT 工作原理、passlib 密码哈希、fastapi 的 `Depends` 依赖注入、关系型数据库的外键与 join、权限的「行级隔离」。

> JWT 是 Web 后端最常考的概念，这一关过了之后，看任何后端项目都不慌。

### 4.3 **项目 3：加后台任务（Celery 发邮件）**

- **做什么**：在项目 2 基础上加一个功能——用户注册之后给他发欢迎邮件，但不阻塞 API 响应。安装 celery + redis，把发邮件函数标成 `@app.task`，注册接口里 `send_welcome_email.delay(user.email)` 一发就返回，邮件由 worker 在后台真正发送。
- **难度**：★★★★☆
- **代码量**：400-600 行
- **学到啥**：消息队列的概念、celery 的 broker 和 backend、redis 如何当队列、worker 如何部署、为什么需要异步任务（用户体验、稳定性）。

> 这个项目做完，已经摸到了「分布式后端」的门。后面学 Kafka、学微服务，都是基于这个思路扩展出去的。
