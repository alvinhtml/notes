# Git 使用手册

## HEAD、Index、Working Directory

Git里有三个区域很重要

- `HEAD` 指向最近一次commit里的所有snapshot
- `Index` 缓存区域，只有Index区域里的东西才可以被commit
- `Working Directory` 用户操作区域


## checkout分支
当你checkout分支的时候，git做了这么三件事情

- 将HEAD指向那个分支的最后一次commit
- 将HEAD指向的commit里所有文件的snapshot替换掉Index区域里原来的内容
- 将Index区域里的内容填充到Working Directory里

所以你可以发现，HEAD、Index、Working Directory这个时候里的内容都是一模一样的。

## Staged
一个文件仅仅changed是不能被commit的，Git要求只能提交Index里的东西。

所以需要git add。这个命令的意思是，把Changed的文件的内容同步到Index区域里。这样Working Directory和Index区域的内容就一致了。这个过程被称之为stage

## 分支操作

### 合并到上一次 commit

```bash
git add a.txt
git commit --amend

#将改动推送到远程服务器
git push origin HEAD --force
```

### 合并多个 commit

```bash
git rebase -i HEAD~3



# 命令
# p，pick = use commit
# r，reword = use commit，但编辑提交消息
# e，edit = use commit，但停止修改
# s，squash =使用提交，但融入先前的提交
# f，fixup = like“squash”，但丢弃此提交的日志消息
# x，exec =运行命令（线路的其余部分）使用shell
# d，drop =删除提交


# 如果你异常退出了 vi 窗口，不要紧张：
git rebase --edit-todo

# 这时候会一直处在这个编辑的模式里，我们可以回去继续编辑，修改完保存一下：
git rebase --continue
```
### 分支合并
```bash
# 创建并切换到分支 feature1
git checkout -b feature1

# 将 master 上的修改应用到 feature1
git rebase master

# 这里补充一点：rebase 做了什么操作呢？
# 首先，git 会把 feature1 分支里面的每个 commit 取消掉；
# 其次，把上面的操作临时保存成 patch 文件，存在 .git/rebase 目录下；
# 然后，把 feature1 分支更新到最新的 master 分支；
# 最后，把上面保存的 patch 文件应用到 feature1 分支上；

```

### 删除远分支

分支名前的冒号代表删除

```bash
git push alvin-ksphere :branch-name
```

### 批量删除分支

```bash
git branch |grep 'branchName' |xargs git branch -D
```
 - `git branch` 输出当前分支列表
 - `grep` 是对 `git branch` 的输出结果进行匹配，匹配值当然就是 `branchName`
 - `xargs` 的作用是将参数列表转换成小块分段传递给其他命令



### 查看分支切换日志
```bash
git reflog --date=local | grep branchname
```

## Stashing

你正在进行项目中某一部分的工作，里面的东西处于一个比较杂乱的状态，而你想转到其他分支上进行一些工作。问题是，你不想提交进行了一半的工作，否则以后你无法回到这个工作点。解决这个问题的办法就是 `git stash` 命令。


```bash
# 储藏当前变更
git stash

# 查看已储藏的变更列表
git stash list

# 使用最近的储藏
git stash apply

# 使用指定的储藏
git stash apply stash@{2}

# 使用储藏，同时立刻将其从堆栈中移走
git stash pop

# 使用储藏的修改，在进行了一些其他的修改后，又要取消之前所应用储藏的修改
git stash show -p stash@{0} | git apply -R

# 如果你沒有指定具体的某个储藏，Git 会选择最近的储藏：
git stash show -p | git apply -R

# 创建一个新的分支，检出你储藏工作时的所处的提交，重新应用你的工作，如果成功，将会丢弃储藏
git stash branch [branchname]

```


## reset

Git有三个区域，Git 的所有操作实际上是在操作这三个区域的状态（或内容）。

git reset 配合不同的参数，对这三个区域会产生不同的影响。

reset 实际上有3个步骤，根据不同的参数可以决定执行到哪个步骤(`--soft`, `--mixed`, `--hard`)。

- 改变 HEAD 所指向的 commit (`--soft`)
- 执行第 1 步，将 Index 区域更新为 HEAD 所指向的 commit 里包含的内容(`--mixed`)
- 执行第 1、2 步，将 Working Directory 区域更新为 HEAD 所指向的 commit 里包含的内容(`--hard`)

### 撤消 git add

```bash
# 撤消所有 add 到 index 区域里的文件
git reset HEAD

# 撤消所有 add 到 index 区域里的单个文件
git reset HEAD test.js
```




## 远程仓库

```bash
# 列出每一个远程仓库的简写
git remote

# 列出每一个远程仓库的简写，并显示url
git remote -v

# 添加一个远程仓库
git remote add repo-name  https://github.com/alvinhtml/miniui

# 设定本地master的上游分支
git branch --set-upstream-to=origin/master

# 用了参数 -u 之后，以后就可以直接用不带参数的git pull从之前push到的分支来pull
git push -u origin master
```

### 合并两个仓库

`git pull `失败，提示：`fatal: refusing to merge unrelated histories`

其实这个问题是因为两个根本不相干的 `git` 库， 一个是本地库， 一个是远端库， 然后本地要去推送到远端，远端觉得这个本地库跟自己不相干，所以告知无法合并

一个种方法是从远端库拉下来代码，然后本地加入代码，然后 push 到远程库，相当于基于远程库的一次 update

另一种方法是在 `git pull alvin-ksphere master` 后面加上 `--allow-unrelated-histories`， 把两段不相干的分支进行强行合并

```bash
git pull alvin-ksphere master --allow-unrelated-histories
```

后面再 push 就可以了 `git push alvin-ksphere master`

## cherry-pick

```bash
# 不包含 commit1
git cherry-pick commit1..commit5

# 包含 commit1
git cherry-pick commit1^..commit5
```

## 拉取 pull requests

```bash
# git fetch [remote] pull/[ID]/head:[local branch]
git fetch origin pull/1/head:pr-1
```

其中 [ID] 就是跟在 PR 标题后面的数字，也可以从链接获得：github.com/[用户名]/[仓库名]/pull/[ID] 。

[remote] 是远端仓库名字。通过 git remote -v 可列出所有添加了的远端仓库。

[local branch] 是 PR 的代码拉取下来后，放到的本地分支的名字。
