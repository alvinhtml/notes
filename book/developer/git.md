# Git 使用手册

## HEAD、Index、Working Directory

Git 里有三个区域很重要

- `HEAD` 指向最近一次 commit 里的所有 snapshot
- `Index` 缓存区域，只有 Index 区域里的东西才可以被 commit
- `Working Directory` 用户操作区域

## checkout 分支

当你 checkout 分支的时候，git 做了这么三件事情

- 将 HEAD 指向那个分支的最后一次 commit
- 将 HEAD 指向的 commit 里所有文件的 snapshot 替换掉 Index 区域里原来的内容
- 将 Index 区域里的内容填充到 Working Directory 里

所以你可以发现，HEAD、Index、Working Directory 这个时候里的内容都是一模一样的。

## Staged

一个文件仅仅 changed 是不能被 commit 的，Git 要求只能提交 Index 里的东西。

所以需要 git add。这个命令的意思是，把 Changed 的文件的内容同步到 Index 区域里。这样 Working Directory 和 Index 区域的内容就一致了。这个过程被称之为 stage

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

```bash
git push alvin-csphere-frontend --delete login-background

# 分支名前的冒号代表删除
git push alvin-ksphere :branch-name
```

### 批量删除分支

```bash
git branch |grep 'branchName' |xargs git branch -D
```

- `git branch` 输出当前分支列表
- `grep` 是对 `git branch` 的输出结果进行匹配，匹配值当然就是 `branchName`
- `xargs` 参数传递命令。用于将标准输入作为命令的参数传给下一个命令。

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
git stash pop stash@{1}

# 使用储藏的修改，在进行了一些其他的修改后，又要取消之前所应用储藏的修改
git stash show -p stash@{0} | git apply -R

# 如果你沒有指定具体的某个储藏，Git 会选择最近的储藏：
git stash show -p | git apply -R

# 创建一个新的分支，检出你储藏工作时的所处的提交，重新应用你的工作，如果成功，将会丢弃储藏
git stash branch [branchname]

# 删除一个存储的进度。如果不指定stash_id，则默认删除最新的存储进度。
git stash drop stash@{1}

# 删除所有存储的进度。
git stash clear
```

## reset

Git 有三个区域，Git 的所有操作实际上是在操作这三个区域的状态（或内容）。

git reset 配合不同的参数，对这三个区域会产生不同的影响。

reset 实际上有 3 个步骤，根据不同的参数可以决定执行到哪个步骤(`--soft`, `--mixed`, `--hard`)。

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
git cherry-pick commit1 commit2 commit3 commit4 commit5

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

## Git 打补丁

Git 提供了两种补丁方案，一是用 git diff 生成的 UNIX 标准补丁 .diff 文件，二是 git format-patch 生成的 Git 专用 .patch 文件。

.diff 文件只是记录文件改变的内容，不带有 commit 记录信息,多个 commit 可以合并成一个 diff 文件。

.patch 文件带有记录文件改变的内容，也带有 commit 记录信息,每个 commit 对应一个 patch 文件。

使用 git 的 format-patch 和 am 命令进行生成 patch 和打 patch

1. 对于 git 这种以 project 为单位的修改，尤其是涉及到多个文件夹下的多个文件的改动时，非常方便，能够记录所有的改动（添加，修改，删除文件等）
2. 可以保存 commit 信息。
3. 能够灵活的获取 patch。可以获取任意两个 commit 之间的 patch 集。

### git format-patch

```bash
# 生成最近的1次commit的patch
git format-patch HEAD^

# 生成最近的4次commit的patch
git format-patch HEAD^^^^

# n指从sha1 id对应的commit开始算起n个提交
git format-patch 【commit sha1 id】-n

# 生成两个commit间的修改的patch（包含两个commit. <r1>和<r2>都是具体的commit号)
git format-patch <r1>..<r2>

# 生成单个commit的patch
git format-patch -1 <r1>

# 生成某commit以来的修改patch（不包含该commit）
git format-patch <r1>

# 生成从根到r1提交的所有patch
git format-patch --root <r1>　　　　　　　　　　　　
```

### git diff

```bash
# git diff  【commit sha1 id】 【commit sha1 id】 >  【diff文件名】

git diff  2a2fb4539925bfa4a141fe492d9828d030f7c8a8  89aebfcc73bdac8054be1a242598610d8ed5f3c8 > patch.diff
```

### 应用 patch 和 diff

```bash
# 查看patch的情况
git apply --stat 0001-limit-log-function.patch

# 检查 patch/diff 是否能正常打入
git apply --check path/to/xxx.patch
git apply --check path/to/xxx.diff

# 打入 patch/diff
git apply path/to/xxx.patch
git apply path/to/xxx.diff

# 添加 -s 或者 --signoff，还可以把自己的名字添加为 signed off by 信息，作用是注明打 patch 的人是谁，因为有时打 patch 的人并不是 patch 的作者
git am --signoff 0001-limit-log-function.patch

# 将路径 ~/patch-set/*.patch 按照先后顺序打上
git am ~/patch-set/*.patch

# 当 git am 失败时，用以将已经在 am 过程中打上的 patch 废弃掉, 返回没有打 patch 的状态
git am --abort

#当 git am 失败，解决完冲突后，这条命令会接着打 patch, git am --resolved 和 git am --continue 是一样的
git am --resolved
```

如果打 Patch 的过程中发生了冲突（conflicts），怎么办？

方案一（个人推荐）：

1. 根据 git am 失败的信息，找到发生冲突的具体 patch 文件，然后用命令 `git apply --reject <patch_name>`，强行打这个 patch，发生冲突的部分会保存为 `.rej`，未发生冲突的部分会成功打上 patch
2. 根据 `.rej` 文件，通过编辑该 patch 文件的方式解决冲突。
3. 废弃上一条 am 命令已经打了的 patch：`git am --abort`
4. 重新打 patch：`git am ~/patch-set/*.patchpatch`

方案二：

1. 根据 git am 失败的信息，找到发生冲突的具体 patch 文件，然后用命令 `git apply --reject <patch_name>`，强行打这个 patch，发生冲突的部分会保存为 `.rej`，未发生冲突的部分会成功打上 patch
2. 根据 `.rej` 文件，通过编辑发生冲突的 code 文件的方式解决冲突。
3. 将该 patch 涉及到的所有文件（不仅仅是发生冲突的文件）通过命令 `git add <file_name>` 添加到工作区中
4. 告诉 git 冲 突已经解决，继续打 patch: `git am --resolved`

分析：方案一和方案二主要区别是解决冲突的方法不一样。方案一是通过编辑 patch 文件的方式解决冲突，方案二十通过编辑冲突 code 文件的方式解决冲突。这两种方案区别比较大：经过实验，核心区别在于，方案二无法验证冲突有没有切实的解决。即使你在方案二的第二步乱改一通，也能“打完”发生冲突的 patch（并没有检测修改后的 code 文件跟 patch 期望的是否相同）。因此，如果采用方案二，那么再解决 code 文件冲突后，需要人工去确认修改的正确性。

### 打包 diff

```bash
git diff b70f3992cd63de7b9652326896b72e8f10f2de16 |grep ^diff|sed -e 's/^.* b\///'|xargs tar zcf /tmp/t.tgz
```

1. fork 主仓库
2. 克隆仓库到本地
  ```bash
  git clone http://gitlab.example.com/your_name/nad.git nad
  ```
3. 显示本地有哪些远程仓库, 并添加主仓库到本地
```bash
git remote -v
git remote add upstream http://gitlab.example.com/lijingfei/nad.git
```
  upstream 是远程仓库的别名 `git remote -v` 可以看到本地有两个远程仓库
```
origin	http://gitlab.example.com/your_name/nad.git
upstream	http://gitlab.example.com/lijingfei/nad.git
```
5. 拉取（更新）代码
```bash
git fetch upstream
```
  - 拉取代码永远是从主仓库，也就是公用仓库拉取

6. 每开始一个任务前，基于主仓库的 master 分支，创建一个新的分支，这样保证代码永远是最新的
```bash
git checkout upstream/master -b new_branch
```
7. 提交代码
```bash
git add <file>
git commit -m  'message'
```
8. 推送到远程仓库 origin（自己 fork 的那个仓库）
```bash
git push origin new_branch
```
9. 提 PR （pull requests）从 origin/new_branch ->  upstream/master
  - 指定 review 人员
  - 在这一步解决代码冲突

10. 管理员审核后 marge PR
11. 开始新的任务，拉取代码，创建新分支
```bash
git fetch upstream
git checkout upstream/master -b new_branch_2
```
