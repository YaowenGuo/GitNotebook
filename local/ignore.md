Git忽略规则和.gitignore规则不生效的解决办法
Git忽略规则：

在git中如果想忽略掉某个文件，不让这个文件提交到版本库中，可以使用修改根目录中

.gitignore 文件的方法（如果没有这个文件，则需自己手工建立此文件）。这个文件每一

行保存了一个匹配的规则例如：

# 此为注释 – 将被 Git 忽略

*.sample 　　      # 忽略所有 .sample 结尾的文件
!lib.sample 　　   # 但 lib.sample 除外
/TODO 　　    # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/ 　　   # 忽略 build/ 目录下的所有文件
doc/*.txt 　　# 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt



对某个文件取消跟踪，这种方式用于已经add了，但不想cimmit，可以删除添加。

git rm --cached readme1.txt    删除readme1.txt的跟踪，并保留在本地。

git rm --f readme1.txt    删除readme1.txt的跟踪，并且删除本地文件。



然后git commit 即可。但是git status查看状态时还是会列出来



.gitignore规则不生效的解决办法

把某些目录或文件加入忽略规则，按照上述方法定义后发现并未生效，原因是.gitignore只能

忽略那些原来没有被追踪的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是

无效的。那么解决方法就是先把本地缓存删除（改变成未被追踪状态），然后再提交：

git rm -r --cached .
git add .
git commit -m 'update .gitignore'
