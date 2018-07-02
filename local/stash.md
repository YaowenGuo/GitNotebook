# 临时保存

[TOC]

git stash 用于未完成某项功能，有更紧急的事要处理，但又不想产生一次提交。可用来暂存当前正在进行的工作。 比如想pull 最新代码， 又不想加新commit， 或者另外一种情况，为了fix 一个紧急的bug,  先stash, 使返回到自己上一个commit, 改完bug之后再stash pop, 继续原来的工作。需要注意的是，保护的现场仅用于本地的操作，并不能推送到远端，所以不能作为工作场所改变，需要将代码推同步到另一主机的应用场景。



## 保护现场
```
$ git stash [ save "标识名"]
```
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







4,git show stash@{0} # see the last stash

4.如果在一个分支上想要恢复某一个工作现场怎么办：先用git stash list查看stash队列。确定要 恢复哪个工作现场 到当前分支。然后用git stash pop stash@{num}。num 就是你要恢复的工作现场的编号。

5.如果想要清空stash队列则使用git stash clear。

6.同时注意使用git stash pop命令是恢复stash队列中的stash@{0}即最上层的那个工作现场。而且使用pop命令恢复的工作现场，其对应的stash 在队列中删除。
*  使用git stash apply stash@{num}方法 除了不在stash队列删除外其他和git stash pop 完全一样。
*  在该分支上使用git stash pop/apply。然而恢复的现场不是该分支的，原现场并不会删除，而是被回复到当前分支中
git stash --help # for more info
