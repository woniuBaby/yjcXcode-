---
name: offline-ios-base
description: Use when working with the uni-non-x-demo offline iOS custom base workflow, especially when the user says 打离线 SDK, 打离线基座, iOS 自定义基座, Archive 好了, ipa 好了, or when changes touch nativeplugins or offline packaging scripts.
---

# 离线 iOS 自定义基座

用于 `uni-non-x-demo` 的离线 iOS 自定义基座流程，来源于 Cursor rule `.cursor/rules/offline-ios-base.mdc`。

完整文档以仓库内 `docs/ios/基座/离线iOS打包-完整协作说明.md` 为准；本 skill 是固定协作流程摘要。

## 改完代码后

根据 diff 主动告诉用户是否需要重新打基座：

- 不用打基座：只改了 `.vue`、`.js`、`js_sdk` 等前端资源时，说明直接用 HBuilderX 自定义基座运行即可。
- 要打基座：改了 `nativeplugins`、集成脚本、`copy-*-sdk`、离线打包相关脚本时，说明原因，并让用户确认后发送“打离线 SDK”。不要未经确认就跑 prepare。
- 改了 `nativeplugins/Modo-AppShare/ios/**/*.m` 或 `.h` 时，云打包和离线打包前都必须先更新 `ios/Modo-AppShare.framework`；否则云打包会继续使用旧二进制。

## 云打包 / 交付 Modo-AppShare 前

只要改过 Modo-AppShare iOS 原生源码，先执行：

```bash
./scripts/build-modo-appshare-ios-framework.sh
```

然后再交付或云打包整个插件目录：

```text
nativeplugins/Modo-AppShare/
```

不要只交付 `ios/` 或 `.m/.h` 源码。云打包不会编译 `.m`，只会使用 `ios/Modo-AppShare.framework` 等预编译产物。

## 用户说“打离线 SDK / 打离线基座”

在工程根目录 `uni-non-x-demo` 执行：

```bash
./scripts/prepare-offline-hello-archive.sh
```

该脚本已内置第一步：先执行 `build-modo-appshare-ios-framework.sh` 刷新 `Modo-AppShare.framework`，再集成离线 Hello。

该命令需要较高权限或打开 Xcode 时，按 Codex 的权限机制请求用户批准。

prepare 成功后会自动 `open` Xcode。告知用户：

- Release 已编过。
- 只需在 Xcode 里执行 Signing、Archive、Development 导出到桌面。
- Build 已由脚本 +1，在 General 核对即可。

不要跑 `setup-offline-hello.sh`，除非用户明确要求把 www 打进 Hello。

## 用户说“Archive 好了 / ipa 好了”

在用户 HBuilderX 打开的工程，也就是本仓库 `uni-non-x-demo`，执行：

```bash
./scripts/install-ios-debug-ipa.sh
```

无参数时脚本会选择桌面修改时间最新的 `HBuilder.ipa`。安装前终端会打印选用路径；若用户指定路径则使用用户给出的路径。

多个 ipa 有疑义时，先执行：

```bash
./scripts/install-ios-debug-ipa.sh --list
```

或者让用户发完整 ipa 路径。

## 安装后提醒

提醒用户在 HBuilderX 中选择：

```text
运行到 iOS 自定义基座 -> unpackage/debug/iOS_debug.ipa
```

仅改 JS / Vue / js_sdk 时，不必重新 Archive。

## 禁止

- 不要替用户 Archive，签名必须由用户本人在 Xcode 完成。
- 不要用 `repack-ios-debug-ipa.sh` 当可安装基座。
