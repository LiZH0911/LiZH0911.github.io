# Docker

相关教程：

- [爬爬虾-Docker](https://www.bilibili.com/video/BV1THKyzBER6/?spm_id_from=333.337.search-card.all.click&vd_source=46f99c7c1ed609a31f70615a4551767f)


## 一、Docker 的核心概念

**Docker**：Docker 是一种软件部署技术，利用容器化技术为应用程序封装独立的运行环境。每个运行环境即为一个容器，承载容器运行的计算机称为宿主机

**容器与虚拟机的区别**：

- **Docker 容器**: 多个容器共享同一个系统内核
- **虚拟机**: 每个虚拟机包含一个操作系统的完整内核

**Docker 镜像（Image）**：容器的模板，可类比为软件安装包

**Docker 仓库（Registry）**：用于存放和分享 Docker 镜像的场所

- Docker Hub: Docker 的官方公共仓库，存储了大量用户分享的 Docker 镜像

## 二、Docker安装

**Docker 运行环境**：

- Docker 是基于 Linux 的容器化技术。在 Windows 和 Mac 电脑上，Docker 通过虚拟化一个 Linux 子系统来运行
- Linux 系统宿主机是最佳的 Docker 实战环境。

**Linux 系统安装 Docker**：

1. 访问 [getdocker.com](https://getdocker.com) 获取安装脚本。
2. 执行安装脚本（例如，通过`curl -fsSL https://get.docker.com -o get-docker.sh`下载脚本，然后执行`sudo sh get-docker.sh`）。
3. 安装完成后，若非`root`用户，需在所有`docker`命令前添加`sudo`以获取管理员权限。

**Windows 系统安装 Docker**：

1. 启用Windows功能: 勾选“Virtual Machine Platform”（虚拟机平台）和“适用于Linux的Windows子系统”（WSL）
2. 重启电脑
3. 安装WSL：以管理员身份打开命令提示符（CMD）；执行`wsl --set-default-version 2`将WSL默认版本设为2；执行`wsl --update`安装WSL（国内网络建议添加`--web-download`参数减少下载失败）
4. 下载并安装Docker Desktop: 从官方网站下载对应CPU架构的安装包（Windows通常为AMD64），按提示完成安装
5. 启动Docker Desktop: 需保持Docker Desktop软件运行
6. 验证安装: 在Windows终端输入`docker --version`，若能打印版本号则表示安装成功

**Mac 系统安装 Docker**：

1. 根据Mac电脑的芯片类型（Intel或Apple Silicon）下载对应的Docker Desktop安装包。
2. 按提示完成安装

## 三、下载镜像

**docker pull 命令**：用于从Docker仓库下载镜像到本地

**镜像名称**：

- 完整格式包含四部分：<span style="color: red">registry/namespace/image_name:tag</span>
- **registry**: 仓库地址。`docker.io`表示Docker Hub官方仓库，官方仓库可省略
- **namespace**: 命名空间，通常是作者或组织名称。`library`是 Docker 官方仓库的命名空间，可省略
- **image_name**: 镜像名称
- **tag**: 镜像标签，通常表示镜像版本。`latest`表示最新版本，可省略

**示例**：

- `docker pull nginx`：从 Docker Hub 官方仓库下载最新版 Nginx 镜像
- `docker pull docker.n8n.io/n8nio/n8n`：从 n8n 的私有仓库下载 n8n 镜像

**Docker Hub 网站**：Docker 官方仓库，可搜索、查看镜像详情（如官方镜像、版本号、使用说明）

**仓库地址/注册表（Registry） 与 镜像库（Repository）的区别**：

- repository = registry/namespace/image_name
- 一个镜像库中存放同一镜像的不同版本
- 整个 Docker Hub 网站可视为一个 Registry
- Nginx 可视为一个 Repository

**常用命令**：

```bash
# 下载镜像
docker pull nginx

# 列出所有已下载到本地的镜像
docker images

# 删除镜像，可指定镜像名称或ID
docker rmi image_name
docker rmi image_id

# 拉取特定CPU架构的镜像
# Docker镜像作为软件，在不同的CPU架构下（如AMD64、ARM64）有不同的版本
# 对于某些低功耗迷你主机（如香橙派），其CPU架构通常为ARM64
docker pull --platform=xxx nginx
```


## 四、运行容器

**常用命令**：

```bash
# 使用镜像创建 + 运行容器 (最重要命令)
# 用模具制造一个糕点/进行类的实例化
# 如果本地不存在指定镜像，docker run会先自动拉取镜像，再创建并运行容器
docker run 镜像名或镜像id
# -d：后台运行容器，不会阻塞当前窗口。控制台只打印容器ID，容器日志不会直接输出到终端。
docker run -d 镜像名或镜像id
# 容器内的网络与宿主机的网络是隔离的
# -p：将宿主机的端口映射到容器内部的端口
docker run -p 宿主机端口号:容器内部端口号 镜像名

# 查看正在运行的容器
# docker run后，原窗口被占用，可新开窗口执行docker ps
docker ps
# 支持查看运行和已停止的容器
docker ps -a

# 删除容器
docker rm 容器名或容器id
# -f：强制删除
docker rm -f 容器名或容器id
```

## 五、挂载卷

**挂载**：将宿主机的文件目录与容器内的文件目录进行绑定。使得在任一方修改该文件夹时，另一方都会同步修改 

- 挂载卷：宿主机上绑定的目录
- 目的：实现数据的持久化保存。当容器被删除时，容器内的数据也会被删除，但挂载卷可确保容器删除时，数据仍保存在宿主机上。
- 两种挂载方式：绑定挂载 与 命名卷挂载

**绑定挂载**：

```bash
# 绑定挂载
docker run -v 宿主机目录:容器内目录 镜像名
```

**命名卷挂载**：

- 让 Docker 自动创建一个挂载卷的存储空间，并为其命名
- 好处：命名卷第一次使用的时候，docker 会把容器目录中的内容同步到命名卷中，进行一个初始化；而绑定挂载没有这个功能

```bash
# 创建命名卷
docker volume create 卷的名字

# 查看命名卷信息（挂载卷在宿主机的真实目录）
docker volume inspect 卷的名字

# 命名卷挂载
docker run -v 卷的名字:容器内目录 镜像名

# 查看所有创建过的卷
docker volume list

# 删除卷
docker volume rm 卷的名字

# 删除所有没有任何容器在使用的卷
docker volume prune -a
```

## 六、docker run的其他参数

## 七、调试容器

## 八、构建镜像

## 九、docker网络

## 十、Docker Compose（多容器编排）