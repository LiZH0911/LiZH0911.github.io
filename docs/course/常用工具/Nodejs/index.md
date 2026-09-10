# Node.js

相关教程：

- [尚硅谷-Node.js](https://www.bilibili.com/video/BV1gM411W7ex/?spm_id_from=333.788.videopod.episodes&vd_source=46f99c7c1ed609a31f70615a4551767f)

## 一、Node.js 入门

### **1.1 Node.js 是什么**

Node.js 是一个开源的，跨平台的 JavaScript 运行环境

通俗来讲：Node.js 就是一款应用程序，是一款软件，它可以运行 JavaScript

### **1.2 Node.js 的作用**

**1.2.1 开发服务器应用**

![开发服务器应用.png](images%2F%E5%BC%80%E5%8F%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%BA%94%E7%94%A8.png)

![开发服务器应用2.png](images%2F%E5%BC%80%E5%8F%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%BA%94%E7%94%A82.png)

**1.2.2 开发工具类应用**

![开发工具类应用.png](images%2F%E5%BC%80%E5%8F%91%E5%B7%A5%E5%85%B7%E7%B1%BB%E5%BA%94%E7%94%A8.png)

**1.2.3 开发桌面端应用**

如代码编辑工具、设计工具、接口测试工具

![开发桌面端应用.png](images%2F%E5%BC%80%E5%8F%91%E6%A1%8C%E9%9D%A2%E7%AB%AF%E5%BA%94%E7%94%A8.png)

![开发桌面端应用2.png](images%2F%E5%BC%80%E5%8F%91%E6%A1%8C%E9%9D%A2%E7%AB%AF%E5%BA%94%E7%94%A82.png)

### **1.3 Node.js 安装**

### **1.4 命令行工具**

**命令的结构**

![命令的结构.png](images%2F%E5%91%BD%E4%BB%A4%E7%9A%84%E7%BB%93%E6%9E%84.png)

命令行本质：调用函数

**CMD 常用命令**

```bash
# 切换盘符
d:
# 切换工作目录
cd
# 查看工作目录
dir
# 查看所有目录
dir /s
```

> Tab 键可以自动补全命令

### **1.5 Node.js 初体验**

新建 `hello.js` 文件

```javascript
console.log('hello world')
```

命令行运行 `hello.js` 文件

```bash
node hello.js
```

### **1.6 Node.js 编码注意事项**

Node.js 中不能使用 BOM、DOM、AJAX 等 API

![浏览器中的 JavaScript.png](images%2F%E6%B5%8F%E8%A7%88%E5%99%A8%E4%B8%AD%E7%9A%84%20JavaScript.png)

![Node.js 中的 JavaScript.png](images%2FNode.js%20%E4%B8%AD%E7%9A%84%20JavaScript.png)

## 二、Buffer

### **2.1 Buffer 介绍**

Buffer 中文译为缓冲区，是一个类似于 Array 的对象，用于表示**固定长度**的字节序列

换句话说，Buffer就是一段固定长度的内存空间，用于处理二进制数据

### **2.2 Buffer 的创建**

Buffer 的创建有多种方式，下面介绍三种常见的方式

```javascript
let buf_1 = Buffer.alloc(10)
let buf_2 = Buffer.allocUnsafe(1000) # 不会清零，但更快
let buf_3 = Buffer.from('hello', 'utf-8') # 将字符串转化为 Buffer
let buf_4 = Buffer.from([1, 2, 3]) # 将数组转化为 Buffer
console.log(buf_1)
console.log(buf_2)
console.log(buf_3)
console.log(buf_4)
```

### **2.3 Buffer 与字符串的转换**

Buffer 与字符串的转换非常常见，下面介绍几种常见的方式

```javascript
let buf = Buffer.from('hello', 'utf-8')
console.log(buf.toString('utf-8'))
console.log(buf.toString())
```

## 三、计算机基础

### **3.1 计算机基本组成**

- CPU
- 内存：读写速度快，断电丢失数据
- 硬盘：读写速度慢，断电不丢失数据
- 主板
- 显卡：处理视频信号。输出可接显示器
- 散热器
- 外设

### **3.2 程序运行的基本流程**

操作系统：操作系统也是一种应用程序，用来管理和调度硬件资源

装系统：将操作系统这个程序装到硬盘

计算机启动的基本过程：

- windows 相关程序载入内存，启动过程就是这些程序的加载过程
- 视频信号交给显卡处理

计算机启动的基本过程：

- 程序一般保存在硬盘中，软件安装的过程就是将程序写入硬盘的过程
- 程序在运行时会加载进入内存，然后由 CPU 读取并执行程序

### **3.3 进程与线程**

进程是程序的一次执行过程

查看进程：任务管理器-进程

线程是一个进程中执行的一个执行流

一个线程是属于某个进程的，一个进程至少有一个线程

## 四、fs 模块

用于读写文件

## 五、HTTP 协议

![报文.png](images%2F%E6%8A%A5%E6%96%87.png)

### **5.1 请求报文**

**请求报文结构**：请求行 + 请求头 + 空行 + 请求体

![请求报文结构.png](images%2F%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87%E7%BB%93%E6%9E%84.png)

**5.1.1 请求行**：请求方法 + URL + HTTP 版号

![请求行结构.png](images%2F%E8%AF%B7%E6%B1%82%E8%A1%8C%E7%BB%93%E6%9E%84.png)

**常见请求方法**：

![请求方法.png](images%2F%E8%AF%B7%E6%B1%82%E6%96%B9%E6%B3%95.png)

**URL 结构**：

![URL结构.png](images%2FURL%E7%BB%93%E6%9E%84.png)

**5.1.2 请求头**：一些键值对

**5.1.3 请求体**：请求方法为 GET 时，请求体为空；请求方法为 POST 时，请求体为 JSON 格式数据

### **5.2 响应报文**

**响应报文结构**：响应行 + 响应头 + 空行 + 响应体

![响应报文结构.png](images%2F%E5%93%8D%E5%BA%94%E6%8A%A5%E6%96%87%E7%BB%93%E6%9E%84.png)

**5.2.1 响应行**：HTTP 版号 + 响应状态码 + 响应状态的描述

![响应行结构.png](images%2F%E5%93%8D%E5%BA%94%E8%A1%8C%E7%BB%93%E6%9E%84.png)

**响应状态码**：

![响应状态码.png](images%2F%E5%93%8D%E5%BA%94%E7%8A%B6%E6%80%81%E7%A0%81.png)

![响应状态码2.png](images%2F%E5%93%8D%E5%BA%94%E7%8A%B6%E6%80%81%E7%A0%812.png)

**响应状态的描述**：

![响应状态的描述.png](images%2F%E5%93%8D%E5%BA%94%E7%8A%B6%E6%80%81%E7%9A%84%E6%8F%8F%E8%BF%B0.png)

**5.2.2 响应头**：一些键值对

**5.2.3 响应体**：格式非常灵活

常见格式有：

1. HTML
2. CSS
3. JS
4. 图片
5. 视频
6. JSON

## 六、网络基础概念

### **6.1 IP**

**IP**：网络设备的数字标识

**IP 的作用**：实现网络设备间的通信

**IP 的分类**：

1. 本地回环 IP：127.0.0.1 ~ 127.255.255.254
2. 局域网 IP（私网 IP）：同一局域网内共享广域网/公网 IP
3. 广域网 IP（公网 IP）：用于连接互联网

### **6.2 端口**

**端口**：应用程序的数字标识

**端口的作用**：实现不同主机应用程序间的通信

![端口.png](images%2F%E7%AB%AF%E5%8F%A3.png)

## 七、http 模块

### **7.1 创建 HTTP 服务端**

```JavaScript
// 1. 导入 http 模块
const http = require('http')

// 2. 创建 HTTP 服务端
const server = http.createServer((request, response) => {
  response.end('hello world') // 设置响应体
})

// 3. 监听端口，启动服务
server.listen(9000, () => {
  console.log('server is running at http://127.0.0.1:9000')
})
```

## 八、模块化

```JavaScript
// 导入 JS 文件
const module = require('./module.js')

// 导入 JS 文件（省略后缀）
const module = require('./module')

// 导入 JSON 文件
const module = require('./module.json')
```

## 九、包管理工具

### **9.1 包管理工具介绍**

包：一组特定功能的源码集合

包管理工具：管理包的应用软件

常用包管理工具：

- **npm**
- yarn
- pnpm

### **9.2 npm**

**npm 安装**：node.js 安装时会自动安装 npm

```bash
# npm 包管理工具常用指令

# -------------------- 项目初始化 --------------------
# 初始化一个新的 Node.js 项目，生成 package.json 文件
# -y 表示使用默认配置，跳过交互式问答
npm init -y

# -------------------- 安装依赖 --------------------
# 安装包并将其添加到 dependencies（生产环境依赖）
# 默认会安装最新版本
npm install <package-name>

# 安装包并将其添加到 devDependencies（开发环境依赖）
# 常用于测试工具、构建工具等仅在开发时需要的包
npm install <package-name> --save-dev
# 或简写
npm i <package-name> -D

# 安装包的特定版本
# @ 符号后面指定版本号
npm install <package-name>@1.2.3

# 全局安装包（可在命令行任何位置使用）
# 通常用于 CLI 工具，如 nodemon、http-server 等
npm install -g <package-name>

# 根据 package.json 中的依赖列表安装所有包
# 新克隆项目后执行此命令
npm install
# 或简写
npm i

# -------------------- 卸载依赖 --------------------
# 卸载指定的包
npm uninstall <package-name>

# 卸载开发环境依赖
npm uninstall <package-name> --save-dev

# 全局卸载包
npm uninstall -g <package-name>

# -------------------- 更新依赖 --------------------
# 检查哪些包有可用的更新
npm outdated

# 更新所有包到符合版本规则的最新版本（遵循 package.json 中的 semver 规则）
npm update

# 更新指定包
npm update <package-name>

# 全局更新所有包
npm update -g

# -------------------- 查看信息 --------------------
# 查看当前项目中所有已安装的包（包括依赖树）
npm list
# 或简写（只显示顶层依赖）
npm list --depth=0

# 查看全局安装的包
npm list -g --depth=0

# 查看某个包的信息（版本、描述、依赖等）
npm view <package-name>

# 查看某个包的所有可用版本
npm view <package-name> versions

# 查看当前 npm 版本
npm -v

# -------------------- 脚本管理 --------------------
# 运行 package.json 中 scripts 字段定义的脚本
# 例如：npm run start、npm run build、npm run test
npm run <script-name>

# 运行 start 脚本的特殊简写（无需加 run）
npm start

# 运行 test 脚本的特殊简写
npm test

# -------------------- 缓存与清理 --------------------
# 清理 npm 缓存
# 当安装出现问题时，可以尝试清理缓存
npm cache clean --force

# 验证缓存文件的完整性
npm cache verify

# -------------------- 配置与登录 --------------------
# 查看 npm 配置
npm config list

# 设置 npm 镜像源（例如切换为淘宝镜像）
npm config set registry https://registry.npmmirror.com

# 查看当前镜像源
npm config get registry

# 登录 npm 账号（用于发布包）
npm login

# 发布当前包到 npm 仓库
npm publish

# 取消发布（有版本限制，需谨慎）
npm unpublish <package-name>@<version>

# -------------------- 其他实用命令 --------------------
# 查看某个包的安装路径
npm root -g

# 查看过期的包（与 npm outdated 类似但输出更简洁）
npm outdated

# 查看某个包的依赖关系树
npm ls <package-name>

# 修复安全问题（自动更新有漏洞的包）
npm audit fix

# 查看安全审计报告
npm audit
```