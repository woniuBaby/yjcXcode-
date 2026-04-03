2026年03月02日~2026年03月06日

星期一

1.crash日志上传,性能打印

```objc
测试机 iPhone 12 设备id AEC5E7B4-701D-423C-A8E8-FC6A77E59E1D    

NSMutableString *baseURL = [NSMutableString string];
    // 最优域名
    NSArray *bestUrls = [[NSUserDefaults standardUserDefaults] valueForKey:kNTSDKCDNList];
    if (bestUrls && bestUrls.count >0) {
        baseURL = bestUrls.firstObject;
    } else {
        baseURL = [[NSUserDefaults standardUserDefaults] objectForKey:kSDKCDNCur];
    }
//    NSString *baseURL = @"https://apitest.babigame.cn";
			
```

2.    1.1.8(2)打包上架
3. 内存监测警告  日志上传(test/正式)

星期二
开始游戏crash
设备总结:

```
1. iPad Air 11 M3 26.3     
   iPad Air 11英寸 （M2) 26.3
2.iPhone13ProMax 设备id: 30B50F31-3BFD-436B-8C73-BAFFD0CE8BE0   反馈多次
3.iPhone12ProMax 18.6  7F6DE6C0-F236-4DE1-B107-EB1D4557F15B
4.iPhone16pro，18.5的系统 SDK openid:176370027963173520689110
5.iPhone13Pro   F2AA42E1-17B0-452C-A8C5-50E34A7E46C7 角色显示ID: ys09mpn00xub
```

星期三

1. pod 'AnyThinkGDTSDKAdapter','6.4.92.2' 

2. 内网 [gitlocal.modo.cn](ssh://git@gitlocal.modo.cn:50322/sdk/reconciliation)     外网 [git.modo.cn](ssh://git@gitlocal.modo.cn:50322/sdk/reconciliation)
2. 1.1.8(4)上架    审核crash  回退激励视频   优量汇的SDK

星期四

1.问题查询 广告

```
D019368A-3C0B-40FA-AFD3-F57EDC95AA7D

角色显示ID: ys06rzvm9gas
角色ID: 19120591101124
角色名称: s1124.段悦颖
角色区服: 正式1124服
角色VIP等级: 4
渠道: 官方渠道
客服系统ID: 1613850922154541321
IP: 110.187.122.10
玩家反馈点进去看视频之后又回到了原页面，无法看完激励视频，加了白名单重启了
```

2. 灵动岛 有关查询 **let** list = Activity<ActivityAttributesModel>.activities
   ```
   位到崩溃原因：swift_getTypeByMangledNameInContext 在从 Objective-C 调用 Swift 并首次使用泛型 Activity<ActivityAttributesModel> 时，在非主线程（堆栈显示来自 WebSocket 回调）解析类型元数据会失败。将 Activity<ActivityAttributesModel>.activities 的访问放到主线程执行，以避免在非主线程解析类型元数据。
   
   把所有对 Activity<ActivityAttributesModel>.activities 的访问都放到主线程执行（例如在 quaryActive 里用主线程做实际查询，再返回 NSDictionary）。
   ```

3. 灵动岛 服务端相关处理

4. 灵动岛  权限判断后再做图片处理

星期五

1.系统升级/xcode升级

```
'-Wl,-ld_classic'
```

2.灵动岛 模拟配置推送
3.问题查询

```
AC631493-F106-4534-AA22-E8AFE0FBA9F9
```

---

2026年03月09日~

星期一 
1.问题查询

```
1613850922154722169  开始闪退

玩家反馈游戏闪退，添加了日志重试了还是不行，麻烦看下  1613850922154722169
```

查到原因

星期二
```
D5F7AE94-6271-484B-867B-A1079C9CA36B  内存压力过大
```

上架 1.1.9  

星期三
1.灵动岛远程推送(时间戳不能小于当前时间)

肯定可用
```
{
    "aps": {
        "timestamp": 1773303994,
        "event": "update",
        "content-state": {
            "ID": "remote_push_1",
            "nameStart": "牡丹花啊",
            "nameEnd": "牡丹花",
            "btnTitleStart": "前往花园",
            "btnTitleEnd": "去收花",
            "countdownTime": 0,
            "tipStart": "倒计时",
            "tipEnd": "熟了",
            "titleStart": "茁壮成长",
            "titleEnd": "可售火",
            "subtitleStart": "距离成熟{$time}",
            "subtitleEnd": "快去收花吧",
            "startImage": "https://hygncdn.babigame.cn/ios_island/flowerMature_countdown.png",
            "endImage": "https://hygncdn.babigame.cn/ios_island/flowerMature_countdown.png"
        },
        "attributes-type": "ActivityAttributesModel"
    }
}
```

```
{
    "aps": {
        "timestamp": 1773307380,
        "event": "update",
        "content-state": {
            "ID": "remote_push_1",
            "nameEnd": "牡丹花1",
            "btnTitleEnd": "去收大声道花",
            "countdownTime": 0,
            "tipEnd": "熟实打实",
            "titleStart": "茁壮成长",
            "titleEnd": "可售火",
            "subtitleEnd": "快去收花吧123",
            "endImage": "https://hygncdn.babigame.cn/ios_island/flowerMature_countdown.png"
        },
        "attributes-type": "ActivityAttributesModel"
    }
}
```





问题待解决:验证    swift 是否可以获取

```
-//@available(iOS 16.1, *)
+/// 与主 App 一致，用于从 UserDefaults 中按图片 URL 查本地路径。需与 APP 侧 group.包名 一致。
+@available(iOS 16.1, *)
+public enum LiveActivityStorage {
+    /// 推导 groupId：group + 主APP bundle id。当前 bundle = 灵动岛 = 主APP bundle id + 灵动岛项目 name，去掉最后一段即为 主APP bundle id；去掉后为空则无法推导返回空。
+    public static var appGroupSuiteName: String {
+        guard let bid = Bundle.main.bundleIdentifier, !bid.isEmpty else { return "" }
+        let parts = bid.split(separator: ".")
+        let mainAppID = parts.dropLast().joined(separator: ".")
+        return mainAppID.isEmpty ? "" : "group." + mainAppID
+    }
+}
```

星期四/五

实现更新

星期六
1.删除无效无用图片,图片迁移-->bundle到assets
每次更换图片态度哦地方都需要去改

----





2026年03月16日~ 2026年03月20日

星期一

1. 支付异常查询/参数缺失查询/
2. 灵动岛远程推送 倒计时 无法显示 ....tipstart 参数中 需要添加{$time}

星期二

1. 剥离 公共 model,新增coredata SDK

2. 注册监听 token

   ```objc
   成功
   [self emitEvent:@"fbShareEvent" withData:[Plugin_callback_bean plugin_callback_beanDicStatus:@"success" Code:@"" Message:@""]];	
   
   失败
   [self emitEvent:@"fbShareEvent" withData:[Plugin_callback_bean plugin_callback_beanDicStatus:@"fail" Code:@"" Message:kFacebookShareContentLoadFail]];
   ```

   

星期三

1. 异步token获取后,通知传给APP,APP添加服务端对token的监听
2. 与后台联测,本地推送 成功

星期四

1. RN环境搭配

星期五

1.1.10 (上传tf) 灵动岛代码优化

---

2026年03月23日~2026年03月28日

星期一
与服务端/推送端 联调灵动岛远程推送



星期二



星期三

1. 7A67018D-EA56-45F9-948D-122AABC9B35F  玩家关闭灵动岛  卡顿 问题查询

2. 海外添加灵动岛







星期四

1. 灵动岛远程更新 后无法本地更新

2. cursor的报销单大家记得统一上传到表格上去，3月份之前的（包含3月的），过几天我要统一提上去了。



星期五

1.查找远端灵动岛更新,本地更新需等待1分钟后才能更新

2.wx小礼物



星期六



---

2026年03月30日

1.getEntryInfo
2.证书:正式环境

星期二

1. 设备id,为空/改变
2. crash 问题查询,数量增多
