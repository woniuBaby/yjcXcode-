# App 被强杀 / 长久挂后台被系统杀掉

玩家从多任务划掉，或挂后台太久被系统回收时，Organizer 会记下一堆「crash」。其中一大半是进程已经被杀掉后的误报；另一部分才是进后台前后游戏还在跑、踩了空对象。

给原生和游戏一起看。下面「现状」= 工程里现在已经怎样；「建议补」= 现在还没有、准备新写；「继续空着」= 现在没写，也别往那里加。

## 1. 分类：系统杀掉 vs 游戏自己崩了

先对号，再决定改不改代码。

|  | A. 强杀 / 系统回收后的误报 | B. 游戏自己崩了（要修） |
| --- | --- | --- |
| 实际发生了什么 | 玩家划掉 App，或挂后台太久（内存紧张、系统回收）。进程已经被系统杀掉。 | 进程还活着。某个 Spine / 贴图已经没了，代码还去 update / 上传，于是真的崩了。 |
| 玩家会不会感觉「闪退」 | 多数不会。划掉或挂太久被回收，是系统正常行为。 | 会。前台，或刚切到后台时，游戏直接没了。 |
| 栈上常见名字 | `UIEventFetcher`、`NO_CRASH_STACK`、UIKit 空符号、`CFRunLoop`（有的带 `0xdead10cc`）、`Semaphore::wait` | Spine `binarySearch`、贴图 `uploadData` / `doBufferTextureCopy` |
| 系统给的类型 | `EXC_CRASH`，codes `0x0/0x0`，或 SIGKILL | 前台 `EXC_BAD_ACCESS`（空指针） |
| 要不要改代码 | 大部分不用专门排期。改了 Organizer 里这类条目也消不掉。<br>唯一值得补的是 `0xdead10cc`（挂后台时还握着文件锁，系统回收时标这个）。 | 要补。进后台、销毁节点后还在发任务，才会走到这条。 |

**数量（只贴量大、能说明问题的）**　设备数为该版本 Top 条目量级，条目间可能有同一设备重复。1.1.10 / 1.1.11 为 Last Year；1.1.20 / 1.1.22 为近两周。

| 类别 | 代表栈 | 1.1.10（年） | 1.1.11（年，峰值） | 1.1.20（近两周） | 1.1.22（近两周） |
| --- | --- | --- | --- | --- | --- |
| A 强杀 / 系统回收误报 | `UIEventFetcher` / 无栈 / RunLoop 等合计 | 占 Top **40%**<br>5423 / 13480 | 占 Top **42%**<br>11053 / 26583 | 占 Top **43%**<br>5670 / 13142 | 占 Top **56%**<br>35 / 62 |
| B Spine | `spine::Animation::binarySearch` | **4709**（该版第 1） | **9423**（该版第 1） | **3767**（该版第 1） | **15**（该版仍第 1） |
| B 贴图 | `copyBuffersToTexture` / `doBufferTextureCopy` | 667 | 1813 + 1106 | 928 + 957 | 量小，未进结论主因 |

读法：A 类大约占 Top 四成到一半，条数多不等于玩家闪退。B 类 Spine 从 1.1.10 到 1.1.22 一直是可归因第 1；贴图是同一条链上的第二块。后文第 3 节只处理 A，第 4 节只处理 B。

## 2. 停任务要卡在「进后台」，不是「被杀掉那一下」

强杀和长久挂后台被回收，游戏都收不到「即将退出」。能跑代码的只有进后台那一下。

| 玩家在干什么 | 游戏还能不能跑代码 | 停任务写这里行不行 |
| --- | --- | --- |
| 按 Home、切到别的 App、来电话（刚进后台） | 能。Cocos 会发「进入隐藏」`EVENT_HIDE`。原生也还能跑一小段。 | **行。**停引擎、停 Spine、丢掉过期网络回调，都放这里。 |
| 从多任务划掉（强杀） | 通常不能。App 多半已经睡着，系统直接杀进程，**没有「即将退出」给脚本挂钩**。 | 写了也执行不到。 |
| 挂后台太久，被系统回收 | 同样不能。系统直接回收进程，也没有退出回调。 | 写了也执行不到。能做的，还是进后台时就把任务停掉、把文件锁放掉。 |

**闸门 = 刚进后台那一下。**后面无论是划掉还是被系统回收，都只是杀进程，不是游戏的退出回调。

**现状（原生）：**`applicationWillTerminate`（iOS「即将退出」）在 `AppDelegate.mm` 里已经是空的，里面的调用都被注释掉了。

**继续空着：**别把停游戏、pause、关 Spine 填进这个空方法。强杀和系统回收都走不到这里。crash 栈里看不到它是正常的。

## 3. A 类怎么办（强杀 / 系统回收误报）

**现状：**Organizer 里大量落在 `UIEventFetcher` / `NO_CRASH_STACK` / UIKit 空栈。工程里没有对应的业务代码可改——进程已经被杀掉了。

**继续空着：**不用专门写「修复 Fetcher / 无栈」的补丁，也没有回调可挂。A 类条数下降也不能当这次改动成功。

**建议补（仅这一种）：**挂后台时还握着 SQLite / 文件锁，系统回收时会标 `0xdead10cc`。进后台把锁放掉。当前 `DidEnterBackground` 里还没有这步。

```
- (void)applicationDidEnterBackground:(UIApplication *)application {
    [[SDKWrapper shared] applicationDidEnterBackground:application];
    // 建议补：关掉 SQLite / 文件句柄 / 跨进程锁
}
```

## 4. B 类怎么改（进后台前后游戏自己崩了）

B 类是「节点 / buffer 已经没了，代码还在 update」。原生停引擎帧，游戏停动画和过期回调，两层都要有。

### 4.A 原生：进后台把引擎停住

**改哪里：**`native/engine/ios/AppDelegate.mm`

**现状已有：**失活时 `applicationWillResignActive` 已经调了 `appDelegateBridge`，引擎 pause 多半在这。

**建议补：**进后台 `applicationDidEnterBackground` 现在只调了 `SDKWrapper`，还没调 `appDelegateBridge`。回前台 `WillEnterForeground` 同样还没调 bridge，需要成对 resume。

```
- (void)applicationWillResignActive:(UIApplication *)application {
    [[SDKWrapper shared] applicationWillResignActive:application];
    [appDelegateBridge applicationWillResignActive:application]; // 现状已有
}

- (void)applicationDidEnterBackground:(UIApplication *)application {
    [[SDKWrapper shared] applicationDidEnterBackground:application];
    [appDelegateBridge applicationDidEnterBackground:application]; // 建议补
}

- (void)applicationWillEnterForeground:(UIApplication *)application {
    [[SDKWrapper shared] applicationWillEnterForeground:application];
    [appDelegateBridge applicationWillEnterForeground:application]; // 建议补
}
```

若 Bridge 没有 background 方法：在 Cocos 线程 `evalString("cc.game.pause()")`，回前台再 `resume()`。这些补在进后台，不填进第 2 节那个已经空着的退出方法。

### 4.B 游戏：隐藏时停任务，销毁后丢回调

**建议补：**启动时注册一次全局隐藏/显示；带 Spine 的组件销毁时先停动画；WebSocket / 下图回调里先判断节点还在不在。这些目前不是「已经写了别动」，而是准备新写。

```
// 全局，启动时注册一次
cc.game.on(cc.Game.EVENT_HIDE, () => { cc.game.pause(); });
cc.game.on(cc.Game.EVENT_SHOW, () => { cc.game.resume(); });

// 带 Spine 的组件
onDestroy() {
    this.skeleton?.clearTracks();
    this.skeleton?.paused = true;
    this.unscheduleAllCallbacks();
}

// 网络 / 贴图回调
onMessage(data) {
    if (!cc.isValid(this.node) || cc.game.isPaused) return;
    // 再 update / uploadData
}
```

B 类典型链：`WebSocket onMessage` → JS → `SkeletonAnimation.update` → `binarySearch`。原生 pause 停不掉这条异步回调，游戏侧要自己拦截。

## 5. 对照：已经怎样，准备怎样

| 点 | 现状 | 准备怎么做 |
| --- | --- | --- |
| 「即将退出」方法 | 已经空着（调用被注释掉了） | 继续空着。强杀和系统回收都走不到。停游戏写在进后台。 |
| A 类 Fetcher / 无栈 | 没有对应业务代码 | 不新写修复。当强杀 / 系统回收误报统计。 |
| 进后台通知引擎 | ResignActive 已有；DidEnterBackground 还没有 | 建议补，见 4.A |
| 游戏 HIDE / 销毁 / 网络回调守卫 | 还没有按 B 类链补全 | 建议补，见 4.B。原生和游戏都要补，只改一层仍会空指针。 |

## 6. 单机怎么验收

一台真机、Xcode 连着打，当场就能判断。Organizer 上传延迟常以天计，改完当天看不到，**不当作这次的验收手段**。发版后过几天再看趋势即可。

| 要确认的事 | 本机怎么看（立刻） |
| --- | --- |
| 进后台有没有停住 | Xcode 跑真机。按 Home 后，原生日志应立刻出现 ResignActive / DidEnterBackground；游戏日志应立刻出现 `EVENT_HIDE` 和 `pause`。<br>再划掉 App，或挂很久等系统回收：通常看不到「即将退出」日志，这是正常的，不代表没改到。 |
| B 类还会不会崩 | 用会复现的场景：进战斗 / 有 Spine 的界面 → 进后台 → 此时仍可能收到 WebSocket → 再回前台或切场景销毁节点。<br>改前：Xcode 会直接停在 `EXC_BAD_ACCESS`（Spine / 贴图）。<br>改后：`onMessage` 应打到 `return`，不再进 `update` / `uploadData`；Xcode 不再因这条崩。 |
| 节点毁了回调还在不在跑 | 在 `onMessage` 第一行打日志：`cc.isValid(this.node)`、`cc.game.isPaused`。销毁或 pause 后必须是「直接 return」，不能再碰 Spine。 |
| 本机已经崩过一次 | Xcode 当时就会停住。也可到手机 **设置 → 隐私与安全性 → 分析与改进 → 分析数据** 看当天的 `.ips`，比 Organizer 快很多。 |
| A 类条数 | 单机看不出来。发版后过几天再扫 Organizer 趋势。A 类 Fetcher 还在，不说明这次失败——强杀和系统回收本来就会继续上报。 |

若接了 Crashlytics 一类近实时上报，可比 Organizer 快，但仍不是秒级。本次验收以 Xcode 当场是否还踩空指针为准。
