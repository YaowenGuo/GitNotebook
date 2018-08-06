# 临时保存

[TOC]

git stash 用于未完成某项功能，有更紧急的事要处理，但又不想产生一次提交。可用来暂存当前正在进行的工作。 比如想pull 最新代码， 又不想加新commit， 或者另外一种情况，为了fix 一个紧急的bug,  先stash, 使返回到自己上一个commit, 改完bug之后再stash pop, 继续原来的工作。需要注意的是，保护的现场仅用于本地的操作，并不能推送到远端，所以不能作为工作场所改变，需要将代码推同步到另一主机的应用场景。



## 保护现场
```
$ git stash [ save "说明信息"]
```
- 会对工作区和暂存区的内容进行保存。
- 没有跟踪（add 或 commit 过的新文件）无法临时保存。需要先 add 进行跟踪。
- 如果不给出注释，则会以当前的分支最后一次提交的注释作为保存现场的注释

那么就可以切换到其他分支进行工作，或者在当前分支上完成其他紧急的工作，比如修订一个bug测试提交。 或者下拉代码进行合并.

## 查看保存的缓存

当你多次使用’git stash’命令后，你的栈里将充满了未提交的代码，这时候你会对将哪个版本应用回来有些困惑，’git stash list’ 命令可以将当前的Git栈信息打印出来

```
$ git stash list

stash@{0}: On <branch_name>: 注释内容
```
它们是
[现场名]: On <分支名>：[注释]
如果是在分离头指针的状况下 stash 的，分支名会是 `(no branch)`。
当save [注释]并未给出时，将使用当前分支最后一次提交的注释作为本现场的注释。

如果一个使用了一个git stash，切换到一个分支，且在该分支上的工作未完成也需要保存它的工作现场。再使用git stash。那么stash 队列中 就有了两个工作现场。


命令：git stash  [save  [--patch]  [-k | --[no-]keep- index]  [-q | --quiet]  [<message>]]

这条命令实际上是第一条 git stash 命令的完整版。即如果需要在保存工作进度的时候使用指定的说明，必须使用如下格式：

git stash save "message... "。


## 还原暂存

```
$ git stash pop <--index> [<stash>]
```

如果不使用任何参数，会恢复最新保存的工作进度，并将恢复的工作进度从存储的工作进度列表中清除。

如果提供<stash>参数（来自于 git stash list 显示的列表），则从该<stash>中恢复。恢复完毕也将从进度列表中删除<stash>.

选项--index 除了恢复工作区的文件外，还尝试恢复暂存区。这也就是为什么在本章一开始恢复进度的时候显示的状态和保存进度前的略有不同。


- 如果在一个分支上想要恢复某一个工作现场怎么办：先用git stash list查看stash队列。确定要恢复哪个工作现场到当前分支。然后用git stash pop stash@{num}。num 就是你要恢复的工作现场的编号。

- 使用参数--patch 会显示工作区和 HEAD 的差异，通过对差异文件的编辑决定在进度中最终要保存的工作区的内容，通过编辑差异文件可以在进度中排除无关内容。

- 使用-k 或--keep-index 参数，在保存进度后不会将暂存区重置。默认会将暂存区和工作区强制重置。

- 如果有冲突，为了保证安全，暂存区的内容并不会被清除。这可以在解决冲突时出现错误，随时清空恢复，从新从暂存区恢复内容。确定解决完问题后，需要手动删除暂存区的暂存。

- 暂存并没有分支信息，在多个分支切换后，在该分支上使用git stash pop/apply。然而恢复的现场不一定是该分支的。好处是能够将修改通过暂存移动到其他分支。坏处是需要自己对缓存进行区分。

## 还原，但保留暂存

git stash apply  [-- index]  [<stash>]

使用git stash apply stash@{num}方法 除了不在stash队列删除外其他和git stash pop 完全一样。

同时注意使用git stash pop命令是恢复stash队列中的stash@{0}即最上层的那个工作现场。而且使用pop命令恢复的工作现场，其对应的stash 在队列中删除。apply 只将暂存区的内容恢复，并不删除对应的暂存。

- git show stash@{0} # see the last stash


## 丢弃暂存

无论是还原时有冲突暂存区的现场没有被清除，还是暂存的一些现场不想要了。为了保持工作场景清洁，都需要删除那些无用的暂存。
```
git stash drop  [<stash>]
```



## 清空暂存历史

如果想要清空stash队列则使用git stash clear。

git stash --help # for more info

## 将暂存建立独立分支

删除一个存储的进度。默认删除最新的进度。
git stash branch  <branch_name>  <stash>基于进度创建分支。



## stash 揭秘

了解一下 git stash 的机理会有几个好处：当保存了多个进度的时候知道从哪个进度恢复；综合运用前面介绍的 Git 知识点；了解 Git 的源码，Git 将不再神秘。

在执行 git stash 命令时，Git 实际调用了一个脚本文件实现相关的功能，这个脚本的文件名就是 git-stash。看看 git-stash 安装在哪里了。
```
$ git --exec-path

/usr/lib/git-core
```
如果查看一下这个目录，您会震惊的。
```
$ ls /usr/lib/git-core/

git     git-help         git-reflog

git-add git-http-backend git-relink

git-add--interactive git-http-fetch git-remote

git -am git-http-push git-remote-ftp

git-annotate git-imap-send git-remote-ftps

git-apply git-index-pack git-remote-http

..... 省略 40 余行 ....

.....................
```

实际上在 1.5.4 之前的版本中，Git 会将这样的一百多个以 git-<cmd>格式命名的程序安装到可执行路径中，而这样做的唯一好处就是不用借助任何扩展机制就可以实现命令行补齐：即键入 git-后，连续两次键入 Tab 键，就可以把这一百多个命令显示出来。这种方式随着 Git 子命令的增加显得越来越混乱，因此从 1.5.4 版本开始，不再提供 git-<cmd> 格式的命令，而是用唯一的 git 命令。而之前的名为 git-<cmd> 的子命令则保存在非可执行目录下，由 Git 负责加载。

在后面的章节中偶尔会看到形如 git-<cmd>字样的名称，以及同时存在的 git  <cmd>命令。可以这样理解：git-<cmd>作为软件本身的名称，而其命令行为 git  <cmd>。

最初很多 Git 命令都是用 Shell 或 Perl 脚本语言开发的，在 Git 的发展中一些对运行效率要求高的命令用 C 语言改写。而 git-stash（至少在 Git 1.7.4 版本）还是使用 Shell 脚本开发的，研究它比研究用 C 写的命令要简单得多。


$ file /usr/lib/git-core/git -stash

/usr/lib/git-core/git -stash: POSIX shell script text executable

解析 git-stash 脚本会比较枯燥，还是通过运行一些示例来理解更好一些。当前的进度保存列表是空的。
```
$ git stash list
```
下面在工作区中做一些改动。
```
$ echo Bye-Bye. >> welcome.txt
$ echo hello. > hack-1.txt
$ git add hack-1.Txt
$ git status -s

A  hack-1.txt
 M welcome.txt
```
可见暂存区中已经添加了新增的 hack-1.txt，修改过的 welcome. Txt 并未添加到暂存区。执行 git stash 保存一下工作进度。
```
$ git stash save "hack-1: hacked welcome.txt, new file hack-1.txt"

Saved working directory and index state On master: hack-1: hacked welcome.txt, new file hack-1.txt HEAD is now at 2b31c19 Merge commit 'acc2f69'
```
工作区恢复了修改前的原貌（实际上用了 git reset --hard HEAD 命令），文件 welcome.txt 的修改不见了，文件 hack-1.txt 整个都不见了。
```
git status -s

detached-commit.txt new - commit.txt welcome.txt
```
再做一个修改，并尝试保存进度。
```
$ echo fix. > hack-2.txt

git stash

No local changes to save
```
进度保存失败！可见本地没有被版本控制系统跟踪的文件并不能保存进度。因此本地新文件需要先执行添加操作，然后再执行 git stash 命令。
```
$ git add hack-2.txt

$ git stash

Saved working directory and index state WIP on master: 2b31c19 Merge commit 'acc2f69'

HEAD is now at 2b31c19 Merge commit 'acc2f69'
```
不用看就知道工作区再次恢复原状。如果这时执行 git stash list 会看到有两次进度保存。

$ git stash list

stash@{0}: WIP on master: 2b31c19 Merge commit 'acc2f69'

stash@{1}: On master: hack-1: hacked welcome.txt, new file hack-1.txt

从上面的输出中可以得出两个结论：

- 在用 git stash 命令保存进度时，如果提供说明则更容易通过进度列表找到保存的进度。
- 每个进度的标识都是 stash@{<n>}格式，像极了前面介绍的 reflog 的格式。实际上，git stash 就是用前面介绍的引用和引用变更日志（reflog）来实现的。

```
$ ls -l .git/refs/stash .git/logs/refs/stash

-rw-r--r--  1 yaowen  staff  289 Jul  2 21:25 .git/logs/refs/stash
-rw-r--r--  1 yaowen  staff   41 Jul  2 21:25 .git/refs/stash
```
那么 reflog 可以派上用场了。
```
$ git reflog show refs/stash

e5c0cdc refs/stash@{0}: WIP on master: 2b31c19 Merge commit 'acc2f69'

6cec9db refs/stash@{1}: On master: hack-1: hacked welcome.txt, newfile hack-t.txt
```
对照 git reflog 的结果和前面 git stash list 的结果，可以肯定用 git stash 保存进度，实际上会将进度保存在引用refs/stash 所指向的提交中。多次的进度保存，实际上相当于引用refs/stash 一次又一次的变化，而refs/stash 引用的变化由 reflog  (即. git/logs/refs/stash）所记录下来。这个实现是多么简单而巧妙啊。

一个新的疑问又出现了，如何在引用refs/stash中同时保存暂存区的进度和工作区中的进度呢? 查看一下引用refs/stash 的提交历史能够看出端倪。
```
git log --graph --pretty=raw refs/stash -2
```


... 详细弄清楚后再补充 ...
