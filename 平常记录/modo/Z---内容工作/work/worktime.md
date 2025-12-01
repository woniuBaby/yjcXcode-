2025年10月22日

```
1.liveactivity 进度条处理,无法实现实时更新,Apple不允许切date/定时器/相关计数都禁止使用,只可使用官方 progress 与 text 的倒计时,,
2.官方progress/text 有进度动画,倒计时,但是无 百分比显示以及 各种计算无法实现,
3.监听  liveactivity 活动,暂时限制只显示一个,更新/停止
4.网络图片加载尝试  将图片转连接(https://easylink.cc/?ref=openi.cn)/大小限制4kb
   结果: 无法实现在 实时屏幕 下载图片...因为Live Activity（灵动岛和锁屏实时活动）运行在一个独立的、受限的环境中，无法直接发起网络请求
   解决方法: 1.主应用下载 2,通知推送  但是 大小限制到4KB下
   
```

2025年10月23日

1.GravityEngineSDK报错

pod 'MDGravityEngineSDK',:git =>"ssh://git@192.168.13.252:8022/cjx/mdgravityenginesdk.git"

更换为 

pod 'GravityEngineSDK' # 引力引擎     

```objc
/Users/lin/Documents/原生能力/modo-native-ability-ios/NativeBility/NativeBility/nt+ability/Plugin/Report/PluginAdapter_gravityReport.m:105:15 No visible @interface for 'GravityEngineSDK' declares the selector 'initializeGravityEngineWithAsaEnable:withClientId:withCaid1:withCaid2:withSyncAttribution:withChannel:withSuccessCallback:withErrorCallback:'

```

2. 灵动岛SDK  导入

   ```objc
   尝试:  
   #  pod 'ActivitySDK', :path => '/Users/lin/Documents/原生能力/modo-native-ability-ios/ActivitySDK' #灵动岛SDK
     project "../../modo-native-ability-ios/ActivitySDK/ActivitySDK.xcodeproj"
   ```

   尝试本地SDK

   .podspec 文件

   cd ~/../../ActivitySDK
   pod spec create ActivitySDK

   ````
   Pod::Spec.new do |s|
     s.name         = 'NativeAbility'
     s.version      = '1.0.0'
     s.summary      = '原生能力封装 SDK'
     s.homepage     = 'https://example.com'
     s.license      = 'MIT'
     s.author       = { 'lin' => 'lin@example.com' }
     s.source       = { :path => '.' }
     s.source_files = 'ios/**/*.{h,m,swift}'
     
     # ✅ 依赖声明在这里
     s.dependency 'AFNetworking', '~> 4.0'
   end
   ````

   

3.待做 灵动岛 通知事件

与APP推送区别:

| 项目                | Live Activity 更新通知                       | 普通 App 通知                        |
| ------------------- | -------------------------------------------- | ------------------------------------ |
| **目的**            | 更新灵动岛 / 锁屏上的 Live Activity 显示内容 | 触发 App 消息通知、显示系统推送      |
| **通道类型**        | **ActivityKit Push（专属通道）**             | **APNs 通知通道（普通推送）**        |
| **是否唤醒 App**    | ❌ 不会唤醒 App                               | ✅ 可后台唤醒 App（根据类型）         |
| **用户可见性**      | ✅ 更新灵动岛 / 锁屏 Widget                   | ✅ 弹出通知中心消息                   |
| **推送 Token 来源** | 来自 `Activity<Attributes>.pushTokenUpdates` | 来自 `UNUserNotificationCenter` 注册 |
| **服务端推送格式**  | 特定的 JSON（含 `content-state` 字段）       | 普通 APS JSON                        |
| **系统解析者**      | iOS 系统（ActivityKit Framework）            | 通知服务 / AppDelegate               |
| **使用前提**        | 用户主动启动了一个 Live Activity             | App 获得推送权限                     |

```
🟢 普通 App 推送（APNs 通知）
Server → APNs → AppDelegate(didReceiveRemoteNotification) → 弹窗/逻辑处理

🟣 Live Activity 更新通知（ActivityKit 通道）
Server → APNs (ActivityKit channel) → 系统解析 → 更新灵动岛显示UI


注意：这里 不会走 App 的任何回调，系统直接更新。
```



## Live Activity 更新通知：完整流程（服务端传输）

```objc
1. 🧩  客户端创建 Live Activity
let activity = try? Activity.request(
        attributes: attributes,
        contentState: initialState,
        pushType: .token
    )
    for await tokenData in activity?.pushTokenUpdates ?? [] {
        let token = tokenData.map { String(format: "%02x", $0) }.joined()
        print("🟢 Push Token: \(token)")
        // 👉 将此 token 上报给服务端
        uploadActivityPushTokenToServer(orderId: "123456", token: token)
    }
    
2. 🧩  服务端保存 push token

每个 Live Activity 实例都有一个唯一 push token。
服务器要保存这个 token，后续用它来发更新请求。

3. 🧩  服务端通过 APNs 调用 ActivityKit 推送接口
✅ URL 格式：
https://api.push.apple.com/3/device/<activity_push_token>
与普通推送不同，这里的 <token> 是 Activity 推送 token。

✅ 请求头：
apns-topic: <你的 App Bundle ID>.push-type.liveactivity
apns-push-type: liveactivity

✅ 请求体 (JSON) 示例：
{
  "aps": {
    "timestamp": 1731440000,
    "event": "update"
  },
  "content-state": {
    "progress": 0.6,
    "status": "骑手已取餐"
  },
  "alert": {
    "body": "骑手正在前往目的地"
  }
}

说明：
"aps.event": "update" 表示更新活动
"content-state" 对应 Swift 中定义的 ContentState
"alert" 可选（系统可显示通知提示）

4. 🧩  iOS 系统自动解析并更新显示
无需 App 参与，系统会：
更新灵动岛 / 锁屏 Live Activity 的 UI
如果加了 "alert" 字段，会弹出轻量提示

5.🧩  服务端结束 Live Activity（可选）
如果订单完成、活动结束：
{
  "aps": {
    "timestamp": 1731443600,
    "event": "end"
  },
  "content-state": {
    "progress": 1.0,
    "status": "已送达"
  },
  "alert": {
    "body": "订单已完成"
  }
}
系统自动移除 Live Activity。
```



2025年10月24日

```
2.SDK未全量启动(客户端问题)
原理说明:融合归因的逻辑需要广告主所有可能给字节回传转化的apk包(包括渠道包/商店包)的激活事件均通过
sdk进行上报。
问题说明:【SDK未全量启动】披露的是广告主回传至字节的所有激活转化中未进行sdk上报的case。例如昨日广告
主共回传了100个激活至字节媒体,但只有90个进行了sdk上批最,其余10个未上报的即为【SDK未全量启动】的典型
case。
常见原因:1)广告主未进行全渠道发版更新,比如部分商店包遗漏,建议检查未上报设备的商店渠道信息看是否
同属于某个渠道,完成该渠道携带sdk的版本发版;2)存量的老版本(未携带SDK)新用户影响,建议检查未上报
的设备是否都属于老版本,一般自然用户覆盖会逐步提升,可以尝试给老用户推送更新app通知加速携带sdk的app
版本覆盖;3)idfv不是最新的。iOS场景下某些客户会把idfv保保存在钥匙链中,后续优先从钥匙链中读取,如果用户
卸载重装会拿到钥匙链存的旧idfv,而sdk采集的是新的,这种会导致上报激活的idfv和sdk采集的不一致。解决办
法:实时获取最新的idfv即可;4)sdk某些情况没有初始化,需要保证每次打开都会初始化sdk。
```

| 序号 | 文档                                                         | 内容                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1    | 1.1 SDK集成<br/>			pod  'BDASignalSDK' <br/>			pod  'Protobuf | pod 'BDASignalSDK'                                           |
| 2    | 1.2 SDK使用方式(必要)<br />启动事件/其他事件上报             | #if OceanReportSupport<br/>    [BDASignalManager didFinishLaunchingWithOptions:launchOptionsconnectOptions:nil];<br/>#endif |
| 3    | 建议:<br /><br />1.全渠道发版更新<br /><br />2.存量的老版本,app推送通知更新<br /><br />3.idfv不是最新的<br /><br />4.sdk某些情况没有初始化 | 3.[BDASignalManager enableIdfa:YES];                         |
| 4    | <br />开启延时上报 <br/>+ (void)enableDelayUpload;<br/> 允许上报，配合上方开启延时上报功能使用<br/>+ (void)startSendingEvents;<br /><br />SDK其他能力(可选) |                                                              |
| 5    |                                                              |                                                              |



```

#if OceanReportSupport
    [BDASignalManager didFinishLaunchingWithOptions:launchOptionsconnectOptions:nil];
#endif
```

2025年10月27日

1.SDK  build后framework位置

```
/Users/lin/Library/Developer/Xcode/DerivedData/ActivityWorkSpace-ferjoubqrlkvbtgzckdjirgmvsvm/Build/Products 
```

----

> 2025年11月17日~ 2025年11月22日~

一: twitter/line  分享

二:  

1.entry 引入  bug 可搜集 --->存放在SDK已经完成
2.订阅中问题排查

``` 
#pragma mark - <SKPaymentTransactionObserver>
- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray<SKPaymentTransaction *> *)transactions 

SKPaymentTransactionStatePurchased  苹果服务没3分钟返回数据,若不进行订单finis,一条订单或重复叠加至几百条导致启动或者支付时候 内存暴增登crash
```

三.

1. 继续订阅(问题:连续订阅会(每次都会将订单finish)导致Apple服务器返回一大堆订单(上百次也可能))
2. 灵动岛验证APP杀死后,重启后是否还可以获取/操作原灵动岛 -->可以
   之前之所以无法实现,代码中activity 创建了对象,APP杀死后对象释放,所以无法查到,现在进行遍历方式 查询

四.

1.灵动岛 SDK 修改:由时间戳改为倒计时,通过倒计时大小判断 状态是否 种植中/已成熟

2.新增 检测权限/去设置/是否存在LA/获取LA数据  接口

3.

五六: entry  上传符号表

---

> 2025年11月24日~28日

一. 订阅支付无法/entry  上传符号表

二.  

1. 广告无法点击播放排查
2. iOS   sentrySDK无法符号化解析bug内容
   https://modoglobal.feishu.cn/wiki/TJ69wmDZOiQ1HskiBn5cnAs3n1g
3. 打包上传 我的花园   1.1.1 (1/2/5)版本

三。 
1.[[NSUserDefaults standardUserDefaults] setValue:mode forKey:kNT_entry_mode_key];

​      [[NSUserDefaults standardUserDefaults] setValue:domainMode forKey:kNT_entry_domain_key];

​      [CdnListManage clearSdkUrlCache];

​	exit(0);

星期四:

日志:
可获取: MDDebugLog(@"🐮🐮🐮🐮 请求情况：url ---------- %@ \n responseObject ---------- %@ \n",URLString,responseObject);

需替换: NSLog(


\+ (NSDictionary *)getCaidReqData;;

#if DEBUG
    NSLog(@"当前是 Debug 模式");
#else
    NSLog(@"当前是 Release 模式");
#endif



 NativeBility_PCH  修改版本号

\#define NativeBilityVersion @"2.0.3.11"

\#define RNVersion @"3.3.2.1"

```objective-c


 获取推送deviceToken

\+ (NSString *)getDeviceToken{

  **return** [kUserDefaults valueForKey:@"PLUGIN_PUSH_DEVICE_TOKEN"];
}

/**

 获取deviceID

 */

\+ (NSString *)getDeviceId{

  **return** [LBKeychain KW_getDeviceID];

}

/**

 获取当前的网络状态

 */

\+ (NSString *)getDeviceNetworkType
  /**
* 获取手机型号
*/
+ (NSString *)getPhoneModel
  /**
 获取idfa
 */
+ (NSString *)getIdfa{
    NSString *idfa = [DeviceUtil idfa];
    if (ex_isEmpty(idfa) || [idfa containsString:@"0000"]) {
        idfa = nil;
    }
    return idfa;
}

/**
 获取idfv
 */
+ (NSString *)getIdfv{
    return [DeviceUtil idfv];
}
/**
 获取屏幕密度
 */
+ (CGFloat)getScreenDensity {
    // 获取当前屏幕的UIScreen对象
    UIScreen *screen = [UIScreen mainScreen];
    // 获取屏幕的缩放因子
    CGFloat scale = screen.scale;
    
    return scale;
}
```



星期五:易盾风控sdk 研究

> 2025年12月01日~2025年12月06日



















