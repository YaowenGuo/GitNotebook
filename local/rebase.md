# rebase 变基操作

变基操作使用的不多，然而其功能却很强大。

## rebase黄金法则

> 法则：
绝不要在公共的分支上使用它！

## rebase


```
$ git rebase        <newbase>         [<till>]
$ git rebase --onto <newbase> <since> [<till>]
$ git rebase -i ...

$ git rebase --continue # 解决冲突后继续执行变基
$ git rebase --skip # 冲突时跳过当前提交继续执行
$ git rebase --abort # 放弃变基操作，回复变基之前的状态
```

## 合并分支
```
$ git rebase        <newbase>         [<till>]
```
rebase 最常用的操作就是讲提交重新放置在另一个分支上

Assume the following history exists and the current branch is "topic":
```
      A---B---C topic
     /
D---E---F---G master
```

From this point, the result of either of the following commands:
```
git rebase master
git rebase master topic
```
would be:
```
              A'--B'--C' topic
             /
D---E---F---G master
```
结束点省略时，默认为当前分支 HEAD 指向的节点。 `master` 和 `topic` 其实只是两个指向节点的指针，可以用任何表示 `master`, `topic` 节点的方法执行 `rebase`。

看起来 `rebase` 会把当前分支重新放置到 `mater` 分支之上。也有很多文档指出，可以将其看作是，先执行了 `git checkout master`，然后将新的提交（这里是 A、B、C）放置到新的 topic 分支上。然而我却认为这是一种误导。根据各种表现记忆不同的规则是难的。我想说明实质的问题。

从显示的结果来看这是两个分支，然而所谓的分支只是指向节点的指针。你可以把它看做是这个节点的别名。这时候我们看到的起始是一个树形的提交节点。

```
      A---B---C
     /
D---E---F---G
```

rebase 起始是将 `<till>` 之前的所有节点合并到 `<newbase>` 之上。为什么说合并，而不是放置？这是因为 git 在变基时，有检测重复的能力。例如

If the upstream branch already contains a change you have made (e.g., because you mailed a patch which was applied upstream), then that commit will be skipped. For example, running git rebase master on the following history (in which A' and A introduce the same set of changes, but have different committer information):
```
      A---B---C topic
     /
D---E---A'---F master
```
will result in:
```
               B'---C' topic
              /
D---E---A'---F master
```

## 重放提交

```
$ git rebase --onto <newbase> <since> [<till>]
```

`--onto` 给出了 rebase 更明确的指示，他能将指定 `<since>` 到 `<till>` 之间的提交节点 明确放置到 `<newbase>` 之上。

### 重放
```
      A---B---C topic
     /
D---E---F article
```

当我们想要将 topic 上的 B，C 放置到 F 之上时，可以执行
```
$ git rebase --onto article topic^^ topic
$ git rebase --onto F       B^      C
```
**since 到 till 是不包括since 的所有提交节点。**

```
      A---B---C topic
     /
D---E---F---B'--C' article
```

**提交试用了原有的内容和提交信息，但却不再是原有的提交了**


Here is how you would transplant a topic branch based on one branch to another, to pretend that you forked the topic branch from the latter branch, using rebase --onto.

First let’s assume your topic is based on branch next. For example, a feature developed in topic depends on some functionality which is found in next.
```
o---o---o---o---o  master
     \
      o---o---o---o---o  next
                       \
                        o---o---o  topic
```
We want to make topic forked from branch master; for example, because the functionality on which topic depends was merged into the more stable master branch. We want our tree to look like this:
```
o---o---o---o---o  master
    |            \
    |             o'--o'--o'  topic
     \
      o---o---o---o---o  next
```
We can get this using the following command:

git rebase --onto master next topic
Another example of --onto option is to rebase part of a branch. If we have the following situation:
```
                        H---I---J topicB
                       /
              E---F---G  topicA
             /
A---B---C---D  master
```
then the command
```
git rebase --onto master topicA topicB
```
would result in:
```
             H'--I'--J'  topicB
            /
            | E---F---G  topicA
            |/
A---B---C---D  master
```
This is useful when topicB does not depend on topicA.

A range of commits could also be removed with rebase. If we have the following situation:
```
E---F---G---H---I---J  topicA
```
then the command

git rebase --onto topicA~5 topicA~3 topicA
would result in the removal of commits F and G:
```
E---H'---I'---J'  topicA
```

### 冲突解决

This is useful if F and G were flawed in some way, or should not be part of topicA. Note that the argument to --onto and the <upstream> parameter can be any valid commit-ish.

In case of conflict, git rebase will stop at the first problematic commit and leave conflict markers in the tree. You can use git diff to locate the markers (<<<<<<) and make edits to resolve the conflict. For each file you edit, you need to tell Git that the conflict has been resolved, typically this would be done with
```
git add <filename>
```
After resolving the conflict manually and updating the index with the desired resolution, you can continue the rebasing process with
```
git rebase --continue
```
Alternatively, you can undo the git rebase with
```
git rebase --abort
```

> rebase 的执行过程

1. 首先会执行 `git checkout <till>` 切换到 `<till>`。
因为会切换到 `<till>`，因此如果 `<till>` 指向的不是一个分支，则变基是在 detached HEAD(分离头指针)状态下执行的，变基结束后，还要执行分支重置到当前节点。
2. 将<since>.. <till>所标识的提交范围写到一个临时文件中。

<since>.. <till>是指包括<since>到<till>但排除<since>后形成的版本范围。

3. 将当前分支强制重置（git reset --hard）到<newbase>。

相当于执行：git reset --hard  <newbase>.

4. 从保存在临时文件中的提交列表中，将提交逐一按顺序重新提交到重置之后的分支上。

5. 如果遇到提交已经在分支中包含，则跳过该提交。

6. 如果在提交过程遇到冲突，则变基过程暂停。用户解决冲突后，执行 git rebase --cont inue 继续变基操作。或者执行 git rebase --skip 跳过此提交。或者执行 git rebase --abort 就此终止变基操作切换到变基前的分支上。

## 交互式变基

当使用 -i 参数时，会进入交互式交互界面。虽然操作变成了手动，但却充满了灵活的操作。执行交互式变基操作，会将 `<since>` 到 `<till>` 的提交悉数罗列在一个文件中，然后打开一个编辑器来编辑这个文件。可以通过修改文件的内容设定变基操作，实现删除提交、将多个提交压缩为一个提交、更改提交顺序，以及更改历史提交的提交说明等。
```
$ git rebase -i <tag/SHA_id>
```


- 开头没有被注释掉的，就是节点之后的按提交顺序的所有提交。

- 行首的 pick 是在退出该编辑时要执行的动作，pick 表示应用此提交。

- 参考文件中的注释，可以通过修改动作名称，在变基的时候执行特定操作。

- 动作 reword，或者简写为 r。在变基时会应用此提交，但是在提交的时候允许用户修改提交说明。这个功能在 Git 1.6.6 之后开始提供，对于修改历史提交的提交说明非常方便。对于老版本的 Git 没有 reword 动作，可以使用 edit 动作。

- 动作 edit，或者简写为 e。也会在变基时应用此提交，但是会在应用后暂停变基，提示用户使用 git commit --amend 执行提交，以便对提交进行修补。当用户执行 git commit --amend 完成提交后，还需要执行 git rebase --continue 继续变基操作。用户在变基暂停状态下可以执行多次提交，从而实现把一个提交分解为多个提交。edit 动作非常强大，对于老版本的 Git 没有 reword 动作，可以使用 edit 动作实现相同的效果。

- 动作 squash，或者简写为 s。该提交会与前面的提交压缩为一个。

- 动作 fixup，或者简写为 f。类似动作 squash，但是此提交的提交说明被丢弃。这个功能在 Git 1.7.0 之后开始提供，老版本的 Git 还是使用 squash 动作吧。

- 可以通过修改变基任务文件中各个提交的先后顺序，进而改变最终变基后提交的先后顺序。
- 可以修改变基任务文件，删除包含相应提交的行，这样该提交就不会被应用，进而在变基后的提交中被删除。

有了对交互式变基命令的理解，就可以开始新的“回到未来”之旅了。

确认舞台已经布置完毕。
```
$ git status -8 -b

##master

$ git log --oneline --decorate -6
```


### 待归类
```
$ git rebase -i origin/master
```
这个命令会执行交互式rebase操作，操作对象是那些自最后一次从origin仓库拉取或者向origin推送之后的所有提交。
若想查看一下将被rebase的提交，可以用如下的log命令：
```
$ git log github/master..
```
一旦运行了'rebase -i'命令，你所预设的编辑器会被调用，其中含有如下的内容：
```
pick fc62e55 added file_size
pick 9824bf4 fixed little thing
pick 21d80a5 added number to log
pick 76b9da6 added the apply command
pick c264051 Revert "added file_size" - not implemented correctly

# Rebase f408319..b04dc3d onto f408319
#
# Commands:
#  p, pick = use commit
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#
# If you remove a line here THAT COMMIT WILL BE LOST.
# However, if you remove everything, the rebase will be aborted.
#
```
这些信息表示从你上一次推送操作起有5个提交。每个提交都用一行来表示，行格式如下：
(action) (partial-sha) (short commit message)
现在你可以将操作（action）改为'edit'（使用提交，但是暂停以便进行修正）或者'squash'（使用提交，但是把它与前一提交合并），默认是'pick'（使用提交）。你可以对这些行上下移动从而对提交进行重排序。当你退出编辑器时，git会按照你指定的顺序去应用提交，并且做出相应的操作（action）。
二、pick操作
如果指定进行'pick'操作，git会应用这个补丁，以同样的提交信息（commit message）保存提交。
三、squash操作
如果指定进行'squash'操作，git会把这个提交和前一个提交合并成为一个新的提交。这会再次调用编辑器，你在里面合并这两个提交的提交信息。所以，如果你（在上一步）以如下的内容离开编辑器：
pick   fc62e55 added file_size
squash 9824bf4 fixed little thing
squash 21d80a5 added number to log
squash 76b9da6 added the apply command
squash c264051 Revert "added file_size" - not implemented correctly
你必须基于以下的提交信息创建一个新的提交信息：
```
# This is a combination of 5 commits.
# The first commit's message is:
added file_size
# This is the 2nd commit message:
fixed little thing
# This is the 3rd commit message:
added number to log
# This is the 4th commit message:
added the apply command
# This is the 5th commit message:
Revert "added file_size" - not implemented correctly
This reverts commit fc62e5543b195f18391886b9f663d5a7eca38e84.
```
一旦你完成对提交信息的编辑并且退出编辑器，这个新的提交及提交信息会被保存起来。
四、edit操作
如果指定进行'edit'操作，git会完成同样的工作，但是在对下一提交进行操作之前，它会返回到命令行让你对提交进行修正，或者对提交内容进行修改。
例如你想要分割一个提交，你需要对那个提交指定'edit'操作：
pick   fc62e55 added file_size
pick   9824bf4 fixed little thing
edit   21d80a5 added number to log
pick   76b9da6 added the apply command
pick   c264051 Revert "added file_size" - not implemented correctly
你会进入到命令行，重置（reset）该提交，然后创建两个（或者更多个）新提交。假设提交21d80a5修改了两个文件，file1和file2，你想把这两个修改放到不同的提交里。你可以在进入命令行之后进行如下的操作：
```
$ git reset HEAD^
$ git add file1
$ git commit 'first part of split commit'
$ git add file2
$ git commit 'second part of split commit'
$ git rebase --continue
```
现在你有6个提交了，而不是5个。
五、丢弃提交操作
交互式rebase的最后一个作用是丢弃提交。如果把一行删除而不是指定'pick'、'squash'和‘edit''中的任何一个，git会从历史中移除该提交。
