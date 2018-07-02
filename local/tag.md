原文链接：http://caibaojian.com/github-create-tag.html

tag
我们可以创建一个tag来指向软件开发中的一个关键时期，比如版本号更新的时候可以建一个“v2.0”、“v3.1”之类的标签，这样在以后回顾的时候会比较方便。tag的使用很简单，主要操作有：查看tag、创建tag、验证tag以及共享tag。·

1查看tag
列出所有tag：

git tag

这样列出的tag是按字母排序的，和创建时间没关系。如果只是想查看某些tag的话，可以加限定：

git tag -l v1.*

这样就只会列出1.几的版本。

2创建tag
创建轻量级tag：

git tag v1.0

这样创建的tag没有附带其他信息，与之相应的是带信息的tag：

git tag -a v1.0 -m 'first version'

-m后面带的就是注释信息，这样在日后查看的时候会很有用，这种是普通tag，还有一种有签名的tag：

git tag -s v1.0-m 'first version'

前提是你有GPG私钥，把上面的a换成s就行了。除了可以为当前的进度添加tag，我们还可以为以前的commit添加tag：

#首先查看以前的commit
git log --oneline
#假如有这样一个commit：8a5cbc2 updated readme
#这样为他添加tag
git tag -a v1.18a5cbc2
3删除tag
很简单，知道tag名称后：

git tag -d v1.0

4验证tag
如果你有GPG私钥的话就可以验证tag：

git tag -v v1.0

git push origin :v1.1   

//也可以这样  

git push origin --delete tag V1.1  

5共享tag
我们在执行git push的时候，tag是不会上传到服务器的，比如现在的github，创建tag后git push，在github网页上是看不到tag的，为了共享这些tag，你必须这样：

git push origin --tags

git push origin v1.1
