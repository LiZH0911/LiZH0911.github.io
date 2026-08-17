# Git

相关教程：

- [菜鸟教程-Git](https://www.runoob.com/git/git-workspace-index-repo.html)
- [廖雪峰-Git](https://liaoxuefeng.com/books/git/introduction/index.html)

## 一、提交与修改

```bash
# 克隆远程仓库到当前文件夹
git clone <仓库链接>

# 初始化 Git 仓库
git init

# 查看已跟踪的文件（版本库中的文件）
git ls-files

# 查看仓库当前状态（显示有变更的文件）
git status

# 比较文件差异（暂存区 vs 工作区）
git diff <filename>

# 准备提交当前文件夹中所有文件和非空文件夹（工作区—>暂存区）
git add . 
# 准备提交文件（工作区—>暂存区）
git add <filename>

# 准备删除（从暂存区和工作区删除文件）
git rm <filename>

# 提交（暂存区—>仓库）
git commit -m '功能1已完成'

# 查看提交的历史记录（按q退出）
git log
git log --oneline

# 从最后一次的提交里，把指定文件复制到工作区（会覆盖本地修改）
git checkout HEAD <filename>
```


## 二、分支管理

```bash

# 创建分支（两种方式）
git init -b <分支名> # 初始化时创建并切换
git branch <分支名> # 在现有仓库中创建

# 切换到指定分支
git checkout <分支名>

# 创建并切换到新分支
git checkout -b <分支名>

# 查看所有本地分支
git branch

# 重命名分支
git branch -m <旧分支名> <新分支名>

# 删除分支
git branch -d <分支名> # 删除已合并过的分支
git branch -D <分支名> # 强制删除分支（不管有没有合并）

# 合并指定分支到当前分支
git merge <被合并的分支> 

# 查看当前分支文件内容
cat <文件名>
```

## 三、远程仓库

```bash
# 关联远程仓库（已有本地仓库）
git remote add origin <远程仓库URL> # 远程仓库URL例如：https://github.com/***/***.git

# 将本地分支上传并合并到远程分支
git push -u origin <分支名>

# 查看远程分支列表
git branch -r

# 删除远程分支
git push origin --delete <分支名>

# 关联第二个远程仓库
git remote add gitee <Gitee仓库URL> # Gitee仓库URL例如：https://gitee.com/***/***.git

# 查看所有远程仓库名称
git remote show

# 查看远程仓库名称及链接
git remote -v

# 重命名远程仓库
git remote rename origin github # 将 origin 重命名为 github
```

## 四、提交冲突与解决方法

```bash
# 冲突解决一：能自动合并的冲突
# 1. 拉取远程代码并合并到本地
git pull
# 2. 在 Vim 中保存合并信息
#    按 i 进入编辑 → 输入信息 → 按 ESC → 输入退出命令 :wq 回车
# 3. 推送合并后的代码
git push

# 冲突解决二：手动解决冲突
# 查看冲突文件的修改内容
git diff <filename>
```

## 五、其他常用操作

```bash
# 配置用户名和邮箱（全局）
git config --global user.name "你的名字"
git config --global user.email "你的邮箱" # 邮箱随便写，例如：trump@bukaopu.com

# 补充文件到上一次提交（不修改提交信息）
git add <文件名>
git commit --amend --no-edit
```

**部署 MkDocs 到 GitHub Pages**：

```bash
# 0. 创建仓库
# 登录 GitHub，点击 + → New repository
# 仓库名称：你的用户名.github.io（必须完全一致）
# 选择 Public，不要勾选 "Add a README file"（因为你的项目已有文件）
# 点击 Create repository

# 1. 本地项目文件夹下初始化Git仓库（如果还没有）
git init

# 2. 关联远程仓库
git remote add origin https://github.com/LiZH0911/LiZH0911.github.io.git

# 3. 添加所有文件
git add .

# 4. 提交
git commit -m "首次提交 MkDocs 项目"

# 5. 推送到GitHub
git push -u origin main

# 6. 部署到 GitHub Pages（python环境下本地终端执行）
mkdocs gh-deploy --force
```