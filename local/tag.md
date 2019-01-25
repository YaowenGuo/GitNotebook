# tag 重要节点标签

原文链接：http://caibaojian.com/github-create-tag.html

[TOC]

我们可以创建一个tag来指向软件开发中的一个关键时期，比如版本号更新的时候可以建一个“v2.0”、“v3.1”之类的标签，这样在以后回顾的时候会比较方便。tag的使用很简单，主要操作有：查看tag、创建tag、验证tag以及共享tag。

## 查看tag
列出所有tag：

git tag

这样列出的tag是按字母排序的，和创建时间没关系。如果只是想查看某些tag的话，可以加限定：

git tag -l v1.*

这样就只会列出1.几的版本。

## 创建tag
创建轻量级tag：

git tag v1.0

这样创建的tag没有附带其他信息，与之相应的是带信息的tag：

git tag -a v1.0 -m 'first version'

-m 后面带的就是注释信息，这样在日后查看的时候会很有用，这种是普通tag，还有一种有签名的tag：

git tag -s v1.0-m 'first version'

前提是你有GPG私钥，把上面的a换成s就行了。除了可以为当前的进度添加tag，我们还可以为以前的commit添加tag：
```
#首先查看以前的commit
git log --oneline
#假如有这样一个commit：8a5cbc2 updated readme
#这样为他添加tag
git tag -a v1.18a5cbc2
```

里程碑无非也是一个引用，通过记录提交 ID（或者创建 Tag 对象）来为当前版本库的状态进行“留影”。
```
$ls .git/xefs/tags/<tag_name>

.git/refs/tags/<tag_name>


$ git rev-parse refs/tags/<tag_name>

41bd4e2cce0f8baa9bb4cdda62927b408c846cd6
```

## 删除tag
很简单，知道tag名称后：

git tag -d v1.0

$ git push origin --delete <branchName>
删除tag这么用：
$ git push origin --delete tag <tagname>

## 验证tag
如果你有GPG私钥的话就可以验证tag：

git tag -v v1.0

git push origin :v1.1   

//也可以这样  

git push origin --delete tag V1.1  

## 共享tag
我们在执行git push的时候，tag是不会上传到服务器的，比如现在的github，创建tag后git push，在github网页上是看不到tag的，为了共享这些tag，你必须这样：

git push origin --tags

git push origin v1.1


## 描述历史

留过影之后，可以执行 git describe 命令将最新提交显示为一个易记的名称。显示的时候会选取离该提交最近的里程碑作为“基础版本号”，后面附加标识距离“基础版本”的数字，以及该提交的 SHAI 哈希值缩写。因为最新的提交上恰好被打了一个“里程碑”，所以直接显示“里程碑”的名称。这个技术会在后面的示例代码中多次用到。
```
$ git describe
```
输出的格式是
```
<tag_name>-<commit_times_after_this_tag>-<current_commit_id>
```

如果打完 tag 立即输出，将只会显示 tag 的名字。


> 想要在查看 log 时,同时输出 tag 信息，可以在Log中增加 `--decoreate` 参数。该参数还会输出分支信息。
