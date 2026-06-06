# HBuilderX「运行」后 App 完全没变化 — 排查

## 原因（你当前的情况）

1. **`iOS_debug.ipa` 里内嵌的是旧版 JS**（只有 `uni.$on`、日志写「等待操作…」）
2. HBuilderX「运行到 iOS」本应通过 WiFi **热更新 www**，若同步失败，App 一直用 ipa 里那份旧资源
3. 所以看起来像：**HBuilderX 控制台在跑，手机页面却纹丝不动**

验证：打开 App **首页**，副标题若没有 **`www 20260530-v3`**，说明手机仍是旧 www。

---

## 方案 1：Xcode 直接 Run（推荐，最稳）

不依赖 HBuilderX 同步。

1. HBuilderX 对 unidemo **运行一次**（生成 `unpackage/dist/dev/app-plus`）
2. 终端执行：
   ```bash
   cd /Users/lin/Desktop/unidemo
   ./scripts/sync-www-to-hello.sh
   ```
3. Xcode 打开 `HBuilder-Hello.xcodeproj` → 选真机 → **Run (⌘R)**
4. 打开 App，首页应 toast **`www 20260530-v3`**

---

## 方案 2：重装更新后的自定义基座 ipa

1. HBuilderX **运行一次** unidemo（编译出最新 dev）
2. 终端：
   ```bash
   cd /Users/lin/Desktop/unidemo
   chmod +x scripts/repack-ios-debug-ipa.sh
   ./scripts/repack-ios-debug-ipa.sh
   ```
3. **删除** iPhone 上的旧 App
4. 重新安装 `unpackage/debug/iOS_debug.ipa`
5. 打开 App，确认首页有 **`www 20260530-v3`**

---

## 方案 3：继续用 HBuilderX 热更新（需满足全部条件）

| 条件 | 说明 |
|------|------|
| 已装自定义基座 | `iOS_debug.ipa`，Bundle ID `cn.modocommunity.ios` |
| 运行菜单 | ☑ **使用自定义基座运行** → **本地基座** |
| 同一 WiFi | Mac 与 iPhone 同一局域网，勿开 VPN 隔离 |
| 运行后 | **杀掉 App 再打开**（不要只切后台） |
| 控制台 | 应出现同步/编译成功，无红色报错 |

基座内 `control.xml` 需 `syncDebug="true"`（当前 ipa 已具备）。

---

## 进入 Demo 后如何确认正常

穿山甲 Demo 页 **顶部**「事件日志」：

- 进入即显示：`页面已打开`、`插件: Modo-AdReward 已加载`（或提示未找到插件）
- 点 **初始化**：出现 `→ initAdSdk`，随后 `[sdk] init OK`

若只有「等待操作…」、没有 **www 20260530-v3**，一律按上面方案 1 或 2 处理，不要反复点 HBuilderX 运行。

---

## HBuilderX 运行菜单检查

`运行 → 运行到手机或模拟器 → 运行到 iOS App 基座`：

- [ ] 使用自定义基座运行
- [ ] 本地基座
- [ ] 基座路径：`unidemo/unpackage/debug/iOS_debug.ipa`

（已在 `.hbuilderx/launch.json` 配置。）
