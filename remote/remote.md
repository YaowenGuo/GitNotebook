首先在github上构建一个仓储，创建时不要勾选Initialize this respository with a README.md选项。因为一旦勾选该项，github上的仓库就会自动生成README文件，从而边检之初便与本地仓库失去了整合性。虽然也可以强制覆盖，但一般还是要避免这类情况发生。（我的建议是，如果要将一个已经存在的仓库发从到github上，不要勾选。如果是新建立一个项目，可以先在github上建立，并勾选。然后下拉仓库）

git remote add——添加远程仓库
git remote add origin <git@github.com:用户明/仓库明.git>
添加了仓库地址后，git会自动将上述仓库地址的名称设置为origin（标识符）


## 修改仓库地址

如果仓库 url 地址被设置为 `http` 想要修改为 `ssh`。或者仓库地址变了，可以修改地址。

``` shell
git remote set-url origin <地址>

git remote -v # 查看修改结果
```

## remote/HEAD 丢失

```
git remote set-head origin master
```
或者打开 `.git/refs/remotes/origin/HEAD` 修改。