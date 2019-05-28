# Ignore file

[TOC]

## Git忽略规则：

在git中如果想忽略掉某个文件，不让这个文件提交到版本库中，可以使用修改根目录中。实际上 `.gitignore` 可以放在任何子目录中，`.gitignore` 对当前目录和当前目录的子目录有效。

.gitignore 文件的方法（如果没有这个文件，则需自己手工建立此文件）。这个文件每一

行保存了一个匹配的规则例如：
```
# 此为注释 – 将被 Git 忽略

*.sample 　　      # 忽略所有 .sample 结尾的文件
!lib.sample 　　   # 但 lib.sample 除外
/TODO 　　    # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/ 　　   # 忽略 build/ 目录下的所有文件
doc/*.txt 　　# 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

- 忽略路径的根是当前目录，例如'/TODO'是忽略当前目录下的 TOTO 目录下的所有文件，而不是系统根目录。
- 可以使用通配符，参见 Linux 手册: glob(7)。例如：星号（`*`）代表任意多字符，问号代表一个任意字符，方括号内代表可选字符范围。
- 如果名称的最后一个字符是 `/`, 表示要忽略的是整个目录，同名文件不忽略。否则，同名的目录和文件都忽略。
- 通过在名称前加一个感叹号(`!`)，表示不忽略。


对某个文件取消跟踪，这种方式用于已经add了，但不想cimmit，可以删除添加。

git rm --cached readme1.txt    删除readme1.txt的跟踪，并保留在本地。

git rm --f readme1.txt    删除readme1.txt的跟踪，并且删除本地文件。



然后git commit 即可。但是git status查看状态时还是会列出来


> Git忽略规则和.gitignore规则不生效的解决办法

.gitignore规则不生效的解决办法

把某些目录或文件加入忽略规则，按照上述方法定义后发现并未生效，原因是.gitignore只能

忽略那些原来没有被追踪的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是

无效的。那么解决方法就是先把本地缓存删除（改变成未被追踪状态），然后再提交：

git rm -r --cached .
git add .
git commit -m 'update .gitignore'


## 独享式忽略

`.gitignore` 文件默认会被添加到版本库中，这样在别人获取代码时，会一起得到 `.gitignore` 文件。 所以称之为“共享式”忽略，与之对应的忽略“独享”式忽略，独享式的忽略就不会因为因为版本共享给他人。只在本地版本库中生效。独享式忽略有两种。
- 一种是针对具体版本库的忽略，即在版本库 .git 目录下的一个文件 `.git/info/exclude` 来设置文件忽略。
- 另一种是全局的“独享式”忽略。 即通过 Git 的配置变量 core.excludesfile 指定一个忽略文件，其设置的忽略在其作用域有效。


## 忽略已经提交的

忽略已经提交的，需要将文件从跟踪中删除，然后提交一个新的节点。

```
git rm --cached -r .idea
git add .
git commit -m "ignore track file"
```

又时候，想要改变 ignore 文件的整个内容，这时候由于添加的比较多，不知道添加了哪些文件是已经跟踪了的。 无法一一清楚跟踪的文件。可以使用如下步骤，先全部清楚，然后取消非 ignore 的文件的清除操作

```bash
git rm -r --cached . # 由于是目录，需要加 -r
```
这时候，所有的文件都被 delete 掉了

```
git status
```
不确定从新 commit 会不会把新的文件都给提交成自己的文件，所以不能提交，可以取消不是 ignore 的更改。

```
git add .
git checkout .
```

然后将剩下的被删除的文件提交即可

```
git commit -m "ignore file"
```
