## `git fetch`命令用于从另一个存储库下载对象和引用。





**1. 更新远程跟踪分支**

```shell
$ git fetch origin
```

**2. 在远程分支上窥视，无需在本地存储库中配置远程**

```shell
$ git fetch git://git.kernel.org/pub/scm/git/git.git maint
$ git log FETCH_HEAD


Shell
```

第一个命令从 `git://git.kernel.org/pub/scm/git/git.git` 从存储库中获取`maint`分支，第二个命令使用`FETCH_HEAD`来检查具有`git-log`的分支。

**3.将某个远程主机的更新**

```shell
$ git fetch <远程主机名>
```

要更新所有分支，命令可以简写为：

```objc
$ git fetch
```

上面命令将某个远程主机的更新，全部取回本地。默认情况下，`git fetch`取回所有分支的更新。如果只想取回特定分支的更新，可以指定分支名,如下所示 - 

```shell
$ git fetch <远程主机名> <分支名>
```

比如，取回`origin`主机的`master`分支。

```shell
$ git fetch origin master
```

所取回的更新，在本地主机上要用”远程主机名/分支名”的形式读取。比如`origin`主机的`master`分支，就可以用`origin/master`读取。







---

# 问题

<font color=red>LibreSSL SSL_read: error:02FFF03C:system library:func(4095):Operation timed out, errno 60 </font>

```
解决方法 

`git fetch --force --tags origin`
```

