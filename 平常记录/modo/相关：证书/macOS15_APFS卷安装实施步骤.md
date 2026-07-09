# 在当前 Mac 上新增 APFS 卷安装 macOS 15.x 的实施步骤

更新时间：2026-07-08

## 先看这个流程

当前已经准备好：

```text
macOS 15.x 安装器：
/Applications/Install macOS Sequoia.app

Xcode 26.2：
/Applications/Xcode-26.2.app

本机最小备份：
/Users/lin/Desktop/HuaYuann_最小备份_20260708

实施文档：
/Users/lin/Desktop/macOS15_APFS卷安装实施步骤.md

U 盘：
原始卷名：KINGSTON
原始路径：/Volumes/KINGSTON
容量：124GB
制作启动盘后名称会变为：Install macOS Sequoia
```

接下来要做的事情：

```text
开始
 |
 |-- 1. 确认当前系统还有 150GB - 200GB 可用空间
 |
 |-- 2. 打开“磁盘工具”
 |       |
 |       |-- 显示 -> 显示所有设备
 |       |-- 选中内置硬盘下的 APFS 容器
 |       |-- 点 “+” 新增 APFS 卷
 |       |-- 名称建议：macOS-15-Test
 |
 |-- 3. 准备 32GB 以上 U 盘
 |       |
 |       |-- U 盘会被抹掉
 |       |-- 不要只复制 Install macOS Sequoia.app
 |       |-- 必须做成“启动安装盘”
 |
 |-- 4. 制作 macOS Sequoia 启动安装盘
 |       |
 |       |-- 安装器路径：/Applications/Install macOS Sequoia.app
 |       |-- 使用 createinstallmedia 写入 U 盘
 |
 |-- 5. 从 U 盘启动安装器
 |       |
 |       |-- Apple Silicon：关机 -> 长按电源键 -> 选择 Install macOS Sequoia
 |       |-- Intel：重启时按住 Option -> 选择 Install macOS Sequoia
 |
 |-- 6. 在安装器中选择安装目标
 |       |
 |       |-- 选择：macOS-15-Test
 |       |-- 不要选：Macintosh HD
 |       |-- 不要选：Data
 |       |-- 不要抹掉主系统
 |
 |-- 7. 等待安装，电脑会自动重启多次
 |
 |-- 8. 安装完成后进入 macOS 15.x 新系统
 |       |
 |       |-- 初始化用户
 |       |-- 登录 Apple ID
 |       |-- 打开 Xcode 26.2
 |       |-- 接受协议、安装组件
 |
 |-- 9. 从旧系统里找备份和工具
 |       |
 |       |-- Finder 侧边栏 -> 位置 -> 找旧系统磁盘
 |       |-- 进入：Users/lin/Desktop/HuaYuann_最小备份_20260708
 |       |-- 如果看不到旧系统磁盘，看下面“找不到备份怎么办”
 |
 |-- 10. 在新系统中准备项目
 |       |
 |       |-- 推荐重新 clone HuaYuann
 |       |-- 或从旧系统复制一份项目
 |       |-- 登录 Xcode 账号，自动下载证书和 profiles
 |       |-- 如果签名失败，再回旧系统导出 .p12
 |
 |-- 11. 连接 iOS 12.5.7 真机，用 Xcode 26.2 运行花园 App
 |
完成验证
```

当前已经验证过：

```text
直接双击 /Applications/Install macOS Sequoia.app：
不可行，macOS 26.3 会提示不能从当前系统使用此安装器。

startosinstall 命令指定 --volume /Volumes/macOS-15-Test：
不可行，macOS 15 的 startosinstall 不支持 --volume 参数。

因此当前可行路径：
制作启动 U 盘 -> 从 U 盘启动 -> 安装到 macOS-15-Test。
```

U 盘实际操作记录：

```text
U 盘原始名称：
KINGSTON

U 盘原始路径：
/Volumes/KINGSTON

U 盘容量：
124GB

Codex 自动化执行 createinstallmedia：
失败在 bless 阶段，错误为 Failed to write .IAPhysicalMedia / bless failed。
原因是自动化环境权限不足，不代表 U 盘或安装器不可用。

已在真实 Terminal 手动执行：
sudo /Applications/Install\ macOS\ Sequoia.app/Contents/Resources/createinstallmedia \
  --volume /Volumes/Install\ macOS\ Sequoia \
  --nointeraction

当前已看到：
Erasing disk: 100%
Copying essential files...
Copying the macOS RecoveryOS...

这表示 U 盘启动盘正在正常制作中。
制作完成后应看到类似：
Install media now available at "/Volumes/Install macOS Sequoia"
```

## 进新系统后如何找旧系统里的备份

正常情况下，新 macOS 15.x 里可以看到旧系统卷。

打开 Finder：

```text
Finder -> 左侧“位置”
```

找类似下面的磁盘名称：

```text
Macintosh HD
macOS 26.3
Data
```

然后进入：

```text
/Volumes/旧系统卷名/Users/lin/Desktop/HuaYuann_最小备份_20260708
```

你要找的备份内容是：

```text
README.md                         恢复说明
git/                              分支、HEAD、status、未提交 diff
docs/                             安装文档和闪退排查记录
environment/                      当前系统、Xcode、安装器信息
certificates/                     预留给 .p12 证书
profiles/                         描述文件备份；如果为空，新系统用 Xcode 自动下载
```

如果 Finder 不显示旧系统卷：

```text
1. 打开“磁盘工具”
2. 查看左侧是否有旧系统的数据卷
3. 如果是灰色，选中后点击“装载”
4. 再回 Finder 的“位置”里查看
```

如果仍然找不到备份：

```text
1. 先回到旧系统
   Apple Silicon：关机 -> 长按电源键 -> 选择原来的 macOS 26.3
   Intel：重启按住 Option -> 选择原来的 macOS 26.3

2. 确认备份目录还在：
   /Users/lin/Desktop/HuaYuann_最小备份_20260708

3. 可以把备份目录复制到新系统能看到的位置，
   或重新进入 macOS 15.x 后从旧系统卷里再找。
```

## 进新系统后 Xcode 26.2 怎么用

如果新系统能看到旧系统的应用目录，可以复制：

```text
/Volumes/旧系统卷名/Applications/Xcode-26.2.app
```

到新系统：

```text
/Applications/Xcode-26.2.app
```

也可以直接从旧系统卷里临时打开，但更建议复制到新系统 `/Applications`。

第一次进入新系统后执行：

```bash
sudo xcode-select -s /Applications/Xcode-26.2.app/Contents/Developer
sudo xcodebuild -license accept
xcodebuild -runFirstLaunch
xcodebuild -version
```

期望看到：

```text
Xcode 26.2
Build version 17C52
```

## 进新系统后签名怎么处理

优先走 Xcode 自动签名：

```text
Xcode -> Settings -> Accounts -> 登录 Apple ID
选择 Team
Download Manual Profiles
```

如果自动签名正常，就不需要手动恢复证书。

如果自动签名失败，再回旧系统导出 `.p12`：

```text
钥匙串访问 -> 登录 -> 我的证书 -> Apple Development -> 导出 .p12
```

然后放到：

```text
/Users/lin/Desktop/HuaYuann_最小备份_20260708/certificates/
```

再从新系统导入钥匙串。

## 目标

在不抹掉当前 macOS 26.3 主系统的前提下，在内置硬盘中新建一个 APFS 系统卷，安装 macOS 15.x，用于验证：

- macOS 15.x + Xcode 26.2
- iOS 12.5.7 真机
- 花园 App 是否可以正常启动和调试

这个方案不是把当前系统直接降级，而是做一个“同一台 Mac 上的独立测试系统”。

```text
内置硬盘
├─ macOS 26.3      当前主系统，日常继续使用
└─ macOS-15-Test   新增测试系统，专门验证 iOS 12 老设备
```

## 当前结论

当前问题更像是 macOS 26.3 系统层面对 iOS 12 老设备的真机启动/调试链路兼容性问题，而不是项目代码本身无法构建 iOS 12 包。

已知对比：

```text
相同项目
相同 iOS 12.5.7 真机
相同 Xcode 26.2

macOS 15.6：正常运行
macOS 26.3：启动失败 / 闪退 / Unable to launch
```

因此需要一个 macOS 15.x 环境作为对照环境。

## 重要前提

1. 你的 Mac 必须支持安装 macOS Sequoia 15。
2. Mac 不能安装早于它出厂支持版本的 macOS。
3. 新增 APFS 卷通常不抹掉当前系统，但仍属于系统级操作，必须先备份重要资料。
4. 如果只为验证 iOS 12 老设备，优先使用 APFS 新卷或外接 SSD，不建议直接抹掉当前 macOS 26.3。

Apple 官方兼容性页面：

- macOS Sequoia 15 兼容机型：https://support.apple.com/en-us/120282

## 一、确认本机是否支持 macOS 15.x

打开左上角苹果菜单：

```text
 -> About This Mac / 关于本机
```

确认你的 Mac 型号是否在 Apple 官方 macOS Sequoia 15 兼容列表中。

常见支持范围包括：

- MacBook Pro 2018 及更新
- MacBook Air 2020 及更新
- Mac mini 2018 及更新
- iMac 2019 及更新
- Mac Studio 全系列
- Mac Pro 2019 及更新

如果你的 Mac 是更新型号，还要注意：如果它出厂系统已经高于 macOS 15，则无法安装 macOS 15。

## 二、下载 macOS 15.x 安装器

Apple 官方说明：

- 下载和安装 macOS：https://support.apple.com/en-us/102662

### 方式 1：终端下载，推荐

先查看 Apple 当前给这台 Mac 提供哪些完整安装器：

```bash
softwareupdate --list-full-installers
```

本机在 2026-07-08 查询到的 macOS 15.x 可用列表是：

```text
macOS Sequoia 15.7.7
macOS Sequoia 15.7.5
macOS Sequoia 15.7.4
macOS Sequoia 15.7.3
macOS Sequoia 15.7.2
```

当前列表里没有 15.6。因此如果必须完全对齐同事的 macOS 15.6，`softwareupdate` 目前不一定能直接下载到 15.6。

实际执行记录：

```text
2026-07-08 已成功下载 macOS Sequoia 15.7.7 完整安装器。

安装器路径：
/Applications/Install macOS Sequoia.app

安装器体积：
15G

版本信息：
DTPlatformVersion = 15.7
DTPlatformBuild   = 24G719

说明：
这是当前 Apple 给这台 Mac 提供的 macOS 15.x 完整安装器。
它不是 15.6，但属于 macOS 15 系列，可以作为 APFS 新卷测试环境使用。
```

下载当前可用的 macOS 15.x，例如 15.7.7：

```bash
softwareupdate --fetch-full-installer --full-installer-version 15.7.7
```

如果想尝试 15.6，可以执行：

```bash
softwareupdate --fetch-full-installer --full-installer-version 15.6
```

如果返回 `update not found`，表示 Apple 当前没有给这台 Mac 提供该版本。

下载完成后，安装器通常放在：

```text
/Applications/Install macOS Sequoia.app
```

注意：下载完成后安装器可能会自动打开。此时先退出安装器，不要直接安装到当前主系统。

### 方式 2：App Store 下载

Apple 官方提供 macOS Sequoia 15 的 App Store 入口：

- https://apps.apple.com/app/macos-sequoia/id6596773750

点击 `Get / 获取` 后，系统通常会跳到“软件更新”开始下载。

下载完成后，安装器同样通常位于：

```text
/Applications/Install macOS Sequoia.app
```

说明：App Store 通常只给该大版本的当前可用版本，不保证能下载到精确的 15.6。

### 方式 3：Apple Developer 下载

如果必须要精确的 macOS 15.6，需要去 Apple Developer Downloads 查找是否仍提供该版本的完整安装器或恢复镜像：

- https://developer.apple.com/download/

只建议使用 Apple 官方来源，不建议使用第三方镜像。

## 三、制作 U 盘启动安装盘

当前必须走 U 盘启动安装盘方式。不能只把 `Install macOS Sequoia.app` 复制到 U 盘里再双击。

原因：

```text
当前系统是 macOS 26.3
安装器是 macOS 15.7.7

在 macOS 26.3 里直接双击低版本安装器会被系统拦截。
把 app 复制到 U 盘里再双击，也仍然会被拦截。
```

### 1. 准备 U 盘

要求：

```text
容量：32GB 以上
注意：制作启动安装盘会抹掉 U 盘全部内容
```

如果 U 盘里有资料，先备份出来。

### 2. 用磁盘工具抹掉 U 盘

打开：

```text
应用程序 -> 实用工具 -> 磁盘工具
```

操作：

```text
1. 左上角：显示 -> 显示所有设备
2. 左侧选中 U 盘最外层设备，不要选内置硬盘
3. 点击“抹掉”
4. 名称：MyVolume
5. 格式：Mac OS 扩展（日志式）或 APFS
6. 方案：GUID 分区图
```

建议名称就用：

```text
MyVolume
```

这样后面的命令可以直接复制。

### 3. 执行 createinstallmedia

确认安装器存在：

```text
/Applications/Install macOS Sequoia.app
```

确认 U 盘挂载路径存在：

```text
/Volumes/KINGSTON
```

如果 U 盘仍叫 `KINGSTON`，执行：

```bash
sudo /Applications/Install\ macOS\ Sequoia.app/Contents/Resources/createinstallmedia \
  --volume /Volumes/KINGSTON
```

如果之前制作失败后，U 盘已经被改名为 `Install macOS Sequoia`，执行：

```bash
sudo /Applications/Install\ macOS\ Sequoia.app/Contents/Resources/createinstallmedia \
  --volume /Volumes/Install\ macOS\ Sequoia \
  --nointeraction
```

这个命令会提示：

```text
To continue we need to erase the volume...
```

输入：

```text
Y
```

然后等待写入完成。

完成后，U 盘名称通常会变成：

```text
Install macOS Sequoia
```

成功标志：

```text
Install media now available at "/Volumes/Install macOS Sequoia"
```

如果出现：

```text
Failed to write .IAPhysicalMedia
The bless of the installer disk failed
```

说明当前执行环境权限不足。用 macOS 自带“终端”手动执行上面的 `sudo createinstallmedia` 命令即可。

### 4. 从 U 盘启动

Apple Silicon Mac：

```text
1. 插着 U 盘
2. 关机
3. 长按电源键，不要松
4. 看到“正在载入启动选项”后松开
5. 选择 Install macOS Sequoia
6. 点继续
```

Intel Mac：

```text
1. 插着 U 盘
2. 重启
3. 立刻按住 Option
4. 选择 Install macOS Sequoia
```

如果启动选项里看不到 U 盘：

```text
1. U 盘可能没有制作成功
2. U 盘可能没有插稳
3. Mac 安全策略可能不允许外部启动
```

### 5. 安装到新卷

进入安装器后，选择：

```text
Install macOS Sequoia
```

选择目标磁盘时，必须选：

```text
macOS-15-Test
```

不要选择：

```text
Macintosh HD
Data
当前 macOS 26.3 主系统卷
```

如果没看到 `macOS-15-Test`：

```text
1. 点“显示所有磁盘”
2. 或打开安装器菜单里的“磁盘工具”
3. 确认 macOS-15-Test 已挂载
4. 退出磁盘工具，回到安装器
```

### 6. U 盘什么时候可以拔

U 盘只在安装阶段需要。

需要插着的阶段：

```text
1. 制作启动安装盘时
2. 从 U 盘启动安装器时
3. 安装器把 macOS 15 安装到 macOS-15-Test 的前半段
```

安装完成后，如果启动选项里已经能看到并进入：

```text
macOS-15-Test
```

就可以拔掉 U 盘。

后续启动 macOS 15 测试系统时：

```text
Apple Silicon：关机 -> 长按电源键 -> 选择 macOS-15-Test
Intel：重启按住 Option -> 选择 macOS-15-Test
```

## 四、新增 APFS 卷

打开：

```text
应用程序 -> 实用工具 -> 磁盘工具
```

操作步骤：

1. 左上角选择 `显示 -> 显示所有设备`。
2. 找到内置硬盘下的 APFS 容器。
3. 选中 APFS 容器，不是单个系统卷。
4. 点击工具栏上的 `+` 新增卷。
5. 名称填写：

```text
macOS-15-Test
```

6. 格式选择：

```text
APFS
```

7. 大小不必固定，但建议当前内置硬盘至少空出：

```text
150GB - 200GB
```

8. 点击添加。

不建议在不了解磁盘编号的情况下直接用命令行新增卷，避免选错容器。

## 五、安装 macOS 15.x 到新卷

打开：

```text
/Applications/Install macOS Sequoia.app
```

关键点：

```text
一定要选择安装到 macOS-15-Test
不要选择当前 macOS 26.3 主系统卷
```

如果安装器没有显示目标磁盘，点击：

```text
Show All Disks / 显示所有磁盘
```

然后选择：

```text
macOS-15-Test
```

继续安装，等待重启和系统初始化。

## 六、启动到 macOS 15.x

Apple Silicon Mac：

```text
1. 关机
2. 长按电源键
3. 出现启动选项后松开
4. 选择 macOS-15-Test
5. Continue / 继续
```

Intel Mac：

```text
1. 重启
2. 按住 Option
3. 选择 macOS-15-Test
```

进入新系统后，可以在：

```text
系统设置 -> 通用 -> 启动磁盘
```

临时切换默认启动系统。

## 七、在 macOS 15.x 中准备开发环境

进入 macOS 15.x 后，按最小验证环境准备：

1. 安装 Xcode 26.2。
2. 打开一次 Xcode，接受协议并安装组件。
3. 登录 Apple ID。
4. 导入或重新下载开发证书、描述文件。
5. 安装项目需要的 Node、npm、CocoaPods 等依赖。
6. 拉取或复制花园项目。
7. 执行 `pod install`。
8. 用 iOS 12.5.7 真机运行。

建议先不要迁移大量个人资料，只准备能运行项目的最小环境。

## 八、验证目标

在 macOS 15.x + Xcode 26.2 中验证：

```text
1. Xcode 是否能识别 iOS 12.5.7 真机
2. 花园 App 是否能安装到手机
3. Xcode 是否能拉起 App
4. 是否还出现 Unable to launch / Code -12
5. 是否还在启动阶段闪退
```

如果 macOS 15.x 正常，而 macOS 26.3 异常，则可以基本确认：

```text
问题集中在 macOS 26.3 的系统级真机调试链路，
不是项目代码本身不支持 iOS 12。
```

## 九、风险和注意事项

### 主要风险

- 选错安装目标，可能覆盖当前系统。
- 证书、描述文件、钥匙串需要重新配置。
- 内置硬盘空间不足会导致安装失败。
- 如果 Mac 出厂系统高于 macOS 15，无法安装 macOS 15。
- 精确的 macOS 15.6 不一定还能从 `softwareupdate` 下载。

### 建议

- 操作前备份当前主系统的重要资料。
- 安装时反复确认目标卷是 `macOS-15-Test`。
- 优先下载 Apple 当前提供的 macOS 15.x。
- 如果必须完全复刻同事环境，再尝试寻找 Apple 官方 macOS 15.6 下载来源。
- 不要使用第三方 macOS 安装器。

## 十、如果要删除这个测试系统

确认不再需要 macOS 15.x 后：

1. 启动回 macOS 26.3 主系统。
2. 打开磁盘工具。
3. 选择 `macOS-15-Test` 卷。
4. 点击删除卷。

注意：只删除测试卷，不要删除 APFS 容器，也不要删除当前主系统卷。

## 参考资料

- Apple：How to download and install macOS  
  https://support.apple.com/en-us/102662

- Apple：Create a bootable installer for macOS  
  https://support.apple.com/en-us/101578

- Apple：macOS Sequoia 15 compatible computers  
  https://support.apple.com/en-us/120282
