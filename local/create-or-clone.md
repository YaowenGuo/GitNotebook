# Create a new Repository

[TOC]

> 你应该了解真相，真相会使你自由－Linus Torvalds


##配置

在开始git之旅前，需要先设置一下git的配置变量。这些配置会保存到全局文件(~/.gitconfig)或系统文件(/etc/gitconfig）中做永久记录。

### 提价着信息



告诉git当前用户的姓名和邮箱，配置的用户名和邮箱将在版本库提交时用到，不要把写其他人的邮箱，否则你到劳动成果会编程别人的。

```

git config --global user.name "用户名"

git config --global user.emal 邮箱地址



取消配置, 如果取消了用户名进行提交，git将推测，使用最后一次提交的用户信息，作为提交者。

git config --unset --global user.name

当不指定值时，可以查看配置

git config user.name

```

如果已经设置了用户信息，然后取消了。再次提交，git 会对提交者进行猜测，使用最后一次提交的用户信息作为提交人。




注意--global参数的几个替换者的区别, -e参数将使用记事本进行打开，否则要写相应的配置的参数。

> git config 有三个级别

-e 将打开./git/config 文件进行修改。 只对该仓库有效

-e --global　编辑/home/用户名/.gitconfig，版本库级配置文件。对该用户下的所有项目有效

--e -system 编辑/etc/gitconfig 系统级配置文件。全局配置文件，对该主机上的所有用户都有效。

优先级覆盖，作用范围不同。git配置文件使用INI格式进行保存。

git config <section>.<key>读取INI文件中某节中键的值。

git config <section>.<key> 更改键的值





### 设置一些git别名，以便可以使用更简洁的命令。系统级配置文件

```
# 如果拥有管理员权限，并且希望设置的别名能够被所有的用户使用，可以使用如下的格式

sudo git config --system alias.st status  # 将st作为status的别名，此时git　status 与git st等效。

#只在本用户的全局配置别名

git config --global alias.st status

#别名还可以包含命令参数，这在团队协作时自动添加作者的姓名和邮件地址标签太重要了。

$git config --global alias.ci "commit -s"

```

### 在git命令输出中开启颜色显示

```

git　config --global color.ui true

```

##  创建仓库
git 使用一个目录作为仓库，有两种方式初始化一个仓库

1. 在要作为仓库的目录下执行 `git init`, 这会把当前目录作为一个仓库。

```

$mkdir demo    ＃

$cd　demo    ＃作为git版本库的测试　

$git init    #进行版本的的初始化　或者在~/gitspace中直接对demo初始化　git init demo

Initialized empty Git repository in /home/bool/gitspace/demo/.git/
```

从上面的初始化输出中可以看到，git　init命令在工作区创建了隐藏目录.git，这个隐藏的目录就是bit版本库，（又叫仓库，repository)。这种方式非常适合于已有的项目，因为这时项目目录已经存在了。

2. 在 `git init` 后跟要创建的仓库名

这种方式是和从 0 创建的目录，这时 git 会新建一个 `demo` 的文件夹作为仓库名。省去了常见目录的步骤。

```
git init demo

```


> 如何知道版本库的位置呢？

1.在工作区中建立目录a/b/c，进入到该目录中

$cd ~/gitspace/demo

$mkdir -p a/b/c

$cd a/b/c

2.显示.git版本库所在的位置。

$ git rev-parse --git-dir

/home/bool/gitspace/demo/.git

３.显示工作区根目录

$ git rev-parse --show-toplevel

/home/bool/gitspace/demo

4.相对于工作区根目录的相对子目录

$ git rev-parse --show-prefix

a/b/c/

5.显示从当前目录(cd)后退(up)到工作区根的深度

$ git rev-parse --show-cdup

../../../




## 思考

> 为什么工作区目录下有一个 .git 目录

这是 git 的版本记录仓库。历史信息都存储在这个地方。

* 版本库和工作去放到一起有被删除的危险，但分开放又会引入同步追踪的问题。可以建立克隆版本库解决问题，或者克隆到网上，团队协作每个人都有一份，根加不用担心安全问题。*

> git 配置

git 使用的是 INI 格式的文件格式。可以打开 .git/config 文件查看内容。

```
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
	ignorecase = true
	precomposeunicode = true
```
想要查看和更改 INI 配置文案的内容非常简单。只需要使用 git config 指令。

> 添加或更改

如果想要更改或设置 INI 文件中的某个属性，命令的格式是：git config <section>[.<section> ...].<key>。可以进行如下操作。

$ git config a.b something
$ git config x.y.z others

如果打开 `.git/config` 文件，会看到如下的内容：
```
[a]
	b = 哈哈哈
[x "y"]
	z = 这样
```

git config 不仅可以方便地操作 git 自己的 INI 文件，还可以操作任何其他的 INI 文件。

- 向配置文件 text.ini 中添加配置

$ GIT_CONFIG=test.ini git config a.b.c.d 'Hello, word!'

- 从 text.ini 中读取配置

$ GIT_CONFIG=test.ini git config a.b.c.d

> 查看

git config a.b.c.d

> 删除

git config --unset --global user.Name
git config --unset --global user.email
