# diff

git diff 用于比较文件的修改。

```
git diff [options] [<commit>] [--] [<path>...]
git diff [options] --cached [<commit>] [--] [<path>...]
git diff [options] <commit> <commit> [--] [<path>...]
git diff [options] <blob> <blob>
git diff [options] [--no-index] [--] <path> <path>
```

前两个类似
```
git diff [options] [<commit>] [--] [<path>...]
git diff [options] --cached [<commit>] [--] [<path>...]
```

- --chched 指定工作区还是缓存区，缺失默认工作区
- commit 缺失默认比较最后一个节点。
- 不指定文件，默认比较所有文件。

例如：

比较工作区和缓存区的修改

```
git diff [<filename>]
```

比较缓存区和上一个提交的修改, 只需添加 `--cached` 参数
```
git diff --cached [<filename>]
```

比较工作区和历史某节点的修改

```
git diff <node> [<filename>] 
```




