modo yjc cjx 111111

1. brew install jenkins
   
   ```ruby
   Note: When using launchctl the port will be 8080.
   To start jenkins now and restart at login:
     brew services start jenkins
   Or, if you don't want/need a background service you can just run:
     /opt/homebrew/opt/jenkins/bin/jenkins --httpListenAddress\=127.0.0.1 --httpPort\=8080
   ==> Summary
   🍺  /opt/homebrew/Cellar/jenkins/2.546: 9 files, 109MB
   ==> Running `brew cleanup jenkins`...
   Disable this behaviour by setting `HOMEBREW_NO_INSTALL_CLEANUP=1`.
   Hide these hints with `HOMEBREW_NO_ENV_HINTS=1` (see `man brew`).
   
   解析 日志
   /opt/homebrew/Cellar/jenkins/2.546
   版本：2.546
   Jenkins 强制监听端口 8080
   
   两种启动 Jenkins 的方式
   方式一：作为系统服务（推荐用于长期运行）
   brew services start jenkins ---> 访问地址 http://localhost:8080
   方式二：手动前台运行（调试 / 临时使用）
   /opt/homebrew/opt/jenkins/bin/jenkins \
     --httpListenAddress=127.0.0.1 \
     --httpPort=8080
   
   若改端口: /opt/homebrew/opt/jenkins/bin/jenkins --httpPort=9090
   
   ```
   
   2. brew services start jenkins--> 访问地址 http://localhost:8080
   
      ```
      启动 Jenkins
      
      为了确保管理员安全地安装 Jenkins，密码已写入到日志中（不知道在哪里？）该文件在服务器上：
      /Users/lin/.jenkins/secrets/initialAdminPassword
      
      
      ```
   
   3. 在浏览器地址栏输入：重启
   
   ```
   http://localhost:8080/restart
   ```
   
   

```
#!/bin/bash
set -e
set -o pipefail

# 1️⃣ 固定 Ruby 版本（唯一来源）
export PATH="/opt/homebrew/opt/ruby@3.2/bin:/opt/homebrew/lib/ruby/gems/3.2.0/bin:/opt/homebrew/bin:$PATH"

# 2️⃣ CI 环境变量
export CI=true
export FASTLANE_SKIP_UPDATE_CHECK=1
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8

echo "===== ENV CHECK ====="
which ruby
ruby -v
which bundle
bundle -v
which fastlane
which pod
pod --version
which node
node -v

# 3️⃣ 进入项目目录
cd /Users/lin/Documents/HuaYuann/build/ios/proj

# 4️⃣ 安装 Gemfile.lock 中定义的依赖
bundle install

# 5️⃣ 使用 bundle exec 运行 fastlane
bundle exec fastlane ios huayuann_build type:adhoc update_description:"jenkins 打包"

```

2026年01月17日 更新内容

```
#!/bin/bash
set -e
set -o pipefail

# 1️⃣ 固定 Ruby 版本（唯一来源）
export PATH="/opt/homebrew/opt/ruby@3.2/bin:/opt/homebrew/lib/ruby/gems/3.2.0/bin:/opt/homebrew/bin:$PATH"

# 2️⃣ CI 环境变量
export CI=true
export FASTLANE_SKIP_UPDATE_CHECK=1
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8

echo "===== ENV CHECK ====="
which ruby
ruby -v
which bundle
bundle -v
which fastlane
which pod
pod --version
which node
node -v

# 3️⃣ 进入项目目录
cd /Users/lin/Documents/HuaYuann/build/ios/proj

# 4️⃣ 安装 Gemfile.lock 中定义的依赖
# bundle install
bundle install --path vendor/bundle

# 5️⃣ 使用 bundle exec 运行 fastlane
bundle exec fastlane ios huayuann_build 
# bundle exec fastlane ios huayuann_build type:adhoc update_description:"jenkins 打包"

```

### Deployment 模式是什么

`deployment` 是 Bundler 的一种模式，设计初衷是 **为了 CI/CD 或生产环境**，让 Ruby 项目在固定的依赖版本下运行，避免意外更新。

特点：

- 只允许使用 **Gemfile.lock 中列出的版本**安装。
- 不会尝试更新依赖。
- 如果缺少 `Gemfile.lock`，或者依赖不匹配，会报错直接失败。
- 常用于 **Jenkins、Fastlane 自动化打包**，保证每次构建都是确定、可重复的。

换句话说：`deployment = true` 的意思是 “严格锁定依赖版本，绝不随意安装新的 gem”。

---

>  让同事也可以访问确认 Jenkins 是否在运行

1. 在终端执行：

```
ps aux | grep jenkins
```

2. 确认 Jenkins 监听的端口

默认 Jenkins 用 8080，但它可能只监听 `127.0.0.1`（本机回环）而不是局域网 IP。

用这个命令检查：

```
lsof -i :8080
```

3. 修改 Jenkins 监听地址

你当前启动 Jenkins 用了：

```
java -jar /opt/homebrew/Cellar/jenkins/2.546/libexec/jenkins.war --httpListenAddress=127.0.0.1 --httpPort=8080
```

需要改成：

```
java -jar /opt/homebrew/Cellar/jenkins/2.546/libexec/jenkins.war --httpListenAddress=0.0.0.0 --httpPort=8080
```

4. 找到你电脑在局域网的 IP：
   ```
   ipconfig getifaddr en0
   
   192.168.63.138
   ```

   本机：`http://127.0.0.1:8080/` → OK

   局域网同事：`http://192.168.63.138:8080/` → OK







---

jenkins 权限设置
系统管理-->全局安全配置-->授权策略-->安全矩阵
![image-20260117142915918](/Users/lin/Library/Application Support/typora-user-images/image-20260117142915918.png)