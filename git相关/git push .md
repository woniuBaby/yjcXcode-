```objective-c
git push 命用于从将本地的分支版本上传到远程并合并。

命令格式如下：

git push <远程主机名> <本地分支名>:<远程分支名>

如果本地分支名与远程分支名相同，则可以省略冒号：

git push <远程主机名> <本地分支名>


实例
以下命令将本地的 master 分支推送到 origin 主机的 master 分支。

$ git push origin master
相等于：

$ git push origin master:master

如果省略 master
如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。
$ git push origin :master
# 等同于
$ git push origin --delete master
//更多请阅读：https://www.yiibai.com/git/git_push.html




如果本地版本与远程版本有差异，但又要强制推送可以使用 --force 参数：

git push --force origin master
```

