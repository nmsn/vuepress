# ProGit

## 起步

### 初次运行Git前的配置

#### 用户信息

```bash
git config --global user.name xxx
git config --global user.email xxx
```

如果使用了`--global`，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情，Git都会使用那些信息。当你想针对特定项目使用不同的用户名称和邮件地址时，可以在那个项目目录下运行没有`--global`选项的命令来配置

#### 检查配置信息

git config --list

列出所有Git当时能找到的配置

你可能会看到重复的变量名，因为Git会从不同的文件同读取同一个配置。这种情况下，Git会使用它找到的每一个变量的最后一个配置

git config `<key>` 来检查Git的某一项配置

### 记录每次更新到仓库

#### 状态简览

git status -s/--short

#### 跳过使用暂存区域

git commit -a

#### 移除文件

git rm

git rm 命令后面可以列出文件或者目录的名字，也可以使用glob模式

#### 移动文件

git mv

可用来重命名

### 查看提交历史

git log

git log -p 用来显示每次提交的内容差异

git log -stat 每次提交的简略统计信息

#### 限制输出长度

git log -`<n>` 表示仅显示最近的若干条提交

--since/after

--until/before

--author 显示指定作者的提交

--grep 搜索提交说明中的关键字

### 撤销操作

git commit --amend

这个命令会将暂存区中的文件提交。如果自上次提交以来你还未做任何修改，那么快照会保持不变。而你所修改的只是提交信息

#### 取消暂存文件

git reset HEAD `<file>`

#### 撤销对文件的修改

git checkout -- `<file>`

### 远程仓库的使用

#### 查看远程仓库

git remote -v 显示远程仓库使用的Git保存的简写与其对应的URL

#### 添加远程仓库

git remote add `<shortname>` url 添加一个新的远程Git仓库，同时指定一个你可以轻松引用的简写

#### 从远程仓库中抓取与拉取

git fetch [remote-name]

git pull

#### 推送到远程仓库

git push origin master

#### 查看远程仓库

git remote show [remote-name]

#### 远程仓库的移除与重命名

git remote rename [old_name] [new_name]

git remote rm [remote-name]

### 打标签

git tag

#### 创建标签

Git使用两种主要类型的标签: 轻量标签与附注标签

#### 附注标签

git tag -a [tag] -m [message]

#### 轻量标签

git tag [message]

#### 后期打标签

git tag -a [tag] [hash]

#### 共享标签

git push origin [tag]

一次性推送多个标签

git push --tags

#### 检出标签

git checkout -b [branchname] [tag]

### Git别名

Git 并不会在你输入部分命令时自动推断出你想要的命令。如果不想每次都输入完整的 Git 命令，可以通过 git
config 文件来轻松地为每一个命令设置一个别名

例如:

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

## Git分支

### 分支简介

#### 分支创建

git branch [branch-name]

#### 分支切换

git checkout [branch-name]

### 分支的新建与合并

#### 新建分支

git checkout -b [new_branch]

新建一个分支并同时切换到那个分支上

等于

git branch [new_branch]
git checkout [new_branch]

git merge --no-ff

git branch -d

#### 分支的合并

需要指出的是，Git会自行决定选区哪一个提交作为最优的共同祖先，并以此作为合并的基础

#### 遇到冲突时的分支合并

### 分支管理

git branch -v --merged | --no-merged

### 分支开发工作流

#### 长期分支

#### 特性分支

### 远程分支

#### 推送

git push

#### 跟踪分支

git checkout --track origin/xxx

git checkou -b xx origin/xxx

git branch -u origin/xxx

#### 拉取

git fetch  get merge

git pull

#### 删除远程分支

git push origin --delete [branch_name]

### 变基

#### 变基的基本操作

git checkout [current_branch]

git rebase [target_branch]

#### 更有趣的变基例子

git rebase --onto [target_branch] [mid_branch] [current_branch]