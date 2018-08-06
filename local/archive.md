# Archive

如果使用压缩工具将工作争取文件归档，一不小心会把版本库（.git目录）或者工作区中被忽略的文件、临时文件包含在其中。git 提供了一个归档命令：git archive，可以对任意提交对应的目录树建立归档。示例如下：

- 基于最新提交建立归档文件 latest.zip
```
$ git archive -o latest.zip HEAD
```

- 只将目录 src 和 doc 建立归档到 partial.tar 中。
```
$ git archive -o partial.tar HEAD src doc
```

- 基于里程碑 v1.0 建立归档，并且为对党这个你哦的文件添加目录前缀1.0
```
$ git archive --format=tar --prefix=1.0/ v1.0 | gzip > foo-1.0.tar.gz
```

  在建立归档时，如果使用书对象 ID 进行归档，则使用当前时间作为归档中文件的修改时间；而如果使用提交 ID 或里程碑等，测使用提交建立的时间作为归档文件的修改时间。。

  如果使用 tar 格式建立归档，并且使用提交 ID 或里程碑 ID，还会把提交 ID 记录在归档文件的文件头中。记录在文件头中的提交 ID 可以通过 git tar-commin-id 命令获取。

  如果希望在建立文档是忽略某些文件或目录，可以通过响应文件或目录建立`export-ignore`属性加以实现。

  
