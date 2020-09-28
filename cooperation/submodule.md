# 子模组


## 删除

```
git submodule deinit <asubmodule>    
git rm <asubmodule>
# Note: asubmodule (no trailing slash)
# or, if you want to leave it in your working tree
git rm --cached <asubmodule>
rm -rf .git/modules/<asubmodule>
```

## 查看各个 submodule 的分支。

```shell
git submodule foreach 'git status'
```

You can always make it simpler by assign this to alias:

```shell
git config --global alias.sb "submodule foreach \"git status\""
```
