git 网址:  https://www.yiibai.com/git/git_fetch.html

workspace：是本地项目的工作目录，属于本地代码发生更新但尚未执行 git add 命令时		的状态，working tree的状态也随之更新

index：是索引文件，它是连接working tree和commit的桥梁，每当我们使用git add命	   令来登记后，index file的内容就会改变，此时index file就和working tree同步了
	
local repository：是本地仓库，当我们使用git commit命令提交最新代码时，代码才真正	进入git仓库。git commit -m “xxx” 就是将 index 里的内容提交到本地仓库中

remote repository：是远程仓库，当我们使用git push命令时就会将本地仓库的代码上传	至远程仓库，完成整个代码的上传工作




一.git diff  相关命令使用

1.1 git diff:查看 workspace 与 index 的差别

1.2 git diff --cached:查看 index 与 local repositorty 的差别

1.3 git diff HEAD:查看 workspace 和 local repository 的差别

//还没提交的时候个人理解是:git add 或者 选择(sourcetree勾选后  .patch相当于把内容写到一个text中)
git diff --cached > HDRDetect.patch

//git show 把commit的内容也写在patch中
git show HEAD > xxx.patch

git add .         命令只是把工作区当前的修改提交到暂存区中。

git commit  -m  "这里是添加注释"    一次将暂存区中的内容提交到版本库中。




二.git reset 命令用于回退版本，可以指定退回某一次提交的版本

//删除最新的一次提交 (删除本地最新的一条commit)  
git reset --hard HEAD~1

//删除远程内容
git push origin HEAD --force  





三.git relog查看所有日志操作(包括删除操作)

//恢复到某一个节点
(git branch branchName versionNumber，branchName为分支名称，versionNumber为版本号)
git branch origin/test/iOS-MetalCodeDemo 33f33fd70

四.修改commit的内容
修改最新一条commit 
git commit --amend


修改若干条commit

4.1 git log　
查看提交记录，可以看到commit记录 包括commitId，录入需要修改的commit前一次 对应的 commitId

4.2 git rebase -i 8876a66df1ea4a7e911c271b2bd3292da0
进入了Vim界面, 可以在顶部看到提交commitId之后的commit都可修改。

这里有几种修改选择：
pick：保留该 commit
reword：保留该 commit，但我需要修改该commit的 Message
edit：保留该 commit, 但我要停下来修改该提交(包括修改文件)
squash：将该 commit 和前一个 commit 合并
fixup：将该 commit 和前一个 commit 合并，但我不要保留该提交的注释信息
exec：执行 shell 命令
drop：丢弃这个 commit

4.3 按照实际需要去选择命令，我们这里需要的是 reword(或者r)，用来修改 Message。

把需要修改的commit message前面的 pick 改成 reword。

4.4 修改完之后，按 Esc 退出编辑，输入":wq" 保存并退出，

之后就会进入编辑界面,操作修改message,修改完之后还是按 Esc 退出编辑，输入":wq" 保存并退出。

4.5 再次执行 git log
看看修改好的记录
再更新到远程仓库

4.6 git push origin (branch 名称) -f

五,查看当前分支
git branch





git reset --hard origin/test/iOS-MetalCodeDemo
git status
git rebase test/iOS-MetalCodeDemo


git rebase origin/test/iOS-MetalCodeDemo