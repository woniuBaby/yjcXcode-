```
.podspec 文件

cd ~/../../TencentGDTActionSDKMoDo
pod spec create TencentGDTActionSDKMoDo
```

```
 pod 'TencentGDTActionSDKMoDo',:git =>"ssh://git@192.168.13.252:8022/modo_ios_public/TencentGDTActionSDKMoDo.git"#腾讯广告2.1.2
```

`文档:` https://datanexus.qq.com/doc/develop/guider/sdk/ios/init

`官网:`https://doc.weixin.qq.com/doc/w3_AZoArQY_ADoCNmxN55au6R3az0dEW?scode=AJEAIQdfAAoNFetdVdANgA7gZHACg

```
数据源ID：1218291294
access_token：4156808edac3ad4166085dcc6d1a535a
secret_key：909bb1e7f80bac180025c07a7e407507
https://datanexus.qq.com/web/datasource
QQ登录，945217674    密码：200240zhf
datanexus.qq.com

DataNexus|腾讯广告
首页 文档中心 反馈工单 一站式数据接入和资产管理平台 接入便捷 资产沉淀 应用提效 价值可见 立即登录
账户ID: 42278582


```

`通用callback`

```objc
NSDictionary *dataDict = configDict[PluginDataDict];
__block Callback *callBack = configDict[PluginCallBack];
//成功
[callBack sucessWithStatus:Result_Success];
//失败
Msg_report *error = [Msg_report msgWithCode:[NSString stringWithFormat:@"0"] arg:@{@"describe":@"TencentGDT send 行为未识别"}];
[callBack failWithMsg:error];
```









```
interface IOpt_gdt_action_init {
    /** 数据源ID */
    userActionSetId: string;
    appSecretKey: string;
    /** 渠道 */
    channelType?: number;
    /** 自定义渠道id */
    channelId?: string;
    /** 关闭采集，默认关闭，设置false不采集 */
    anidEnable?: boolean;
}
interface IOpt_send_gdt_action<T extends GDT_TYPE, D extends IOpt_gdt_data_register | IOpt_gdt_data_login | IOpt_gdt_data_purchase> {
    /** 事件类型 **/
    gdtEventType: T;
    /** 事件数据 **/
    gdtData?: D;
}
const enum GDT_TYPE {
    /** 应用启动 **/
    startApp = "startApp",
    /** 注册 **/
    register = "register",
    /** 登录 */
    login = "login",
    /** 付费 */
    purchase = "purchase"
}
interface IOpt_gdt_data_register {
    /** 默认：Mobile，其他：QQ，微信，TAPTAP账号 */
    method: string;
    /** 是否注册成功 */
    success: boolean;
}
interface IOpt_gdt_data_login extends IOpt_gdt_data_register {
}
interface IOpt_gdt_data_purchase {
    /** 装备 */
    type: string;
    /** 商品名称 */
    name: string;
    /** 商品标识符 */
    id: string;
    /** 商品数量 */
    number: number;
    /** 支付渠道名，如支付宝、微信等 */
    channel: string;
    currency: string;
    /** 支付真实金额，单位：分 */
    value: number;
    /** 支付是否成功 */
    success: boolean;
}
```



> 相关代码

```objective-c
//腾讯广告SDK
#if __has_include("GDTAction.h")
#import "GDTAction.h"
#import "GDTAction+convenience.h"
#define TencentGDTActionSDKMoDoSupport 1
#endif
#import "PluginAdapter_TXgdtReport.h"
/// - Parameter configDict: 参数
- (void)init:(NSDictionary *)configDict{
    
    MDDebugLog(@"PluginAdapter_TXgdtReport ------init------- %@",configDict);
    NSDictionary *dataDict = configDict[PluginDataDict];
    __block Callback *callBack = configDict[PluginCallBack];
    GDTActionInitOptions *optInit = [GDTActionInitOptions mj_objectWithKeyValues:dataDict];

#if TencentGDTActionSDKMoDoSupport
    NSString *TencentGDTActionID = optInit.userActionSetId;
    NSString *TencentGDTSecretKey = optInit.appSecretKey;
    if (ex_isValidString(TencentGDTActionID) && ex_isValidString(TencentGDTSecretKey)) {
        /* 2.1.2
         * 请在App启动的时候调用初始化方法
         * 方法入参为步骤2获取的数据源ID和密钥
         */
        [GDTAction init:TencentGDTActionID secretKey:TencentGDTSecretKey];
        
        /*
         * SDK版本号>=2.1.0才需要调用start方法
         * 调用start方法前，请先调用init方法初始化SDK
         */
        [GDTAction start];
        [callBack sucessWithStatus:Result_Success];
    }
    else {
        Msg_report *error = [Msg_report msgWithCode:[NSString stringWithFormat:@"0"] arg:@{@"describe":@"请检查 腾讯广告SDK 数据源ID和密钥 TencentGDTActionID TencentGDTSecretKey 是否设置"}];
        [callBack failWithMsg:error];
        NSAssert(false, @"请检查 腾讯广告SDK 数据源ID和密钥 TencentGDTActionID TencentGDTSecretKey 是否设置");
    }
#endif
}


//下面是action埋点
- (void)application:(UIApplication *)application continueUserActivity:(nonnull NSUserActivity *)userActivity restorationHandler:(nonnull void (^)(NSArray<id<UIUserActivityRestoring>> * _Nullable))restorationHandler { 
    NSURL *url = userActivity.webpageURL;   
     if (url) {
#if TencentGDTActionSDKMoDoSupport
         /* 腾讯广告SDK
          * 在应用被呼起的时候请上报GDTSDKActionNameStartApp行为，并带上呼起的webpageURL链接
          */
         [GDTAction logAction:@"START_APP" actionParam:@{@"open_url": url.absoluteString}];
#endif
     }
     
- (void)applicationDidBecomeActive{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
#if TencentGDTActionSDKMoDoSupport
    /* 腾讯广告SDK 埋点
      * 在应用启动的时候请上报GDTSDKActionNameStartApp行为
      * SDK内部会判断此次启动行为是否为激活行为并上报，开发者无需另外作判断逻辑
      */
     [GDTAction logAction:@"START_APP" actionParam:@{}];
#endif
    });
}

- (BOOL)thirdWithApplication:(UIApplication *)application openURL:(NSURL *)url{
#if TencentGDTActionSDKMoDoSupport
    /*腾讯广告SDK 埋点
     * 在应用被呼起的时候请上报GDTSDKActionNameStartApp行为，并带上呼起的openURL链接
     */
    [GDTAction logAction:@"START_APP" actionParam:@{@"open_url":url.absoluteString}];
#endif
	return YES;
}

```

```
参数
    NSArray *txParmsArray = @[
        //  初始化事件
        @{
            @"userActionSetId" : @"1218291294",
            @"appSecretKey" : @"909bb1e7f80bac180025c07a7e407507"
        },
        //  启动事件
        @{
            @"gdtEventType" : @"startApp",
            @"gdtData" : @{
            }
        },
        //  注册事件
        @{
            @"gdtEventType" : @"register",
            @"gdtData" : @{
                    @"method"  : @"Mobile",
                    @"success" : @(YES)
            }
        },
        //  登录事件（继承 register）
        @{
            @"gdtEventType" : @"login",
            @"gdtData" : @{
                    @"method"  : @"WeChat",
                    @"success" : @(YES)
            }
        },
        //  付费事件
        @{
            @"gdtEventType" : @"purchase",
            @"gdtData" : @{
                    @"type"     : @"装备",
                    @"name"     : @"传说宝剑",
                    @"ID"       : @"item_001",
                    @"number"   : @(1),
                    @"channel"  : @"微信支付",
                    @"currency" : @"CNY",
                    @"value"    : @(600),   // 单位分
                    @"success"  : @(YES)
            }
        }

    ];

```

