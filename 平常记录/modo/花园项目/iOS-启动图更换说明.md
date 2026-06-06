# iOS 启动图更换说明（主 App）

本文记录 **Assets 中与本包启动展示相关的图片集** 命名与含义，便于换图时对照，避免漏改或混用「带忠告 / 不带忠告」素材。

## 资源位置

- Xcode 资源目录：`native/engine/ios/Images.xcassets/`
- 系统启动页 Storyboard：`native/engine/ios/Base.lproj/LaunchScreen.storyboard`、`native/engine/ios/zh-Hans.lproj/LaunchScreen.storyboard`（背景图引用名见下节）

## 截图圈出的 7 个 imageset 名称（请按此名在工程内替换）

| imageset 名称 | 用途归类 |
|----------------|----------|
| `1242-2208` | **不带忠告**（纯分辨率命名） |
| `1242-2688` | **不带忠告** |
| `2304-3072` | **不带忠告** |
| `gameTips1` | **带忠告**（游戏提示文案） |
| `gameTips2` | **带忠告** |
| `gameTips3` | **带忠告** |
| `laudef` | **Launch Screen Storyboard 专用背景图**（见下） |

### 分类规则（换图时自检）

- **数字分辨率命名**（`1242-2208`、`1242-2688`、`2304-3072`）：画面为 **不带忠告** 版本。
- **`gameTips` 前缀**（`gameTips1`、`gameTips2`、`gameTips3`）：画面为 **带忠告** 版本。
- **`laudef`**：供 **`LaunchScreen.storyboard`** 中 `UIImageView` 引用的图片名；设计稿规格为 **1242 × 2688**（与 `@2x` 逻辑像素对应关系以当前 `imageset` / Storyboard 资源声明为准，换图后需在 Xcode 中确认无拉伸/裁切问题）。

## 服务端提醒（非本仓库静态图）

- 另有一张 **启动相关图** 由 **网络 / 服务端** 下发或配置，文件名以 **`en` 开头**；换启动整体方案时 **需同步协调服务端改资源或配置**，客户端单独改本地图无法覆盖该路径。

## 验收建议

- 冷启动观察：Storyboard 首帧、`1242-*` / `2304-*` / `gameTips*` 在各机型或逻辑分辨率下是否选用正确。
- 若涉及多语言或合规文案，核对「带忠告」三套是否与设计稿一致。
