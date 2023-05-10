修改commit的内容

## 1.修改最新一条commit 

`git commit --amend`

## 2.修改若干条commit

- 2.1  git log　
  查看提交记录，可以看到commit记录 包括commitId，录入需要修改的commit前一次 对应的 commitId



- 2.2 git rebase -i 8876a66df1ea4a7e911c271b2bd3292da0
  进入了Vim界面, 可以在顶部看到提交commitId之后的commit都可修改。

```
这里有几种修改选择：
pick：保留该 commit
reword：保留该 commit，但我需要修改该commit的 Message
edit：保留该 commit, 但我要停下来修改该提交(包括修改文件)
squash：将该 commit 和前一个 commit 合并
fixup：将该 commit 和前一个 commit 合并，但我不要保留该提交的注释信息
exec：执行 shell 命令
drop：丢弃这个 commit
```



- 2.3 按照实际需要去选择命令，我们这里需要的是 reword(或者r)，用来修改 Message。

​	**把需要修改的commit message前面的 pick 改成 reword。**



- 2.4 修改完之后，按 Esc 退出编辑，输入":wq" 保存并退出，

之后就会进入编辑界面,操作修改message,修改完之后还是按 Esc 退出编辑，输入":wq" 保存并退出。

- 2.5 再次执行 git log
  看看修改好的记录
  再更新到远程仓库

- 2.6 git push origin (branch 名称) -f
