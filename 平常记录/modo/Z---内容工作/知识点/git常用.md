```
# 日常开发 - 通常不需要每次都更新 只使用本地已有的仓库信息（可能不是最新的）
pod install

# 先更新本地仓库，再安装（确保获取最新信息）
pod install --repo-update

# 或者分开执行
pod repo update  # 先更新仓库
pod install      # 再安装
```

```
vim 强制退出

输入命令（冒号是关键）：

:q → 退出

:q! → 不保存退出

:wq → 保存并退出

:w → 保存但不退出
```

Git 初始化 提交

```objective-c
# 1. 进入你的项目目录
cd /Users/lin/Desktop/ActivitySDK

# 2. 初始化 Git 仓库（如果还没有 .git 文件夹）
git init

# 3. 查看当前分支（macOS Git 默认可能是 main 或 master）
git branch

# 4. 创建 main 分支并切换到它（如果需要）
git checkout -b main

# 5. 添加所有文件到 Git 暂存区
git add .

# 6. 提交文件到本地仓库
git commit -m "Initial commit"

# 7. 添加远程仓库（使用你的 SSH 地址）

git remote add origin	ssh://git@192.168.13.252:8022/modo_ios/activityui.git
git remote add origin ssh://git@192.168.13.252:8022/modo_ios/activitysdk.git

# 8. 推送本地 main 分支到远程，并建立追踪关系
git push -u origin main
```

