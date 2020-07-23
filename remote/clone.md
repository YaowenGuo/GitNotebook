[TOC]

## clone

要参与任何一个 Git 项目的协作,必须要了解该如何管理远程仓库.远程仓库是指托管在网络上的项目仓库,可能会有好多个,其中有些你只能读,另外有些可以写.同他人协作开发某 个项目时,需要管理这些远程仓库,以便推送或拉取数据,分享各自的工作进展.管理远程仓库的工作,包括添加远程库,移除废弃的远程库,管理各式远程库分 支,定义是否跟踪这些分支,等等.本节我们将详细讨论远程库的管理和使用.

远程操作的第一步，通常是从远程主机克隆一个版本库，这时就要用到git clone命令。
```
$ git clone <repository> [<directory>]
$ git clone --bare <repository> <directory.git>
$ git clone --mirror <repository> <directory.git>
```
- 用法一将 <repository> 指向的版本创建一个克隆到 <directory> 目录。目录 <directory> 相当于克隆版本库的工作区，文件都会检出，版本库位于工作区下的 .git 目录。

- 口用法 2 和用法 3 创建的克隆版本库都不包含工作区，直接就是版本库的内容，这样的版本库称为裸版本库。一般约定俗成裸版本库的目录名以 .git 为后缀，所以上面示例中将克隆出来的裸版本库目录名写作 <directory.git>.

- 用法 3 区别于用法 2 之处在于用法 3 克隆出来的裸版本对上游版本库进行了注册，这样可以在裸版本库中使用 git fetch 命令和上游版本库进行持续同步。
- 用法 3 只在 1.6.0 或更新版本的 Git 中才提供。

Git 的 PUSH 和 PULL 命令的用法相似，使用下面的语法：

git push  [<remote- repos>  [<refspec>]]

git pull  [<remote - repos>  [<refspec>]]

其中方括号的含义是参数可以省略，<remote-repos> 是远程版本库的地址或名称，<refspec> 是引用表达式，暂时理解为引用即可。后面的章节再具体介绍 PUSH 和 PULL 命令的细节。

下面就通过不同的 Git 命令组合，掌握版本库克隆和镜像的技巧。

比如，克隆jQuery的版本库。
```
$ git clone https://github.com/jquery/jquery.git
```

该命令会在本地主机生成一个目录，与远程主机的版本库同名。Git会把"Git URL"里最后一级目录名的'.git'的后辍去掉,做为新克隆(clone)项目的目录名: (例如. git clone http://git.kernel.org/linux/kernel/git/torvalds/linux-2.6.git 会建立一个目录叫'linux-2.6')如果要指定不同的目录名，可以将目录名作为git clone命令的第二个参数。
```
$ git clone <版本库的网址> <本地目录名>
```

git clone支持多种协议，除了HTTP(s)以外，还支持SSH、Git、本地文件协议等，下面是一些例子。
```
$ git clone http[s]://example.com/path/to/repo.git/
$ git clone ssh://example.com/path/to/repo.git/
$ git clone git://example.com/path/to/repo.git/
$ git clone /opt/git/project.git
$ git clone file:///opt/git/project.git
$ git clone ftp[s]://example.com/path/to/repo.git/
$ git clone rsync://example.com/path/to/repo.git/
```

SSH协议还有另一种写法。如果访问一个Git URL需要用法名和密码，可以在Git URL前加上用户名，并在它们之间加上@符合以表示分割.git会提示你输入密码。
```
$ git clone [user@]example.com:path/to/repo.git/
git clone robin.hu@http://www.kernel.org/pub/scm/git/git.git
```
通常来说，Git协议下载速度最快，SSH协议用于需要用户认证的场合。但是有时必须使用http协议,比如你公司的防火墙阻止了你的非http访问请求.各种协议优劣的详细讨论请参考官方文档


另外，我们可以通过-b <name>来指定要克隆的分支名，比如
```
$ git clone -b master2 ../server .
```
表示克隆名为master2的这个分支，如果省略-b <name>表示克隆master分支。

***--git-dir  用于指定操作的仓库路径，而不是当前所在目录。***

## 裸版本库

裸版本库只包含仓库，而不包含工作区，并且，这些内容并没有包含在 `.git` 目录中，而是直接在仓库的目录下。因此，为了区分裸版本库，罗版本库约定以 `.git` 作为后缀。

### 克隆产生裸版本库

```
$ git clone --bare <repository> <directory.git>
$ git clone --mirror <repository> <directory.git>
```

### 创建裸版本库

使用 `--bare` 参数即可

```
git init --bare
```

## 对等工作区

不使用--bare 或--mirror 创建出来的克隆包含工作区，这样就会产生两个包含工作区的版本库。这两个版本库是等价的。 但是默认 clone 出来的版本库能 pull 代码，而原版本库不能向 clone 库推送代码。这是因为在 clone 时保存了原仓库的信息。


之所以不建议推送，是因为两个工作区都能提交代码，而推送会导致版本库、缓存区和工作区的进度不同，难以管理。也可以通过配置允许推送。


## 查看当前的远程库

　　要查看当前配置有哪些远程仓库,可以用 Git remote 命令,它会列出每个远程库的简短名字.在克隆完某个项目后,至少可以看到一个名为 origin 的远程库,Git 默认使用这个名字来标识你所克隆的原始仓库:

```
$ git clone git://github.com/schacon/ticgit.git
```




### 查看远程分支

```
git remote show origin

git remote -v
```

git remote 不带参数，列出已经存在的远程分支

```
git remote
```

git remote -v | --verbose 列出详细信息，在每一个名字后面列出其远程url,此时， -v 选项(译注:此为 –verbose 的简写,取首字母),显示对应的克隆地址:

## push


pull request是用户再修改代码后向对方仓库发送采纳请求的功能，也是github的核心功能。
  git pull命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。它的完整格式稍稍有点复杂。

$ git pull <远程主机名> <远程分支名>:<本地分支名>
比如，取回origin主机的next分支，与本地的master分支合并，需要写成下面这样。

$ git pull origin next:master
如果远程分支是与当前分支合并，则冒号后面的部分可以省略。

$ git pull origin next
上面命令表示，取回origin/next分支，再与当前分支合并。实质上，这等同于先做git fetch，再做git merge。

$ git fetch origin
$ git merge origin/next
在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系(tracking)。比如，在git clone的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的master分支自动”追踪”origin/master分支。

Git也允许手动建立追踪关系。

git branch --set-upstream master origin/next
上面命令指定master分支追踪origin/next分支。

如果当前分支与远程分支存在追踪关系，git pull就可以省略远程分支名。

$ git pull origin

上面命令表示，本地的当前分支自动与对应的origin主机”追踪分支”(remote-tracking branch)进行合并。

如果当前分支只有一个追踪分支，连远程主机名都可以省略。

$ git pull
上面命令表示，当前分支自动与唯一一个追踪分支进行合并。

如果合并需要采用rebase模式，可以使用–rebase选项。

$ git pull --rebase <远程主机名> <远程分支名>:<本地分支名>
