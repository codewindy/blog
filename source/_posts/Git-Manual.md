
---
title: GIT Manual
date: 2017/3/28 8:59
tags: git

---


* 常用 Git 命令清单。几个专用名词的译名如下。
* Workspace：工作区
* Index / Stage：暂存区
* Repository：仓库区（或本地仓库）
* Remote：远程仓库
<!--more-->

# 新建代码库
```
// 在当前目录新建一个Git代码库
$ git init

// 新建一个目录，将其初始化为Git代码库
$ git init --bare [project-name]

// 下载一个项目和它的整个代码历史
$ git clone [url]
```

![](http://ot2hu9qoc.bkt.clouddn.com/17-8-28/5617535.jpg)
# 配置
Git的设置文件为`.gitconfig`，它可以在用户主目录下（全局配置），也可以在项目目录下（项目配置）。
```
// 显示当前的Git配置
$ git config --list

// 编辑Git配置文件
$ git config -e [--global]

// 设置提交代码时的用户信息
$ git config [--global] user.name "[name]"
$ git config [--global] user.email "[email address]"
```

# 查看3种状态
* 工作区：项目所在目录
* 暂存态：add 到暂存区
* 仓库：commit 到仓库


```
//objets
[git@localhost .git]$ find /home/git/jd.git/.git/objects/ -type f
/home/git/jd.git/.git/objects/ce/a2a9038c107da69ce2222aeb0b47f83df7c1a0
/home/git/jd.git/.git/objects/bf/6399354dd8bd0538549b9dd254ffe48843c6bf
/home/git/jd.git/.git/objects/7f/55fc6e3fcc69a0cc36a290b671a9a60ff1ee7b

//HEAD
[git@localhost .git]$ cat HEAD
ref: refs/heads/master

//master
[git@localhost .git]$ cat refs/heads/master
7f55fc6e3fcc69a0cc36a290b671a9a60ff1ee7b

//commit
[git@localhost .git]$ git cat-file commit 7f55
tree bf6399354dd8bd0538549b9dd254ffe48843c6bf
author yangqinghua <yqh700@163.com> 1491724859 +0800
committer yangqinghua <yqh700@163.com> 1491724859 +0800

is first commit

//tree
[git@localhost .git]$ git ls-tree bf63
100644 blob cea2a9038c107da69ce2222aeb0b47f83df7c1a0    main.java

//blob
[git@localhost .git]$ git show cea2
this is a java file

//hash
[git@localhost jd.git]$ git hash-object main.java
cea2a9038c107da69ce2222aeb0b47f83df7c1a0
```

# 增加/删除文件
```
// 添加指定文件到暂存区
$ git add [file1] [file2] ...

// 添加指定目录到暂存区，包括子目录
$ git add [dir]

// 添加当前目录的所有文件到暂存区
$ git add .

// 添加每个变化前，都会要求确认
// 对于同一个文件的多处变化，可以实现分次提交
$ git add -p

// 删除工作区文件，并且将这次删除放入暂存区
$ git rm [file1] [file2] ...

// 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]

// 改名文件，并且将这个改名放入暂存区
$ git mv [file-original] [file-renamed]
```

# 代码提交
```
// 提交暂存区到仓库区
$ git commit -m [message]

// 提交暂存区的指定文件到仓库区
$ git commit [file1] [file2] ... -m [message]

// 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

// 提交时显示所有diff信息
$ git commit -v

// 使用一次新的commit，替代上一次提交
// 如果代码没有任何新变化，则用来改写上一次commit的提交信息
$ git commit --amend -m [message]

// 重做上一次commit，并包括指定文件的新变化
$ git commit --amend [file1] [file2] ...
```

# 分支
```
// 列出所有本地分支
$ git branch

// 列出所有远程分支
$ git branch -r

// 列出所有本地分支和远程分支
$ git branch -a

// 新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

// 新建一个分支，并切换到该分支
$ git checkout -b [branch]

// 新建一个分支，指向指定commit
$ git branch [branch] [commit]

// 新建一个分支，与指定的远程分支建立追踪关系
$ git branch --track [branch] [remote-branch]

// 切换到指定分支，并更新工作区
$ git checkout [branch-name]

// 切换到上一个分支
$ git checkout -

// 建立追踪关系，在现有分支与指定的远程分支之间
$ git branch --set-upstream [branch] [remote-branch]

// 合并指定分支到当前分支
$ git merge [branch]

// 选择一个commit，合并进当前分支
$ git cherry-pick [commit]

// 删除分支
$ git branch -d [branch-name]

// 删除远程分支
$ git push origin --delete [branch-name]
$ git branch -dr [remote/branch]
```

# 标签
```
// 列出所有tag
$ git tag

// 新建一个tag在当前commit
$ git tag [tag]

// 新建一个tag在指定commit
$ git tag [tag] [commit]

// 删除本地tag
$ git tag -d [tag]

// 删除远程tag
$ git push origin :refs/tags/[tagName]

// 查看tag信息
$ git show [tag]

// 提交指定tag
$ git push [remote] [tag]

// 提交所有tag
$ git push [remote] --tags

// 新建一个分支，指向某个tag
$ git checkout -b [branch] [tag]
```

# 查看信息
```
// 显示有变更的文件
$ git status

// 显示当前分支的版本历史
$ git log

// 显示commit历史，以及每次commit发生变更的文件
$ git log --stat

// 搜索提交历史，根据关键词
$ git log -S [keyword]

// 显示某个commit之后的所有变动，每个commit占据一行
$ git log [tag] HEAD --pretty=format:%s

// 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
$ git log [tag] HEAD --grep feature

// 显示某个文件的版本历史，包括文件改名
$ git log --follow [file]
$ git whatchanged [file]

// 显示指定文件相关的每一次diff
$ git log -p [file]

// 显示过去5次提交
$ git log -5 --pretty --oneline

// 显示所有提交过的用户，按提交次数排序
$ git shortlog -sn

// 显示指定文件是什么人在什么时间修改过
$ git blame [file]

// 显示暂存区和工作区的差异
$ git diff

// 显示暂存区和上一个commit的差异
$ git diff --cached [file]

// 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD

// 显示两次提交之间的差异
$ git diff [first-branch]...[second-branch]

// 显示今天你写了多少行代码
$ git diff --shortstat "@{0 day ago}"

// 显示某次提交的元数据和内容变化
$ git show [commit]

// 显示某次提交发生变化的文件
$ git show --name-only [commit]

// 显示某次提交时，某个文件的内容
$ git show [commit]:[filename]

// 显示当前分支的最近几次提交
$ git reflog
```

# 远程同步
```
// 下载远程仓库的所有变动
$ git fetch [remote]

// 显示所有远程仓库
$ git remote -v

// 显示某个远程仓库的信息
$ git remote show [remote]

// 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

// 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

// 上传本地指定分支到远程仓库
$ git push [remote] [branch]

// 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

// 推送所有分支到远程仓库
$ git push [remote] --all
```

# 撤销
```
// 恢复暂存区的指定文件到工作区
$ git checkout [file]

// 恢复某个commit的指定文件到暂存区和工作区
$ git checkout [commit] [file]

// 恢复暂存区的所有文件到工作区
$ git checkout .

// 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
$ git reset [file]

// 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard

// 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
$ git reset [commit]

// 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]

// 重置当前HEAD为指定commit，但保持暂存区和工作区不变
$ git reset --keep [commit]

// 新建一个commit，用来撤销指定commit
// 后者的所有变化都将被前者抵消，并且应用到当前分支
$ git revert [commit]

// 暂时将未提交的变化移除，稍后再移入
$ git stash
$ git stash pop
```

# 其他
```
// 生成一个可供发布的压缩包
$ git archive
```
# Set up GitHub push with SSH keys
```shell script

Create a repo. Make sure there is at least one file in it (even just the README) Generate ssh key:

ssh-keygen -t rsa -C "your_email@example.com"
Copy the contents of the file ~/.ssh/id_rsa.pub to your SSH keys in your GitHub account settings. Test SSH key:

ssh -T git@github.com
clone the repo:
git clone git://github.com/username/your-repository
Now cd to your git clone folder and do:

git remote set-url origin git@github.com:username/your-repository.git
Now try editing a file (try the README) and then do:

git add -A
git commit -am "my update msg"
git push
```
## 注释规范

```
feature：新增feature
fix: 修复bug
docs: 仅仅修改了文档，比如README, CHANGELOG, CONTRIBUTE等等
sql: 新增、修改SQL文件
style: 仅仅修改了空格、格式缩进、符号等等，不改变代码逻辑
refactor: 代码重构，没有加新功能或者修复bug
perf: 优化相关，比如提升性能、体验
test: 测试用例，包括单元测试、集成测试等
chore: 改变构建流程、或者增加依赖库、工具等
revert: 回滚到上一个版本
```

每个commit必须包含以上注释，方便review。

## 分支规范

- master分支：生产分支，用于存放发布到生产环境的代码。该分支只能从其他分支合并，不能直接修改推送。
- feature/*分支：功能分支，用来开发新功能。开发完成后合入测试或者生产分支，被合并后删除。
- hotfix/*分支：补丁分支，用来紧急修复生产环境的缺陷。开发完成后合入测试或者生产分支，被合并后删除。
- 环境分支：可选项，如果有要求可以加测试环境的分支，规范同生产分支。

每次开发从master分支中检出，命名为feature/version_no，开发完成后通过pull request/merger request合入环境分支进行测试，最后合入master分支。上线部署后，在master分支增加版本号tag。

## Label规范

- feature：新功能
- enhancement：性能优化或者功能增强
- bug：问题修复
- backend：后端功能（如果前后端代码都有）
- frontend：前端功能（如果前后端代码都有）
- 其他特性label

Issues和Pull Request/Merge Request最好都加上Label，方便分类。

* [git rebase教程](https://blog.csdn.net/zeng6325998/article/details/109320301?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-0&spm=1001.2101.3001.4242)
* [git rebase push commit](https://blog.csdn.net/u010176097/article/details/96875148)