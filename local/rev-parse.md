# rev-parse & rev-list

## rev-parse
rev-parse 是 git 的一个底层命令，其功能非常丰富（或者说杂乱），很多 git 脚本或工具都会用到这条命令。

自命令参数

- --git-dir: 获得 git 版本库（.git文件夹）的位置。
- --show-cdup: 当前工作区目录的深度
- --parseopt: 可以被 git 无关的应用用于解析命令行参数。
### 获取信息
> 显示分支

```
$ git rev-parse --symbolic --branches
master
```

> 显示里程碑

```
$ git rev-parse --symbolic --tags
A
B
C
D
E
F
G
H
I

```

> 显示定义的所有引用

```
$ git rev-parse --symbolic --glob=refs/*
```
这个指令在 zsh 下执行有个 bug, 提示 `zsh: no matches found: --glob=refs/*` 通过提示最前面的 `zsh:` 可以确定这个 zsh 的提示，说明该指令被 zsh 处理了。并不是 git 解析有问题。

### 获取 SHA1 哈希值

`rev-parse` 自命令的另一个重要功能就是讲一个git对象表达式表示为对应的 SHA1 哈希值。

> 显示 HEAD 对应的 SHA1 哈希值

```
$ git rev-parse HEAD
```

> git describe 的输出也可以接续为正确的 SHA1 哈希值

```
$ git describe
A-1-g6652a0d
$ git rev-parse A-1-g6652a0d

```

> 可有同时获得多个表达式的 SHA1 哈希值

```
$ git rev-parse master refs/heads/master
```

> 可以用哈希值的前几位只带整个哈希值

```
$ git rev-parse 6652 6652a0dc
```

> 历程碑的两种表示方法均置顶相同的对象

```
$ git rev-parse A refs/tags/A
```

> 里程碑 A 实际指向的是一个 Tag 对象而给提交，用下面的三个表示法可以显示提交本身的哈希值而非里程碑对象的哈希值。还有，下面的语法也可以直接作用于轻量级里程碑（直接指向提交的里程碑），或者作用于提交本身。

```
$ git rev-parse A^{} A^0 A^{commit}
81993234fc12a325d303eccea20f6fd629412712
81993234fc12a325d303eccea20f6fd629412712
81993234fc12a325d303eccea20f6fd629412712
```

> A 的第一个父提交就是 B 所指向的提交。

"^" 符号表示父提交，当一个提交有多个父提交时，可以通过在符号“^”后面跟上一个数字表示第几个父提交。 “A^” 就相当于“A^1”。而 B^0 代表了 B 所指向的一个 Commit 对象（因为 B 是 Tag 对象）
```
$ git rev-parse A^ A^1 B^0
776c5c9da9dcbb7e463c061d965ea47e73853b6e
776c5c9da9dcbb7e463c061d965ea47e73853b6e
776c5c9da9dcbb7e463c061d965ea47e73853b6e
```

> 更为复杂的表示方法

连续的"^"符号一次沿着父提交进行定位值一祖先提交。“^”后面的数字代表改提交的第几个父提交。
```
$ git rev-parse A^^3^2 F^2 J^{}
3252fcce40949a4a622a1ac012cb120d6b340ac8
3252fcce40949a4a622a1ac012cb120d6b340ac8
3252fcce40949a4a622a1ac012cb120d6b340ac8
```

> 记号 `~<n>` 就相当于连续 n 个 “^” 符号。

```
$ git rev-parse A~3 A^^^ G^0
e80aa7481beda65ae00e35afc4bc4b171f9b0ebf
e80aa7481beda65ae00e35afc4bc4b171f9b0ebf
e80aa7481beda65ae00e35afc4bc4b171f9b0ebf
```

> 显示里程碑A对应的目录树，下面的两种写法都可以

```
$ git rev-parse A^{tree} A:
95ab9e7db14ca113d5548dc20a4872950e8e08c0
95ab9e7db14ca113d5548dc20a4872950e8e08c0
```

> 显示树里面的文件，下面两种方法均可

```
$ git rev-parse A^{tree}:src/Makefile A:src/Makefile
96554c5d4590dbde28183e9a6a3199d526eeb925
96554c5d4590dbde28183e9a6a3199d526eeb925
```

> 暂存区里文件和 HEAD 中的文件相同

```
$ git rev-parse :gitg.png HEAD:gitg.png
fc58966ccc1e5af24c2c9746196550241bc01c50
fc58966ccc1e5af24c2c9746196550241bc01c50
```

> 还可以通过在提交日志中查找字符串的方式显示提交

```
$ git rev-parse :/"Commit A"
81993234fc12a325d303eccea20f6fd629412712
```

> 还可以使用 reflog 的相关语法。
```
$ git rev-parse HEAD@{0} master@{0}
6652a0dce6a5067732c00ef0a220810a7230655e
6652a0dce6a5067732c00ef0a220810a7230655e
```

## 版本范围表示法: git rev-lit


有的命令使用 一个版本范围作为参数，命令 git rev-list可以帮助研究 git 的个版本范围的语法。

> 使用一个提交 ID 查看该版本开始的所有历史提交

```
$ git rev-list --oneline A
8199323 Commit A: merge B with C.
0cd7f2e commit C.
776c5c9 Commit B: merge D with E and F
beb30ca Commit F: merge I with J
212efce Commit D: merge G with H
634836c commit I.
3252fcc commit J.
83be369 commit E.
2ab52ad commit H.
e80aa74 commit G.
```

> 两个或多个版本，相当于每个版本单独使用时只带的列表的并集

```
git rev-list --oneline D F
beb30ca Commit F: merge I with J
212efce Commit D: merge G with H
634836c commit I.
3252fcc commit J.
2ab52ad commit H.
e80aa74 commit G.
```

> 在一个版本前面加上符号(^)含义是取反，即排除这个版本以及其历史版本。

```
$ git rev-list --oneline ^G D
212efce Commit D: merge G with H
2ab52ad commit H.
```

> 和上面等价的“..”表示法。使用两个点连接连个版本，如 "G..D"，就相当于 “^G D”

```
$ git rev-list --oneline G..D
212efce Commit D: merge G with H
2ab52ad commit H.
```

版本取反的顺序不重要，但是‘..’表示法的前后表示法很重要。

> 三点表示法的含义是两个版本共同能够访问到的除外。如 B...C 将 B 和 C 共同能访问到的 F、I、J 排除在外。

```
$ git rev-list --oneline B...C
0cd7f2e commit C.
776c5c9 Commit B: merge D with E and F
212efce Commit D: merge G with H
83be369 commit E.
2ab52ad commit H.
e80aa74 commit G.
```

三点表示法，两个版本的前后顺序无关。

> 某提交历史，自身除外，用语法 '^@' 表示

```
$ git rev-list --oneline B^@
beb30ca Commit F: merge I with J
212efce Commit D: merge G with H
634836c commit I.
3252fcc commit J.
83be369 commit E.
2ab52ad commit H.
e80aa74 commit G
```

> 提交本山不包括其历史提交，用语法 “^!” 表示。

```
$ git rev-list --oneline B^!
776c5c9 Commit B: merge D with E and F

$ git rev-list --oneline F^! D
beb30ca Commit F: merge I with J
212efce Commit D: merge G with H
2ab52ad commit H.
e80aa74 commit G.
```
