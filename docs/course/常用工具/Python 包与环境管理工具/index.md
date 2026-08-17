# Python 包与环境管理工具

相关教程：

- [菜鸟教程-pip](https://www.runoob.com/python3/python3-pip.html)
- [菜鸟教程-Anaconda](https://www.runoob.com/python-qt/anaconda-tutorial.html)
- [菜鸟教程-uv](https://www.runoob.com/python3/uv-tutorial.html)

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

暂未用到，以后用到再学