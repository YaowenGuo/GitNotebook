# 子模组

在项目变得大而且多之后，一些功能的功能可以抽取成模块公用。这些模块代码为了多个项目公用一套代码，但是因为频繁修改、又不想打成包的形式。就可以将该模块单独作为一个仓库，而原有的项目接口一点不用动。

## 

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
