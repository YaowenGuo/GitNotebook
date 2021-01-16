# History


## 查看提交(log)

```shell
git log
```

也可以直接在指令中添加参数，来直接搜索。输入 `/` 文件名可以在 log 信息里搜索相关提交点。

git log --stat

--stat 参数查看每次提交的文件变更统计信息。

--amend 是对刚刚提交的提交进行修补，这样就可以改正前面的提交中错误的用户名和邮件地址，而不用产生另外的新提交。
--allow-empty 使得空提交被允许，之所以这里需要使用，是因为修补的是一个空白提交。
--reset-author 将 Author 的 ID 同步修改，否则只会影响提交者的 ID，使用此参数也会重置 AuthorDate 信息。


如果只想显示提交信息的一行描述。可以使用
git log --pretty=short

只显示指定目录文件的日志
git log --pretty=oneline #用一行显示日志输出

可以使用 --oneline 显示更加精简的输出。
```
# git log --pertty=oneline

683ec75933cfdcde1642376b6197612b2b7b7db0 (origin/master, origin/fen_zu_tao_lun) Update libengine.so for fix no sound.
53dd30c26636ff32e47a68390b5a9fd7b7c60786 Sync truman engine.
04483a47d0ab7b6d21f2ca606205fbf3cd5a9c3e Sync truman.
091f2b4f10a1da29e28569599adb1df5e7e95c62 Add interview mute remote function.
f806688054954efe414e20736d1f255dfdad6f1a Update libengine.so.
...
```

### 也可以查看范围

git log <rev1>...<rev2>

当不使用任何表示范围的参数是，相当于使用了默认的参数 HEAD，即显示当前HEAD 能够访问到的所有历史提交。可以是 tag 或 md5 哈希值，例如使用 tag 值。

```
$ git log --oneline F^! D
```

### 指定数量的提交log

可以使用 -<n> 参数（n 为数字），显示最近的 n 条日志。例如

```
$ git log -3 --pretty=oneline
```
### 显示文件的提交历史

git log <文件或目录名>

```
git log --pertty=oneline src/main/jni/live-engine/prebuilt/libengine.so
```

用于查看过去所有的提交记录，包括什么人在什么时候进行了提交或合并，以及操作前后有怎样的差别。

### 显示文件的改动

-p 参数可以在显示日志的时候显示具体的改动
```
$ git log -p -1
```

 默认情况下，git 不显示二进制文件的改动内容。实际上 git 的文件差异比较提供对二进制文件的支持。

### 显示文件改动的概要

使用 -p 参数会让日志输出显得非常冗余，当不需要知道具体的改动而只想知道改动在哪些文件上时，可以使用 --stat 参数。输出变得更概要，像极了 GNU 的 diffstat 命名的输出。

```
$ git log --stat --oneline I..C

0cd7f2e (tag: C) commit C.
 README    | 1 +
 doc/C.txt | 1 +
 2 files changed, 2 insertions(+)
beb30ca (tag: F) Commit F: merge I with J
3252fcc (tag: J) commit J.
 README           |  7 +++++++
 doc/J.txt        |  1 +
 src/.gitignore   |  3 +++
 src/Makefile     | 27 +++++++++++++++++++++++++++
 src/main.c       | 10 ++++++++++
 src/version.h.in |  6 ++++++
 6 files changed, 54 insertions(+)
```

### 定制输出

git 的日志输出命令提供很多输出模板选择，可以根据需要选择荣冗余显示或精简显示。参数 --pretty=row 显示commit的原始数据，可以显示提交对应的树 ID.

```
$ git log --pretty=row -1
```

参数 --pretty=fuller 会同时显示作者和提交者，两者可以不同。

```
$ git log --pretty=fuller -1
```

参数 --pretty=oneline 显示会提供最精简的日志输出。也可以使用 --oneline 参数，效果近似。

### 某次提交

如果只想查看和分析某次提交，也可以使用 git show 或git cat-file 命令。使用 git show 显示里程碑 D 及其他提交：
```
$ git show D --stat

tag D
Tagger: Jiang Xin <jiangxin@ossxp.com>
Date:   Thu Dec 9 14:24:52 2010 +0800

create node D

commit 212efce1548795a1edb08e3708a50989fcd73cce (tag: D)
Merge: e80aa74 2ab52ad
Author: Jiang Xin <jiangxin@ossxp.com>
Date:   Thu Dec 9 14:06:34 2010 +0800

    Commit D: merge G with H

    Signed-off-by: Jiang Xin <jiangxin@ossxp.com>

 README    | 2 ++
 doc/D.txt | 1 +
 doc/H.txt | 1 +
 3 files changed, 4 insertions(+)
```

使用 cat-file 显示里程碑 D 及其提交。参数 -p  的含义是美观的输出（pretty）。

```
git cat-file -p D^0
tree 1c22e90c6bf150ee1cde6cefb476abbb921f491f
parent e80aa7481beda65ae00e35afc4bc4b171f9b0ebf
parent 2ab52ad2a30570109e71b56fa1780f0442059b3c
author Jiang Xin <jiangxin@ossxp.com> 1291874794 +0800
committer Jiang Xin <jiangxin@ossxp.com> 1291875877 +0800

Commit D: merge G with H

Signed-off-by: Jiang Xin <jiangxin@ossxp.com>
```
