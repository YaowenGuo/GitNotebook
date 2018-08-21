[TOC]

# fetch

一旦远程主机的版本库有了更新(Git术语叫做commit)，需要将这些更新取回本地，这时就要用到git fetch命令。

$ git fetch <远程仓库名>
上面命令将某个远程仓库的更新，全部取回本地。如果不写将默认将获取名为origin的远程仓库

默认情况下，git fetch取回所有分支(branch)的更新。如果只想取回特定分支的更新，可以指定分支名。

$ git fetch <远程仓库名> <分支名>
比如，取回origin主机的master分支。

$ git fetch origin master
所取回的更新，在本地主机上要用”远程仓库名/分支名”的形式读取。比如origin仓库的master，就要用origin/master读取。

git branch命令的-r选项，可以用来查看远程分支，-a选项查看所有分支。

$ git branch -r
origin/master

$ git branch -a
* master
remotes/origin/master
上面命令表示，本地主机的当前分支是master，远程分支是origin/master。

取回远程主机的更新以后，可以在它的基础上，使用git checkout命令创建一个新的分支。


$ git checkout -b newBrach origin/master
上面命令表示，在origin/master的基础上，创建一个新分支。

此外，也可以使用git merge命令或者git rebase命令，在本地分支上合并远程分支。

$ git merge origin/master
# 或者
$ git rebase origin/master
上面命令表示在当前分支上，合并origin/master。
