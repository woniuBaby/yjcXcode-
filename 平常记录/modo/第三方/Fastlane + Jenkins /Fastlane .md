# Fastlane 

> #### 📌1.是什么

#### Fastlane是一个 专为 iOS / Android 设计的自动化构建与发布工具集。

#### 把打包、签名、上传这些复杂流程“代码化

 **一句话**：

> Fastlane 决定 **“移动端包是如何被正确地构建和发布的”**



> #### 📌2. 做什么?

在iOS中:

| Fastlane Action | 作用               |
| --------------- | ------------------ |
| `match`         | 管理证书 & profile |
| `gym`           | xcodebuild → ipa   |
| `scan`          | XCTest             |
| `pilot`         | 上传 TestFlight    |
| `deliver`       | 上传 App Store     |
| `sigh`          | profile 管理       |

在 Android

| Action                      | 作用              |
| --------------------------- | ----------------- |
| `gradle`                    | assemble / bundle |
| `supply`                    | 上传 Play Console |
| `signing`                   | keystore          |
| `firebase_app_distribution` | 内测              |

> #### 📌3. 优点

1. **抽象了平台复杂度**
2. **跨机器一致（本地 / CI）**
3. **流程可复用、可审计**
4. **非常适合写成“发布规范”**



```
CI：编译 + 校验 + 产物
CD：上传 TestFlight / 内测 / 商店
```

Fastlane 通常横跨 CI + CD。

----



实操---->

1. brew install fastlane 使用 Homebrew 安装 Fastlane

```
brew install fastlane
```

2. 安装号后看版本

   ```
   fastlane -v    fastlane 2.230.0
   ```

   

3. 在含有 HuaYuann.xcworkspace的目录内 执行

```
fastlane init

[15:48:54]: What would you like to use fastlane for?
1. 📸  Automate screenshots
2. 👩‍✈️  Automate beta distribution to TestFlight
3. 🚀  Automate App Store distribution
4. 🛠  Manual setup - manually setup your project to automate your tasks

选择2 
```

然后填写账号/密码/双重验证码

4. 会再项目生成
   ```
   执行完毕后，会生成fastlane文件夹、Gemfile文件，Appfile、Fastfile文件。
   
   Appfile: 存储有关开发者账号相关信息
   Fastfile: 核心文件，用于命令行调用和处理具体的流程，lane相对于一个action方法或函数
   Gemfile 类似于cocopods 的Podfile文件
   
   .env 配置环境变量（在fastlane init进行初始化后并不会自动生成，如果需要可以自己创建
   
   打开项目，看看刚生成的文件，你可能发现都没有代码高亮....
   因为 fastlane 使用 ruby 写的，所以要支持 ruby 啊
   我这里用的是 VS Code，搜索 ruby 插件安装。
   
   ```

   

5. 用VS Code 打开后,下载一些插件
   ```
   #常用插件
   fastlane add_plugin versioning  //版本控制
   fastlane add_plugin firim		//fir 打包
   fastlane add_plugin pgyer		//蒲公英
   
   详细:
   fastlane add_plugin versioning --> 用来修改 build 版本号和 version 版本号，fastlane 内嵌的actionincrement_build_number使用的是苹果提供的agvtool， 在更改Build的时候会改变所有target的版本号。如果你在一个工程里有多个target，每次编译，所有的Build都要加1。
   有了fastlane-plugin-versioning不仅可以指定target增加Build，当然也可以直接设定Version， 并且可以指定版本号的版本（major/miner/patch），这一点非常重要，而且这个插件也可以非常方便的修改 android 的版本号，插件排行榜第一位。
   
   astlane-plugin-pgyer ---> 上传到蒲公英分发平台。
   
   ```

6. 证书,Profile 类型

   ### 标准对应关系

   | Configuration       | 证书             | Profile 类型        | export_method |
   | ------------------- | ---------------- | ------------------- | ------------- |
   | Debug               | iOS Development  | Development Profile | development   |
   | Release（AdHoc）    | iOS Distribution | AdHoc Profile       | ad-hoc        |
   | Release（AppStore） | iOS Distribution | App Store Profile   | app-store     |

   👉 **Debug 和 Release 使用不同 Profile 是标准工程结构**

   ### 不正常的情况（会直接炸）

   - Debug 用了 Distribution Profile
   - Release 用了 Development Profile
   - export_method 和 Profile 类型不一致

7. # Fastlane 所有 UI 类型

   Fastlane 的 UI 不是打印日志，而是**控制流程级别的输出**。

   | 方法             | 用途              | 是否终止 |
   | ---------------- | ----------------- | -------- |
   | `UI.message`     | 普通信息          | 否       |
   | `UI.important`   | 关键步骤（黄色）  | 否       |
   | `UI.success`     | 成功（绿色）      | 否       |
   | `UI.error`       | 错误信息（红色）  | ❌ 不终止 |
   | `UI.user_error!` | 用户可修复错误    | ✅ 终止   |
   | `UI.crash!`      | Fastlane 内部错误 | ✅ 终止   |
   | `UI.header`      | 打印分段标题      | 否       |









实现功能 命令

```
fastlane ios huayuann_build type:dev branch:master update_description:"测试更新"
fastlane ios huayuann_build type:adhoc  update_description:"灵动岛修复" 
```



>  代码实现

1. 环境.env

```
# 国内环境
ENV_LABEL=国内
WORKSPACE=HuaYuann.xcworkspace
XCODEPROJ=HuaYuann.xcodeproj
SCHEME=HuaYuann
BUNDLEID=cn.lbwdhysj.gf.ios
BUNDLEID_LA=cn.lbwdhysj.gf.ios.HuaYuanLA
PROFILE_DEV="iOS Team Provisioning Profile: cn.lbwdhysj.gf.ios"
PROFILE_ADHOC="modo_huayuan_ios"
PROFILE_ADHOC_LA="modo_huayuan_ios_LA"
PGYER_API_KEY="7b0201426f20d987187832d5b170ac2c"
FEISHU_WEBHOOK="https://open.feishu.cn/open-apis/bot/v2/hook/2cd47959-b2ea-4d9b-a092-c70926aa7ffd"

```

fastlane 官方到目前为止仍然只支持 Bundler 2.x

```
begin
  ... 打包、上传、飞书成功通知 ...
rescue => e
  ... 飞书失败通知，然后 raise e ...
ensure
  ... Git 恢复、清空全局变量 ...
end
```





---

之后会将代码 补充,现在不断的修改
