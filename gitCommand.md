# Git commands

## 1. 常用

```
# 配置远程git版本仓库
git remote add origin git@github.com:example/example.git

# 多添加一个push地址，这样可以一次push到多个remote
git remote set-url --add origin git@gitee.com:example1/example1.git

# 下载代码及快速合并
git pull origin master

# 上传代码及快速合并
git push origin master

# 从远程仓库获取代码
git fetch origin

# 显示所有分支
git branch

# 切换到master分支
git checkout master

# 创建并切换到dev分支
git checkout -b dev

# 提交
git commit -m "Modified information"

# 查看状态
git status

# 查看提交历史
git log

# 设置默认编辑器为vim（git默认用nano）
git config --global core.editor vim

# 设置大小写敏感
git config core.ignorecase false

# 设置用户名
git config --global user.name "YOUR NAME"

# 设置邮箱
git config --global user.email "YOUR EMAIL ADDRESS"

```

## 别名 Alias

```
# 创建/查看本地分支
git config --global alias.br="branch"

# 切换分支
git config --global alias.co="checkout"

# 创建并切换到新分支
git config --global alias.cb="checkout -b"

# 提交
git config --global alias.cm="commit -m"

# 查看状态
git config --global alias.st="status"

# 拉取分支
git config --global alias.pullm="pull origin master"

# 提交分支
git config --global alias.pushm="push origin master"

# 单行、分颜色显示记录
git config --global alias.log="git log --oneline --graph --decorate --color=always"

# 复杂显示
git config --global alias.logg="git log --graph --all --format=format:'%C(bold blue)%h%C(reset) -%C(bold green)(%ar)%C(reset)%C(bold white)- %an%C(reset)%C(bold yellow)%d%C(reset)' --abbrev-commit --date=relative"

```


## 创建版本库

```
# 克隆远程版本库
git clone <url>

# 初始化本地版仓库
git init

```


## 修改和提交

```
# 查看状态
git status

# 查看变更内容
git diff

# 跟踪所有改动过的文件
git add .

# 跟踪指定的文件
git add <file>

# 文件改名
git mv <old> <new>

# 删除文件
git rm <file>

# 停止跟踪文件但不删除
git rm --cached <file>

# 提交所有更新过的文件
git commit -m "commit message"

# 提交最后一次提交
git commit --amend

# 修改最后一次提交的用户和邮箱
git commit --amend --author="NewAuthor <NewEmail@address.com>"

```

## 查看提交历史

```
# 查看提交历史
git log

# 查看指定文件的提交历史
git log -p <file>

# 以列表方式查看指定文件的提交历史
git blame <file>

```

## 撤销

```
# 撤销工作目录中所有未提交文件的修改内容
git reset --hard HEAD

# 撤销到某个特定版本
git reset --hard <version>

# 撤销指定的未提交文件的修改内容
git checkout HEAD <file>

# 同上一个命令
git checkout -- <file>

# 撤销指定的提交
git revert <commit>

```

## 分支与标签

```
# 显示所有本地分支
git branch

# 切换到指定分支或标签
git checkout <branch/tag>

# 创建新分支
git branch <new-branch>

# 删除本地分支
git branch -d <branch>

# 列出所有本地标签
git tag

# 基于最新提交创建标签
git tag <tagname>

# -a 指定标签名称，-m 指定标签说明
git tag -a "v1.0" -m "一些说明"

# 删除标签
git tag -d <tagname>

# 合并特定的 commit 到 dev 分支上
git checkout dev
git cherry-pick 62ecb3

```


## 合并与衍合

```
# 合并指定分支到当前分支
git merge <branch>

# 取消当前合并，重建合并前状态
git merge --abort

# 以合并 dev 分支到当前分支，有冲突则以 dev 为准
git merge dev -Xtheirs

# 衍合指定分支到当前分支
git rebase <branch>

```


## 远程操作

```
# 查看远程版本库信息
git remote -v

# 查看指定远程版本库信息
git remote show <remote>

# 添加远程版本库
git remote add <remote> <url>

# 删除指定的远程版本库
git remote remove <remote>

# 从远程库获取代码
git fetch <remote>

# 下载代码及快速合并
git pull <remote> <branch>

# 上传代码及快速合并
git push <remote> <branch>

# 删除远程分支或标签
git push <remote> :<branch/tag-name>

# 上传所有标签
git push --tags

```


## 打包

```
# 将 master 分支打包成 file.zip 文件，保存至上一级目录
git archive --format=zip --output ../file.zip master

# 打包 v1.2 标签的文件，保存至上一级目录 v1.2.zip 文件中
git archive --format=zip --output ../v1.2.zip v1.2

# 作用同上一条命令
git archive --format=zip v1.2 > ../v1.2.zip

```
git 打包会自动忽略 `.gitignore` 中指定的目录和文件，以及 `git` 目录


## 全局和局部配置

- 全局配置保存在：`$home/.gitconfig`
- 本地仓库配置保存在：`.git/config`


## 远程与本地合并

```
# 初始化本地仓库
git init

# 添加本地代码
git add .

# 提交本地代码
git commit -m "add local source"

# 下载本地代码
git pull origin master

# 合并 master 分支
git merge master

# 上传代码
git push -u origin master

```














```