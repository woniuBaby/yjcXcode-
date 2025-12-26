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