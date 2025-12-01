### git diff  

- 1.1  git diff:查看 workspace 与 index 的差别

- 1.2 git diff --cached:查看 index 与 local repositorty 的差别

- 1.3 git diff HEAD:查看 workspace 和 local repository 的差别



//还没提交的时候个人理解是:

git add 或者 选择(sourcetree勾选后  .patch相当于把内容写到一个text中)
git diff --cached > HDRDetect.patch

//git show 把commit的内容也写在patch中
git show HEAD > xxx.patch

git add .         命令只是把工作区当前的修改提交到暂存区中。

git commit  -m  "这里是添加注释"    一次将暂存区中的内容提交到版本库中。