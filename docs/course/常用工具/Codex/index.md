# Codex

该笔记参考的课程链接：

- [ChatGPT 橙皮书](https://bozhoudev.github.io/codex-orange-book/)
- [CC Switch + Codex 使用方案](https://www.bilibili.com/video/BV112ge6KEmX/?spm_id_from=333.337.search-card.all.click&vd_source=46f99c7c1ed609a31f70615a4551767f)


## 一、Codex 基础认知

**AI 编程工具的四次进化**

![AI 编程工具的四次进化.png](image%2FAI%20%E7%BC%96%E7%A8%8B%E5%B7%A5%E5%85%B7%E7%9A%84%E5%9B%9B%E6%AC%A1%E8%BF%9B%E5%8C%96.png)

**Codex 能做什么**

![Codex 能做什么.png](image%2FCodex%20%E8%83%BD%E5%81%9A%E4%BB%80%E4%B9%88.png)

Codex 是一个可以进入项目现场的 AI 工程助手。

**Codex 与 ChatGPT 的区别**

![Codex 与 ChatGPT 的区别.png](image%2FCodex%20%E4%B8%8E%20ChatGPT%20%E7%9A%84%E5%8C%BA%E5%88%AB.png)

**Codex 与 Cursor 的区别**

![Codex 与 Cursor 的区别.png](image%2FCodex%20%E4%B8%8E%20Cursor%20%E7%9A%84%E5%8C%BA%E5%88%AB.png)

**Codex 与 Claude Code 的区别**

![Codex 与 Claude Code 的区别.png](image%2FCodex%20%E4%B8%8E%20Claude%20Code%20%E7%9A%84%E5%8C%BA%E5%88%AB.png)

**Codex 的使用入口**

![Codex 的使用入口.png](image%2FCodex%20%E7%9A%84%E4%BD%BF%E7%94%A8%E5%85%A5%E5%8F%A3.png)


## 二、Codex 安装

### 2.1 **安装前准备**

**账号准备**

**系统准备**

**软件工具准备**

**项目目录准备**

**权限与安全准备**

### 2.2 **Codex App**

### 2.3 **Codex CLI**

Codex CLI 是 Codex 的命令行版本。

它适合愿意打开终端的人使用，比如 PowerShell、Terminal、iTerm、Windows Terminal。

**Windows 安装**

```bash
# 1. 安装 Node.js
node -v
npm -v

2. 安装 Codex CLI
npm install -g @openai/codex
codex --version
```

**第一次运行**

```bash
# 打开 powershell，进入项目目录
cd 项目目录
# 运行 codex
codex

# 登陆方式
# 1. 使用 ChatGPT 账号登录
# 2. 使用 OpenAI API Key 登录
```


**CLI 基础命令**

- 终端命令：
    - PowerShell / Terminal 里输入
    - 启动、登录、更新、诊断、管理 Codex
- 斜杠命令
    - 进入 Codex 后输入
    - 切模型、调权限、看 diff、生成规则、退出会话

**CLI 常用终端命令**

```bash
# 启动 Codex
codex
# 只读模式
codex --sandbox read-only
# 当前项目可读写，敏感操作先请求批准
codex --sandbox workspace-write --ask-for-approval on-request

# 接着上次任务继续
codex resume --last
# 归档不用的任务
codex archive
# 一次性执行任务
codex exec "任务"

# 登录 Codex（默认打开浏览器，用 ChatGPT 账号登录）
codex login
# Windows PowerShell 使用 API Key 登录
$env:OPENAI_API_KEY | codex login --with-api-key

# 查看登录状态
codex login status

# 退出登录
codex logout

# 检查环境问题
codex doctor

# 更新 Codex
codex update

# 打开 Codex App
codex app

# 把 Codex Cloud 生成的 diff 应用到本地，用 Cloud 后再学
codex apply
```

**CLI 常用斜杠命令**

<details>
<summary>点击展开命令手册</summary>

模型、权限与安全

```bash
# 切换模型和推理强度
/model

# 查看当前状态，看模型、权限、上下文、token 等信息
/status

# 调整权限
/permissions

# 进入计划模式
/plan
```

代码检查与 Review 相关

```bash
# 查看代码改动（修改后必看）
/diff

# 让 Codex review 当前改动（提交前检查）
/review
```

会话管理相关

```bash
# 开始新对话（当前任务结束，想开始新任务）
/new

# 清空终端并开始新聊天（界面太乱、想重新开始）
/clear

# 恢复之前的会话（上次任务没做完）
/resume

# 归档当前会话并退出（任务完成或方案不要了）
/archive

# 复制当前会话成新 thread（保留原思路，另开分支尝试）
/fork

# 开一个临时侧边对话（不影响主任务地问个小问题）
/side

# 退出 Codex CLI
/quit
/exit
```

上下文与长对话相关

```bash
# 压缩当前对话（把长对话总结成重点）
/compact

# 查看上下文使用情况
/status

# 指定 Codex 重点看某个文件
/mention
```

项目规则与能力相关

```bash
# 创建项目规则文件
/init

# 浏览和使用 Skills（选择专项技能，用于做 UI、写文档、review 等专项任务时）
/skills

# 配置记忆（想管理长期偏好时）
/memories

# 设置任务目标
/goal

# 浏览可连接的应用
/apps

# 管理插件
/plugins

# 查看 MCP 工具
/mcp
```

终端和后台任务相关

```bash
# 查看后台终端任务
/ps

# 停止后台终端任务
/stop
```

</details>

新手推荐使用流程

```bash
# 0. 启动 Codex
cd 项目目录
Codex
# 1. 生成项目规则
/init
# 2. 确认权限不要太大
/permissions
# 3. 确认模型和推理强度
/model
# 4. 复杂任务先规划
/plan
# 5. 让 Codex 开始工作
输入任务
# 6. 检查代码改动
/diff
# 7. 让 Codex 再检查一遍
/review
# 8. 查看当前状态和上下文
/status
# 9. 对话太长时压缩
/compact
# 10. 退出 Codex
/quit
# 11. 满意后保存版本
git commit -m "完成任务"
```

**任务写法模板**

```bash
请帮我完成【具体任务】。

要求：
1.
2.
3.

限制：
1. 不要修改无关文件
2. 不要删除已有功能
3. 完成后告诉我改了哪些文件
```



### 2.4 **Codex IDE Extension**

把 Codex 直接装进代码编辑器里，不用单独打开 Codex App，也不用一直切到终端，而是可以在 VS Code、Cursor、Windsurf 这类编辑器侧边栏里直接使用 Codex。


**Codex IDE支持**

- VS Code
- Cursor
- JetBrains IDE
- ...

### 2.5 **Codex Web**

在网页里使用的云端 Codex。不需要一直开着本地电脑，也不一定要在终端里操作，而是可以连接 GitHub 仓库，让 Codex 在云端环境里读取代码、执行任务、修改文件，并生成可 review 的结果

与入门

## 三、核心功能详解

### 3.1 **自动化**

Codex 自动化 = 让 Codex 不只是“听你指挥”，而是能按规则定期帮你巡查项目、发现问题、处理问题。

示例提示词可以这样写：

```
请检索并复盘最近一周的 Codex 会话记录与执行日志，维护一份“Codex 会话复盘与个人风格档案”。

要求：
1. 优先使用可用的会话历史检索能力；如果需要读取日志，只做搜索、元数据提取和相关片段抽取，不要整文件载入大型 session 文件。
2. 不要复现原始日志、隐私内容、密钥、内部 reasoning 或长对话原文。
3. 总结执行经验：哪些做法导致了问题，最终正确做法是什么，适合什么场景复用。
4. 总结我的偏好：UI 设计偏好、产品理念、交互原则、内容系统偏好和工作流偏好。
5. 整理可复用规则清单：把复盘结论改写成后续 Codex 会话可以遵循的简洁规则。
6. 更新文档时去重、合并相近规则，保留日期范围或任务类型作为来源线索。
7. 如有适合长期复用的规则，请建议是否加入项目级或用户级 AGENTS.md。
```

### 3.2 **插件**

Codex 本身已经能读代码、改代码、运行命令；插件是在这个基础上，让它连接更多工具、使用固定流程，或者获得某些专项能力。

### 3.3 **Skill**

给 Codex 准备的一套「固定工作方法」。

Codex 本身会读代码、改代码、运行命令。

但如果你经常让它做同一类任务，比如写 README、做代码 Review、生成网页、整理文档，就可以把这套流程做成 Skill。

**Skill 和普通提示词的区别**

- 只做一次的任务 = 直接写提示词
- 经常重复做的任务 = 适合做 Skill

**Skill 通常包含什么**

- instructions：告诉 Codex 怎么做
- resources：放参考资料、模板、标准
- scripts：可选脚本，用来自动处理任务
- examples：示例输入和示例输出
- checklist：检查清单，防止漏步骤

**Skill 的基本结构**

```
# Skill 名称

## 适用场景
这个 Skill 适合用来做什么。

## 工作目标
Codex 最终要交付什么结果。

## 工作流程
1. 先分析输入内容
2. 再确认任务类型
3. 然后按固定步骤处理
4. 最后输出结果和检查清单

## 输出格式
规定 Codex 最后应该怎么输出。

## 注意事项
哪些事情不能做，哪些风险要提醒。
```

比如 README Skill：

```
# README 生成 Skill

## 适用场景
用于根据当前项目生成 README 文档。

## 工作目标
输出一份结构清晰、适合新手阅读的 README。

## 工作流程
1. 阅读项目结构
2. 查看 package.json 或主要入口文件
3. 判断项目类型
4. 生成项目介绍
5. 补充安装步骤和启动命令
6. 说明文件结构
7. 输出常见问题

## 输出格式
使用 Markdown 格式。

## 注意事项
不要编造不存在的功能。
不确定的地方要明确标注。
```

**Codex App 里怎么添加 Skill** 点击插件-技能

1. 使用已有 Skill
2. 创建自己的 Skill

**创建自己的 Skill** 

1. 打开 Codex App
2. 选择一个项目
3. 新建一个 thread
4. 输入 `$skill-creator`
5. 告诉它你想创建什么 Skill
6. 提供使用场景、规则、示例输出
7. 让 Codex 生成 Skill 文件
8. 检查生成结果
9. 之后在新 thread 里使用这个 Skill

示例提示词：

```
请帮我创建一个 README Skill。

这个 Skill 的作用：
根据当前项目自动生成适合小白阅读的 README。

触发场景：
当我说“生成 README”“写项目说明”“整理项目文档”时使用。

工作流程：
1. 先阅读项目结构
2. 查看 package.json、README、入口文件
3. 判断项目类型
4. 生成项目简介
5. 写安装步骤
6. 写启动命令
7. 说明主要文件夹作用
8. 补充常见问题
9. 不确定的地方不要编造

输出格式：
使用 Markdown。

必须包含：
- 项目简介
- 功能特点
- 安装步骤
- 启动命令
- 文件结构
- 常见问题
- 后续优化方向
```

**推荐安装的 Skill**

- [superpowers](https://github.com/obra/superpowers)：给 Coding Agent 加一整套“软件开发方法论”
- skill-creator：创建 Skill 的辅助 Skill
- [baoyu-skills](https://github.com/JimLiu/baoyu-skills)：偏内容创作和日常效率
- [Agent-Reach](https://github.com/Panniantong/Agent-Reach)：给 Agent 装“联网能力”
- [find-skills](https://github.com/vercel-labs/skills/tree/main/skills/find-skills)：“找 Skill 的 Skill”。

**在 Codex CLI 里添加 Skill 的三种方式**

1. 使用已有 Skill
2. $skill-creator 创建（最推荐）
3. 手动创建 SKILL.md

### 3.4 **MCP**

**只有进阶AI编程才需要了解，普通人可以直接跳过**

让 Codex 连接外部工具的接口。

Codex 本身可以读代码、改代码、运行命令。

MCP 的作用是让 Codex 连接更多外部工具、数据源或服务。

**MCP 概念**

* MCP：连接外部工具的标准接口
* MCP Server：提供工具能力的服务
* Tool：Codex 可以调用的具体功能
* Config：MCP 的配置文件
* STDIO Server：通过本地命令启动的 MCP 服务
* HTTP Server：通过网址连接的 MCP 服务
* Context：外部工具提供给 Codex 的上下文信息

**在 Codex App 里怎么使用 MCP**

1. 打开 Codex App
2. 进入 Settings
3. 找到 MCP servers
4. 查看 recommended servers
5. 添加 custom server
6. 按提示完成授权
7. 回到项目 thread
8. 查看结果和权限请求

**添加 MCP 时通常需要填什么**

- Name：MCP 名称：MCP 名称
- Command / URL：启动命令或服务地址
- Type：MCP 类型
- Env：环境变量
- Auth：授权方式
- Enabled tools：启用哪些工具

![Codex MCP.png](image%2FCodex%20MCP.png)

**添加 MCP 后怎么使用**

添加完成后，回到 Codex App 的 thread，直接描述任务即可。

示例提示词：

```
请使用可用的 MCP 文档工具，
查询 Next.js App Router 的最新用法，
然后告诉我当前项目应该怎么修改。
```

或者：

```
请用 Figma MCP 读取这个设计稿，
分析页面结构，并给我生成前端实现计划。
```

**在 Codex CLI 里怎么使用 MCP**

1. 打开终端
2. 进入项目目录
3. 添加 MCP server
4. 检查 MCP 是否添加成功
5. 启动 Codex CLI
6. 用 /mcp 查看工具
7. 在任务里调用 MCP
8. 查看结果和权限提示

**常用 MCP 终端命令**

```bash
codex mcp --help

codex mcp list

codex mcp add 名称 -- 启动命令
# 给 Codex 添加一个叫 context7 的 MCP。它通过 npx 启动 @upstash/context7-mcp 这个工具。
codex mcp add context7 -- npx -y @upstash/context7-mcp

codex mcp remove 名称

# 查看某个 MCP server 详情
codex mcp get

# 登录需要授权的 MCP
codex mcp login

codex mcp logout

/mcp
```

**MCP 配置文件在哪里**

~/.codex/config.toml 里面可能会有类似这样的配置：

```toml
[mcp_servers.context7]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
```

这表示：有一个 MCP server 叫 context7。 

启动命令是 npx -y @upstash/context7-mcp。

**添加远程 MCP**

有些 MCP 不是本地命令启动，而是通过网址连接。

这类一般叫远程 MCP / HTTP MCP。

可能会需要：

- URL：远程 MCP 服务地址
- Auth：是否需要登录
- Token：访问凭证
- OAuth：浏览器授权登录

### 3.5 **Work**

很多人第一次看到 ChatGPT Work，会产生一个很自然的疑问：

它的界面和 Codex 很像，也可以读文件、查资料、使用工具、生成内容。

那为什么还要单独做一个 Work？

如果只看底层能力，两者确实有很多相似之处。它们都不是只能回答问题的聊天工具，而是可以围绕目标持续执行任务的 Agent。

但两者面对的工作环境不同，默认使用的工具不同，最终交付的结果也不同。

一句话总结：

**Work 擅长把资料变成可以直接使用的成果，Codex 擅长把需求变成经过验证的代码改动。**

**Work 和 Codex 的核心区别**

![Work 和 Codex 的核心区别.png](image%2FWork%20%E5%92%8C%20Codex%20%E7%9A%84%E6%A0%B8%E5%BF%83%E5%8C%BA%E5%88%AB.png)

**同一个任务应该选择 Work 还是 Codex？**

最简单的判断方法是看最终要交付什么。

- 最终需要文档、表格或 PPT：优先选择 Work。
- 最终需要修改代码或验证项目：优先选择 Codex。
- 既要研究，又要开发：可以把任务拆成两个阶段，第一阶段使用 Work，第二阶段使用 Codex

### 3.6 **站点（sites）**

**让 Codex 把网站直接发布成可访问链接**

Codex 负责把网站做出来，Sites 负责把网站变成可以访问和分享的线上成果。

**Sites 和 Vercel、Netlify 有什么区别？**

![Sites 和 Vercel、Netlify 有什么区别？.png](image%2FSites%20%E5%92%8C%20Vercel%E3%80%81Netlify%20%E6%9C%89%E4%BB%80%E4%B9%88%E5%8C%BA%E5%88%AB%EF%BC%9F.png)

如果只是想快速发布一个活动页、内部看板或产品原型，Sites 通常更省步骤。

如果项目依赖特殊框架、私有网络、复杂数据库、后台服务或自定义基础设施，就应该先检查兼容性，必要时继续使用专业部署平台。

### 3.7 **代码管理 （Git 与 GitHub 工作流）**

Git = 本地代码版本管理工具
GitHub = 把代码放到网上协作的平台
Codex = 帮你读代码、改代码、跑命令的 AI 编程助手

### 3.8 **云端运行**

Codex 的云端任务适合在你不方便一直开着本地电脑时继续处理工作；如果你的账号和客户端支持移动端入口，也可以在外出时查看或推进部分任务。

* Local：Codex 直接改你电脑里的代码
* Worktree：Codex 在安全副本里改代码
* Cloud：Codex 在云端拉取 GitHub 仓库并处理任务

**云端运行和本地运行的区别**

![云端运行和本地运行的区别.png](image%2F%E4%BA%91%E7%AB%AF%E8%BF%90%E8%A1%8C%E5%92%8C%E6%9C%AC%E5%9C%B0%E8%BF%90%E8%A1%8C%E7%9A%84%E5%8C%BA%E5%88%AB.png)

### 3.9 **记忆系统**

让 Codex 记住一些长期有用的信息，方便以后继续工作。

**项目级AGENTS.md**：写给 Codex 看的项目规则说明书。

**AGENTS.md 放在哪里**

![AGENTS.md 放在哪里.png](image%2FAGENTS.md%20%E6%94%BE%E5%9C%A8%E5%93%AA%E9%87%8C.png)

**如何写 AGENTS.md**

可以直接交给AI来写，让AI总结这个项目的核心内容制作成 AGENTS.md

**项目级 AGENTS.md**：~/xxx项目/AGENTS.md

前端项目 AGENTS.md 模板

<details>
<summary>点击展开完整模板</summary>

```markdown
# AGENTS.md

## 项目说明

这是一个前端网页项目，用于构建产品页面、工具页面或个人作品展示页面。

## 技术栈

- React
- Vite
- Tailwind CSS
- JavaScript / TypeScript

## 常用命令

- 安装依赖：`npm install`
- 启动项目：`npm run dev`
- 构建项目：`npm run build`

## 项目结构

- `src/`：主要源代码
- `src/components/`：通用组件
- `src/pages/`：页面文件
- `src/assets/`：图片、图标等静态资源
- `public/`：公开静态文件

## 代码规范

- 优先使用 React 函数组件
- 优先使用 Tailwind CSS 写样式
- 不要引入 Bootstrap
- 不要大范围重构无关代码
- 修改时保持文件结构清晰
- 中文文案要自然、简洁、适合普通用户阅读

## UI 规则

- 页面要有清晰的信息层级
- 按钮、卡片、标题、留白要统一
- 移动端要基本可用
- 不要过度渐变、阴影和 AI 模板感
- 优先做真实产品感，而不是 Demo 感

## 禁止事项

- 不要修改 `.env`、`.env.local`
- 不要输出 API Key、token、密码
- 不要删除已有核心功能
- 不要随意新增大型依赖
- 不要直接改动和当前任务无关的文件

## 完成任务后

每次修改完成后，请输出：

1. 修改了哪些文件
2. 每个文件改了什么
3. 为什么这样改
4. 是否需要运行 `npm run build`
5. 提醒我检查 diff
```

</details>

**全局级 AGENTS.md**：~/.codex/AGENTS.md

打开Codex的设置，找到个性化

输入指令，这里的指令会作为你的个人通用偏好影响后续 Codex 会话

![全局级AGENTS.md.png](image%2F%E5%85%A8%E5%B1%80%E7%BA%A7AGENTS.md.png)

**Codex记忆的最佳使用方法**

![Codex记忆的最佳使用方法.png](image%2FCodex%E8%AE%B0%E5%BF%86%E7%9A%84%E6%9C%80%E4%BD%B3%E4%BD%BF%E7%94%A8%E6%96%B9%E6%B3%95.png)

- 当前会话 / prompt：最适合放“这次任务”的要求
- Memories：适合放“稳定偏好”和“常见背景”：~/.codex/memories/
- AGENTS.md：最适合放“必须遵守的项目规则”：
    - 全局级：~/.codex/AGENTS.md
    - 项目级：~/xxx项目/AGENTS.md
- Skills：适合放“可复用工作流”

### 3.10 **Chrome 插件**

Chrome 插件带来的变化，不只是让 Codex 多了一个打开网页的入口。

它解决了过去浏览器任务中的三个问题。

**第一个变化：可以使用现有登录状态**

很多真实工作都发生在需要登录的网站里。

Chrome 插件连接的是你当前使用的 Chrome Profile，也就是包含现有登录状态和浏览器环境的用户配置。

因此，Codex 可以在获得网站权限后，操作你已经登录的网站。

需要注意： 使用登录状态不等于把所有密码直接交给 Codex。

更准确的理解是： Chrome 扩展在用户授权的 Chrome Profile 中执行网页任务，网站权限和敏感操作仍然受到 ChatGPT 的确认机制控制。

**第二个变化：浏览器任务可以跨多个标签页**

**第三个变化：可以处理浏览器下载和文件上传**

## 四、标准工作流

## 五、实战案例库

## 附录 A、第三方模型接入

本节介绍第三方模型接入的非官方思路，并以 CC Switch + DeepSeek 为例。它不属于 OpenAI 官方功能，模型兼容性、稳定性、隐私和费用规则以对应第三方工具与模型服务商为准。

**什么是 CC Switch**

它不是 Claude Code 本体，也不是 Codex 本体，而是一个第三方开源桌面工具，用来统一管理不同 Agent 工具。

> 以前你要手动改 Claude Code、Codex、Gemini CLI 的配置文件。 现在 CC Switch 给你做成一个可视化面板，一键切换。

它最核心的用途有 3 个：

![CC Switch的核心功能.png](image%2FCC%20Switch%E7%9A%84%E6%A0%B8%E5%BF%83%E5%8A%9F%E8%83%BD.png)

[CC Switch 官网](https://ccswitch.io/zh/)