# File operate

[TOC]

## 删除未添加文件

直接在工作区删除这文件，会如何呢？
```
$ rm *.txt
```
通过下面的命令，可以看到在暂存区（版本库）中的文件仍然存在，并未删除。
```
$ git ls-fles
```

从文件的状态来看，文件只是在本地进行了删除，尚未添加到暂存区（提交任务）中。也就是说：直接在工作区删除，对暂存区和版本库没有任何影响。
```
$ git status

On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
...

```
rm 命令只能删除工作区的文件，对缓存区和版本库中的文件没有影响，这很好理解，rm 是系统软件，就是删除指定的文件，对其他目录下的文件不会进行关联删除。
想要将文件和缓存区的命令一同删除，可以使 git 的内部 rm 自命令。


### 执行 git rm 命令删除 ADD 过的文件

git rm 不能删除未跟踪的文件。还是需要用系统的 rm 指令。

```
$ git rm -- <file>
```

按照上面状态输出的内容，将所有的文本文件删除。执行下面的命令：
```
$ git rm <filename1, filename2, ....>

$ git status

$ git commit -m ...
```
删除动作加入了暂存区。这时执行提交动作，就从真正意义上执行了文件删除。文件只是在最新的提交中被删除了，并没有改变历史版本。可以通过下面的命令查看历史版本中的文件。
```
$ git ls-files --with-tree=HEAD^
```

也可以查看历史版本中文件的内容

```
$ git cat-file -p HEAD^:welcome.txt
```


## 查看文件

### 查看工作区的文件
```
ls
```
### 查看缓存区的文件
```
git ls-files
```


删除本地多余的目录和文件，可以使用 git clean 命令。先来测试运行以便看看哪些文件和目录会被删除，以免造成误删。
```
$ git clean -nd

Would remove a/
```

真正开始强制删除多余的目录和文件。
```
$ git clean -fd

Removing a/
```

整个世界清净了。

$ git status -S


## 文件的恢复
对不想删除的文件执行 git checkout --  <file>，可以让文件在工作区重现。

恢复工作区中的文件，注意会覆盖工作区中的更改，并且没有任何提示，这是很危险的操作，会让修改的内容丢失。除非确认文件的内容更改是无用的，才会执行覆盖操作。

```
git checkout -- <files>
```

或者

```
git cat-file -p HEAD~1:<file>  >  <file>
```

或者使用 show 命名
```
git show HEAD~1:welcome.txt > welcome.txt
```
等同于
```
git checkout HEAD -- welcome.txt
```

## 移动文件

git 拥有强大的文件对比追踪能力，无论是重命名还是删除后从新恢复，都能追踪到，甚至改变了少量文件的内容。甚至在提交时还会显示文件的相似度。
除了系统的 mv 指令外，还有 git 带的 mv 自命令可以用。mv 子命令同样会将缓存区一同更改。

```
git mv <origin_name> <target_name>
```
