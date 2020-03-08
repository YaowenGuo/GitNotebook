[TOC]

# 丢弃历史提交

交互式变基操作当然能够完成这样的任务，但是如果历史版本库有成百上千个，将这些操作的 pick 都改成 fixup 是很费事的。git 有更简单的方式完成该操作。

如果想要把 A 之前的历史提交全部清除。


基于 A 对应的提交构建一个跟提交，然后在将 master 分支在里程碑 A 之后的提交变基的新的根提交上，实现对历史提交的清除。

A对应的提交的构造出一个根提交至少有两种方法，
## 方法一： git commit-tree


1. 查看里程碑 A 指向的目录树
用 A^{tree} 语法访问里程碑 A 对应的目录树。
```
$ git cat-file -p A^{tree}
```
2. 使用 git commit-tree 可以直接从该目录树创建提交

```
$ echo "Commit from tree of tag A." | git commit-tree A^{tree}
```
该指令输出一个提交的 SHA1 哈希值。
3. 查看该提交，会发现这个提交没有历史提交。
```
$ git log --pretty=raw 提交ID
```

## 方法二：git hash-object

1. 查看里程碑 A 指向的提交。用 A^0 语法访问里程碑 A 对应的提交

```
$ git cat-file commit A^0
```

2. 将上面的输出（里程碑 A 指向的提交）过路掉以 parent 开头的行，并将结果保存到一个文件中。
```
$ git cat-file commit A^0 | sed -e '/^parent/ d' > tmpfile
```

3. 执行 git hash-object 命令，将文件 tmp 作为一个commit commit 对象写入对象库。
```
$ git hash-object -t commit -b -- tmpfile
```
4. 上面的结果就是 至此那个提交的对象 ID. 可以查看以验证该提交确实没有父提交。
```
$ git log --pretty=raw 提交ID
```


无论哪种方法创建了新的根提交，都可以使用变基操作，将 master 分支在里程碑 A 之后的提交变基到新的根提交上。

1. 执行变基，将 master 分支里程碑 A 之后的提交全部迁移到新的根提交之上

```
$ git rebase --onto <根提交ID> A master
```



	Slug	Description	Product count
Save shipping classesAdd shipping class
EUB特货
Edit | Remove
eub-special
1
E特快
Edit | Remove
ems-express
1
E邮宝
Edit | Remove
ems-normal
2
美国专线
Edit | Remove
us-special



Intelligent Voice & Smart Vibrating Sucking Male Masturbator Cup (Intelligent Voice & Smart Vibrating Sucking Male Masturbator Cup)