---

### 速度复制代码

```
pod install
用于添加或移除第三方库框架

pod install --verbose --no-repo-update
该命令只安装新添加的库，已更新的库忽略


pod update
在终端执行命令 
pod update --verbose --no-repo-update


pod update 库名--verbose --no-repo-update

该命令只更新指定的库，其它库忽略
podfile 里面删除要删除的库
```

---

### 仓库位置

```
/Users/mac/.cocoapods/repos/edu-git-cocoapods-specs
本地仓库位置
```

```
File not found: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/arc/libarclite_iphoneos.a


```

---

### 安装pod

一、安装homebrew
 Homebrew是一款包管理工具，主要有四个部分组成: brew、homebrew-core 、homebrew-cask、homebrew-bottles。
 打开终端输入：

```bash
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

二、升级ruby
 目前mac的默认ruby环境都是2.6.10，但是默认的cocoapods的版本支持已经升级到了3.2.2，所以我们要升级当前的ruby环境：
 打开终端执行下面命令，等待安装完成即可。

```undefined
brew install ruby
```

然后我们继续执行：

```undefined
ruby -v
```

执行完命令后，其实还是原来的版本👌，这是因为环境变量没有配置。因此，还有一个步骤就是配置环境变量。

```bash
echo 'export PATH="/usr/local/opt/ruby/bin/:$PATH"' >> ~/.zshrc
source ~/.zshrc
ruby -v
```

如果出现的结果为3.2.2及以上版本，就说明安装成功。
 三、安装cocoapods
 1、我们更换gem镜像地址

```csharp
gem sources --remove https://rubygems.org/
gem sources -a https://gems.ruby-china.com/
```

如果正常则表示替换成功了，如果出现了下面的问题，则说明权限出了问题。这个时候我们要给他指出的文件夹开放权限，终端执行：

```bash
sudo chmod -R 777 /Users/lee/.local
```

这个之后再执行上面的替换操作就会正常了。

2、安装cocoapods
 终端执行：

```bash
sudo gem install -n /usr/local/bin cocoapods
```

这个时候会提示我们安装完成，但是没有完，我们这个时候我们继续执行第三步。

3、下载pod依赖
 终端执行:

```php
git clone https://github.com/CocoaPods/Specs.git ~/.cocoapods/repos/master
```

等待下载完成即可，此时cocoapods已经安装完成可以使用了。

[https://www.swvq.com/boutique/detail/63672](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.swvq.com%2Fboutique%2Fdetail%2F63672)

---



