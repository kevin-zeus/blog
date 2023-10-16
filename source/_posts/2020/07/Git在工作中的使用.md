---
title: Git在工作中的使用
date: 2020-07-10 20:18:37
tags:
---

# git在工作中的使用

## 一、参与开发任务的一般流程

### 1、若第一次参与项目
从远程库克隆项目到本地

```bash
git clone https://XXXXX/XXX/XXX.git
```

### 2、否则
1、更新本地的master分支

先看你的工作区当前分支是否在master分支，如果不在的话，先切换回master分支:

```bash
git checkout master
```

如果在master分支下，直接：

```bash
git pull
```

2、基于master分支，切出你的本地任务分支并切换到你的任务分支，假设你的任务是实现登录，你的任务分支自定义命名是`feature/Login`

> 注意，此时你是在master分支下，进行如下操作。在执行下面的操作之后，你会发现你的工作区的当前分支已经切换到`feature/Login`分支下，在该分支下愉快的去写你的登录任务代码吧

```bash
git checkout -b feature/Login
```

3、当你的登录任务在分支feature/Login下完成之后，并且联调测试均没有问题，也就是可以合并到master里面去了，进行如下操作：

```bash
# 切换回master分支
git checkout master
# 将你的任务分支合并到master里面
git merge feature/Login
# 将master推送到远程仓库
git push
```

### 场景1：当你还在写feature/Login功能的时候，同事告诉你master有更新

为了保证你的feature/Login内代码是基于最新的master，所以，这个时候你不可能重新回到master，pull，再建新分支吧，然后重写登录业务？

上面讲到feature/Login是基于旧的master切出来的分支，如果中途master有更新，其实借助rebase可以完美继承最新的master，步骤如下：

```bash
# 切回master分支
git checkout master
# 拉取最新的master提交内容
git pull
# 切回我的feature/Login分支， "-"表示的是切回当前分支的上一个分支
git checkout -    
# 将我的feature/Login分支的基准，移到最新的master提交上，也就是让我的feature/Login基于最新的master
git rebase master
```
> 注意：如果执行了rebase之后，分支名是一个哈希值，其实就是说明有冲突了，一般add之后再commit就完事

### 场景2：你在feature/Login下碰到问题需同事远程帮忙解决或需要回家解决

这个时候，因为你的feature/Login分支下的任务并没有联调和测试，也就是没有完成，不建议合并到master里面去！但是可能你需要把你的feature/Login下代码给同事看，或者说你需要回家用其他电脑继续解决，一般步骤如下：

```bash
# 先将工作区的修改内容add, "."表示全部，也可以直接写"路径/文件"指定单个或多个文件
git add .
# 提交
git commit -m '你的提交信息'
# 在远程库创建一个feature/Login分支，并推送到该分支
git push --set-upstream origin feature/Login 
```

> 注意，--set-upstream表示的是设置在当前分支下以后使用push的默认推送分支，所以如果你后面需要在本地feature/Login下继续推送提交到远程库时，只需要在当前分支下`git push`即可

### 场景3：同事上传了他的任务分支如feature/Register，需要你帮忙看下代码

因为这个feature/Register分支是同事创建的，你的本地仓库里没有，他按照上面场景2推送到了远程库上，你需要将该分支拉下即可：

```bash
# fetch一下，同步远程的分支
git fetch
# 切换到feature/Register
git checkout feature/Register
```

然后，就可以在该分支下看同事的修改

### 场景4：你和同事做的任务需要你合并成一个大任务，并做一些修改，然后再合并到master里面

假如同事做的任务在feature/Register分支，你做的在feature/Login，你们需要合并到一个大任务，叫做feature/Auth，并做修改然后过程如下：

```bash
# 保证你现在在master分支下，好习惯，先pull一下
git pull
# 新建一个分支并切换到该分支，叫做feature/Auth
git checkout -b feature/Auth
# 然后在该分支下合并那两个任务分支
git merge feature/Login feature/Register
# 假如你在feature/Auth做了修改，后续执行上面的第3步就行
```

## 二、常用git命令

1、切换分支

切换分支需要注意看当前的工作区和索引是否有修改，如果有必须要先完成commit才可以切换

```bash
git checkout <分支名>
```

2、删除分支

删除本分支需要先切换到其他分支，才可以删除本分支

```bash
git branch -D <分支名>
```

3、合并分支

假设当前分支是release/rt46，需要与feature/addTools合并

```bash
git merge feature/addTools
```

会将feature/addTools修改的内容合并到release/rt46里面

4、查看所有分支

```bash
git branch -a
```

可以通过grep通道限制显示的内容

```bash
git branch -a | grep release/rt4
```

5、添加远程版本库

```bash
git remote add <remote> <url>
```

remote指代远程版本库，一般默认远程版本库为origin

6、查看提交历史

```bash
git log # 查看所有提交历史
git log -p <file> # 查看指定文件的提交历史
```

7、撤销工作区所有未提交的文件修改内容

```bash
git reset --hard HEAD
```

8、撤销指定的未提交文件内容

```bash
git checkout HEAD <file>
```

9、更新所有线上代码和分支

```bash
git fetch
```
