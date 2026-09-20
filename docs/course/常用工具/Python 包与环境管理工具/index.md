# Python 包与环境管理工具

相关教程：

- [菜鸟教程-pip](https://www.runoob.com/python3/python3-pip.html)
- [菜鸟教程-Anaconda](https://www.runoob.com/python-qt/anaconda-tutorial.html)
- [菜鸟教程-uv](https://www.runoob.com/python3/uv-tutorial.html)
- [uv 官方教程](https://docs.astral.sh/uv/)

## 一、pip

**pip**：pip 是 Python 包管理工具，提供了对 Python 包的查找、下载、安装、卸载的功能。

**pip 常用命令**：

```bash
# 查看是否已经安装 pip
pip --version

# 下载安装包
pip install some-package-name

# 移除软件包
pip uninstall some-package-name

# 查看已经安装的软件包
pip list

# 导出当前环境中所有已安装的包
pip freeze > requirements.txt

# 导入 requirements.txt 中指定的包
pip install -r requirements.txt
```

**仅导出项目实际导入的包**：

```bash
# 安装 pipreqs
pip install pipreqs

# 扫描当前目录下的 .py 文件，生成 requirements.txt
pipreqs . --force

# 指定项目路径，生成 requirements.txt
pipreqs /path/to/your/project --force
```


## 二、Anaconda

**Anaconda**：Anaconda 是一个专门为数据科学和机器学习打造的“Python全家桶”发行版。

- conda：核心包管理器和环境管理器
- Python：基础的编程语言解释器
- Anaconda Navigator：图形化界面

**conda 环境管理**：

```bash
# 查看所有虚拟环境
conda env list

# 创建新环境（指定 Python 版本）
conda create --name <环境名> python=3.11

# 激活指定环境
conda activate <环境名>

# 退出当前环境
conda deactivate

# 删除虚拟环境（及其中所有包）
conda remove -n <环境名> --all

# 克隆现有环境
conda create --name <新环境名> --clone <被克隆的环境名>
```

**conda 包管理**：

```bash
# 查看当前环境中已安装的包
conda list

# 安装指定包
conda install <包名>

# 安装指定版本的包
conda install <包名>=<版本号>

# 从指定渠道安装包
conda install -c <渠道名> <包名>

# 更新指定包
conda update <包名>

# 更新所有包
conda update --all

# 卸载指定包
conda remove <包名>

# 搜索可用包
conda search <包名>
```

**conda 环境导出与导入**：

```bash
# 导出当前环境配置到文件
conda env export > environment.yml

# 仅导出显式安装的包（不含依赖）
conda env export --from-history > environment.yml

# 从配置文件创建环境
conda env create -f environment.yml

# 更新环境（同步配置文件）
conda env update -f environment.yml
```

**Jupyter Notebook**：Jupyter 是一个交互式的计算环境，支持多种编程语言，但在 Anaconda 中主要用于 Python。它允许用户创建和共享包含实时代码、方程式、可视化和叙述文本的文档。

```bash
# 安装 Jupyter Notebook
conda install jupyter

# 启动 Jupyter Notebook
jupyter notebook
```

## 三、uv

uv 是由 Astral 公司开发的一款用 Rust 编写的 Python 包管理器和环境管理器，主要目标是提供比现有工具快 10-100 倍的性能，同时保持简单直观的用户体验。

uv 可以替代 pip、virtualenv、pip-tools、pyenv 等工具，提供依赖管理、虚拟环境创建、Python 版本管理等一站式服务。

**1、Python 版本管理**

```bash
# 查看可用的 Python 版本
uv python list
# 安装特定版本 python
uv python install 3.11.6
# 设置全局默认 Python 版本
uv python default 3.12
# 为当前项目固定 Python 版本（会创建 .python-version 文件）
uv python pin 3.12
```

**2、虚拟环境管理**

```bash
# 创建虚拟环境
# 在当前目录创建名为 .venv 的虚拟环境（使用系统默认 Python）
uv venv
# 使用指定 Python 版本创建虚拟环境
uv venv --python 3.12

# 激活虚拟环境
# macOS / Linux
source .venv/bin/activate
# Windows（PowerShell）
.venv\Scripts\activate

# 退出虚拟环境
deactivate
```

日常开发中可以使用 `uv run` 直接运行脚本，无需手动激活虚拟环境。

**3、包管理（pip 兼容模式）**

uv 提供了与 pip 完全兼容的命令接口，可以直接替换已有工作流中的 pip 命令：

```bash
# 安装最新版本
uv pip install requests

# 安装特定版本
uv pip install requests==2.31.0

# 从 requirements.txt 批量安装
uv pip install -r requirements.txt

# 升级包
uv pip install --upgrade requests

# 卸载包
uv pip uninstall requests

# 查看已安装的包：
uv pip list

# 导出当前环境的依赖到 requirements.txt
uv pip freeze > requirements.txt
```

**4、项目管理（推荐方式）**

uv 支持以 `pyproject.toml` 为中心的现代项目管理方式，这是比 pip 模式更推荐的使用方法，尤其适合团队协作和多环境部署。

```bash
# 初始化项目
uv init my_project
cd my_project

# 添加和移除依赖
# 在项目模式下，推荐使用 uv add 和 uv remove 管理依赖，它们会自动更新 pyproject.toml 和 uv.lock
# 添加生产依赖
uv add requests
# 添加指定版本的依赖
uv add "requests>=2.31.0"
# 添加开发依赖（只在开发环境使用，如测试框架）
uv add --dev pytest ruff
# 移除依赖
uv remove requests

# 安装项目全部依赖（uv sync）
# 克隆项目或更新 pyproject.toml 后，运行以下命令一键安装所有依赖
uv sync
```

如果安装速度慢，可以在 pyproject.toml 中设置国内镜像源：

```toml
[tool.uv]
index-url = "https://pypi.tuna.tsinghua.edu.cn/simple"
```

**5、运行脚本（uv run）**

uv run 是 uv 中非常实用的命令，可以**无需手动激活虚拟环境**直接运行脚本或命令，uv 会自动找到并使用正确的环境

```bash
# 直接运行 Python 脚本
uv run main.py

# 运行项目中的测试
uv run pytest

# 运行任意命令（在虚拟环境的上下文中执行）
uv run python -c "import requests; print(requests.__version__)"
```

**6、迁移到 uv**

从 pip + virtualenv 迁移：

```bash
# 创建并激活虚拟环境
uv venv
source .venv/bin/activate

# 安装原有依赖
uv pip install -r requirements.txt
```