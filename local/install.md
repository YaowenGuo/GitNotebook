Ubuntu10.10或更新

```

sudo aptitude install git

sudo aptitude install git-doc git-svn git-email git-gui gitk

git软件包包含了大部分git命令，是必须安装的软件。

 git-svn git-email git-gui gitk本来也是git软件包的一部分，但是因为有着不一样的软件包依赖(如更多的perl模组和tk等)，所以单独作为软件包发布。

git-doc包含了git的html文档，可以选择安装，如果安装了，就可以通过执行git help -w <sub-commend>来自动用web浏览器打开相关字命令的html文档。

```

RHEL，Fedora,CentOS

```

yum install git

yum install git-svn git-email git-gui gitk

```

从源代码进行安装

```

方位ｇｉｔ的官网，下载ｇｉｔ源码包，如：git-1.7.4.1.tar.bz2。

＃解压缩，并进入相应目录

tar -jxvf git-1.7.4.1.tar.bz2

cd git-1.7.4.1/

#安装方法在INSTALL文件中，参照其中的指导即可安装。下面将git 安装到/usr/local/bin中。

make prefix=/usr/local all

sudo make prefix=/usr/local install

#安装Git文档（可选），实际上，即使不安装Git文档也可以使用man手册查看git帮助，使用命令git help <sub-commend>或git <sub-commend> --help即可。

#编译文档依赖asciidoc,因此，需要先安装asciidoc，然后编译文档。

make prefix=/usr/local doc info

sudo  make profix=/usr/local install-doc install-html install-info

＃ 安装完成后。就可以在/us/local/bin下找到git命令

```

从git版本库进行安装

```

如果在本地克隆一个GIT项目的版本库，就可以用版本库同步的方式获取最新版本的git,这样在下载不同版本源代码时实际上采用了增量方式。非常节省时间可空间，当然使用这种方式安装前提是已经安装了其他版本的git

#克隆git项目的版本库到本地

git clone git://git.kernel.org/pub/scm/git/git.git

#如果已经克隆了一个git项目版本库，直接在工作区中更新，以获得最新版本的git.

git getch

#执行清理工作，避免前一次编译的遗留文件对编译造成影响。注意，下面的操作将丢弃本地对git代码的改动。

git clean -fdx

git reset --hard

#查看git的里程碑，选择最新版本进行安装。例如v1.7.4.1。

$git tag

...

v1.7.4.1

#检出该版本的的代码。

git checkout v1.7.4.1

#执行安装，例如，安装到/usr/local目录下。

make prefix=/usr/local all doc info

sudo make prefix=/usr/local install install-doc install-html install-info

```

###命令补全

linux的shell环境（bash）通过bash-completion软件包提供命令补全功能。在录入命令参数时按一次或两次TAB键可实现参数的自动补全提示。

如果通过包管理工具安装git，一般都已经为git配置好了自动补全，但如果时以编译源码的方式安装git就需要做一些配置。

```

#将git源码包中的命令自动补全脚本复制到bash-completion对应的目录中。

cp contrib/completion/git-completion.bash  /etc/bash_completion.d/

#重新加载自动补齐脚本，使之在当前的shell中生效。

. /etc/bash_completion

#为了能够在终端开启时自动加载hasn_completion脚本，需要在系统配置文件/etc/profile及本地配置文件~/.bashrc中添加下面的内容。

if [ -f /etc/bash_completion ]; then

    . /etc/hash_completion

fi

```

###中文支持

git的本地化做的并不好。命令的输出和命令的帮助还只能输出英文。也许在未来的版本中会使用gittext实现本地化。

可以在提交说明中使用中文，但要在git中进行配置。

文件名或目录即引用，以及分支或里程碑使用中文，只有在只用UTF-8的环境（Linux,Mac OS X,Windows下的Cygwin)里再可以使用。

但是在默认状态下，中文文件名在工作区状态输出、查看历史更改概要，以及在补丁文件中，文件名中的中文不能正确的显示，而是显示为八进制的字符编码。

通过设置git环境变量core.quotepath设置为false，接可以解决这些中文文件名在这些git命令输出中的显示问题。

```

git config --global core.quotepath false

#测试一下

git status -s

```

如果使用的时GBK字符集。如zh_CH.GBK字符集编码。就要另外做些工作了。

```

#将显示提交说明所使用的字符集设置为gbk，这样，使用git log查看提交说名时才能够真确显示其中的中文。

git config --global i18n.logOutputEnconding gbk

#设置提交说明时使用的字符集，以便在commit对象中正确标注字符集。

git在提交时不会对提交说明进行从GBK字符集到UTF－８的转换，但是可以在提交说明中标注所使用的字符集。因此在非UTF-8字符集的平台中录入中文时需要用如下的的命令设置录入提交说明的字符集，以便在commit对象中嵌入正确的编码说明。

git config --global i18n.commitEnconding gbk

```

> 查看版本

git --version
