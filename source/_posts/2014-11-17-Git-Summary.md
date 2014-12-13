title: Git 总结
date: 2014-11-17 08:27:59
tags: 
- Git
description:
---

根据[廖雪峰的Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)总结而成

## 创建版本库

初始化一个Git仓库，使用`git init`命令

添加文件到Git仓库，分两步：

1. 使用命令`git add <file>`，可以反复使用，可以添加多个文件；
2. 使用命令`git commit -m "..."`，完成。

<!--more-->

## 查询状态

* 要随时掌握工作区的状态，使用`git status`命令。
* 如果`git status`告诉你有文件被修改过，用`git diff`查询修改内容。

## 版本回退

* `HEAD`指向的版本是当前版本，`HEAD^`指向上一个版本，`HEAD^^`指向上上一个版本，Git允许我们在不同版本之间穿梭，使用命令`git reset --hard <commit_id>`。
* 穿梭前用`git log`可以查看提交历史，以便确定要退到哪个版本。
* 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

## 工作区和暂存区

**工作区**（Working Directory）：执行`git init`所在的目录以及子目录，或者是`git clone`得到的文件夹。

**版本库**（Repository）：工作区有个隐藏的目录`.git`，这个不算工作区，而是Git的版本库。

Git版本库里存了很多东西，最重要的就是成为stage（或者index）的**暂存区**，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针`HEAD`。

* `git add`把文件添加到暂存区中
* `git commit`把暂存区中所有内容提交到当前分支

![工作区、版本库和暂存区](/img/wd.jpg)

## 管理修改

* 每次修改只有`add`到暂存区中，才能`commit`到分支
* 使用`git diff HEAD -- <file>`查看工作区和版本库里最新版本的区别

## 撤销修改

* 场景一：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改，用命令 `git checkout -- <file>`。

    这里有两种情况：一种是修改后还没有添加到暂存区，现在，撤销修改就回到和版本库一模一样的状态；一种是已经添加到暂存区后，又做了修改，现在，撤销修改就回到添加到暂存区的状态。
* 场景二：当你不但改乱了工作区某个文件的内容，还添加到暂存区时，想撤销修改，分两步，第一步用命令`git reset HEAD <file>`，撤销了向暂存区的添加，就回到情景一，第二步按情景一处理。
* 场景三：如果已经提交到分支，就按照版本回退处理。

## 删除文件

命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，通过命令 `git checkout -- <file>`，你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后修改的内容。

## 添加远程库

* 要关联一个远程库，使用命令 `git remote add origin git@server-name:path/repo-name.git`；
* 关联后，使用命令`git push -u origin master`第一次推送master分支的内容；
* 此后，每次本地提交后，只要有必要，就可以使用`git push origin master`推送最新修改。

## 从远程库克隆

要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone`命令克隆。Git支持多个协议，包括https，但通过ssh速度最快。

## 创建与合并分支

* 查看分支：`git branch`
* 创建分支：`git branch <branch name>`
* 切换分支：`git checkout <branch name>`
* 创建并切换分支：`git checkout -b <branch name>`
* 合并某分支到当前分支：`git merge <branch name>`
* 删除分支：`git branch -d <branch name>`

## 解决冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

用git log --graph命令可以看到分支合并图。

## 分支管理策略

合并分支时，使用`git merge --no-ff -m "..." <branch name>`就可以使用普通模式合并，能看出来曾经做过合并，而直接`git merge <branch name>`fast forward模式合并，看不出来曾经做过合并。

在实际开发中，我们按照几个基本原则进行分支管理：

* `master`分支应该是非常稳定，也就是仅用来发布新版本，平时不在上面干活。
* 干活在`dev`上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`分支上，在`master`分支发布1.0版本。
* 你和你的小伙伴们每个人都在`dev`上干活，每个人都有自己的分支，时不时往`dev`上合并就可以了。

![分支策略](/img/merge.png)

## Bug分支

修复Bug时，我们通过创建新的Bug分支进行修复，名字如issue-101，然后合并，最后删除该Bug分支。

如果手头工作没有完成时，先把工作现场用`git stash`存下来，然后去修复Bug，修复后，再用`git stash pop`，回到工作现场。

如果有多个保存的现场，可以用`git stash list`命令查看，然后用`git stash apply <stash name>`恢复，但这样恢复后，stash内容没有消失，还需要用`git stash drop <stash name>`来删除。

## Feature分支

开发一个新feature，最好新建一个分支。

如果要丢弃一个没有合并的分支，可以通过`git branch -D <branch name>`强行删除。

## 多人协作

* 查看远程库信息，使用`git remote -v`；
* 本地新建的分支如果不单独推送到远程，对其他人是不可见的；
* 从本地推送分支，使用`git push origin <branch name>`，如果推送失败，先用`git pull`抓取远程的新提交；
* 在本地创建与远程分支对应的分支，使用`git checkout -b <branch name> origin/<branch name>`，本地分支和远程分支的名称最好一致；
* 建立本地分支与远程分支的关联，使用`git branch --set-upstream <branch name> origin/<branch name>`;
* 从远程抓取分支，使用`git pull`，如果有冲突，先处理冲突。

哪些分支需要推送，哪些不需要呢？

* `master`分支是主分支，因此要时刻与远程同步；
* `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
* bug分支只用于在本地修改bug，没有必要推到远程了，除非老板要看你一周到底修复了几个bug；
* feature分支是否推到远程，取决于feature是否需要多人合作。

多人合作的工作模式通常是这样的：

* 首先，可以试图用`git push origin <branch name>`推送自己的修改；
* 如果推送失败，则因为远程分支比你的本地更新，需要`git pull`试图合并；
* 如果合并有冲突，则解决冲突，然后本地提交；
* 没有冲突或已解决冲突后，再用`git push origin <branch name>`推送就能成功！

如果`git pull`提示“no tracking information”，则说明本地分支和远程分支的链接关系没有创建，根据提示使用命令`git branch --set-upstream <branch name> origin/<branch name>`。

## 创建标签

* 命令`git tag <tag-name>`用于新建一个标签，默认是`HEAD`，也可以用`git tag <tag-name> <commit-id>`指定一个版本。
* `git tag -a <tag-name> -m 'blablabla...'`可以指定标签信息。
* 命令`git tag`查看所有标签。

## 操作标签

* 命令`git push origin <tag-name>`可以推送一个本地标签；
* 命令`git push origin --tags`可以推送全部未推送过的本地标签；
* 命令`git tag -d <tag-name>`可以删除一个本地标签；
* 命令`git push origin :refs/tags/<tag-name>`可以删除一个远程标签。




