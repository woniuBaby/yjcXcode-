> 2025年12月01日~2025年12月06日

一.riskperception.  PluginAdapter

二,

1. 我的花园世界  1.1.1 build 6 国内iOS包体替换联动icon+加载页
2. 日志分类 waring/error
3. 易盾sdk 插件

4. 1.1.1 .6  出现2个crash   键盘/内存

三,

> 新任务:[市场需求对接](https://modoglobal.feishu.cn/wiki/KtFqwWthWiCPzRkQ0Ksc7TcJncf?sheet=c28d1e&rangeId=c28d1e_n6hEJEhaFM&rangeVer=1) @张济汶（中台发行部-部门经理）  新增花园IOS手游接入腾讯广告SDK的需求，麻烦安排对接下哈

1. 调试页面 统一UI,新增 接口插接调整

四,



五,

1. 易盾SDK 添加初始化,
2. 调试页面 日志全部ddlog化(游戏日志未写入文本)

六

1. 测试包 打包蒲公英链接





> 2025年12月08日~2025年12月12日

一,

1. 腾讯广告SDK ,添加方法 注册、登录、创角、付费
2. 日志等级可调试

二,

1. report --->  gdt_action  --->- (**void**)init:(NSDictionary *)configDict{   --->

```objc
//对象
@interface Opt_initMax : NSObject

@property (nonatomic, assign) NSInteger timeOut;// 毫秒单位：NSEC_PER_MSEC
@property (nonatomic, assign) BOOL debugMax;// 毫秒单位：NSEC_PER_MSEC

@property (nonatomic, copy) NSString *appKey;
@property (nonatomic, strong) NSDictionary *extraParameter;
@end
//字典转 对象
Opt_initMax *opt = [Opt_initMax mj_objectWithKeyValues:dataDict];
```

```
 pod 'TencentGDTActionSDKMoDo',:git =>"ssh://git@192.168.13.252:8022/modo_ios_public/TencentGDTActionSDKMoDo.git"#

	<key>TencentGDTActionID</key>
	<string>1218291294</string>
	<key>TencentGDTSecretKey</key>
	<string>909bb1e7f80bac180025c07a7e407507</string>

```

2. 加固易盾SDK,ipa打包,无法安装

```
你可以用命令查看所有可用证书：
security find-identity -v -p codesigning

"Apple Distribution: Xiamen Linbei Interactive Entertainment Technology Co.,LTD (7BBKRAB2HT)"


profile. 描述文件 位置  Xcode16.1
~/Library/Developer/Xcode/UserData/Provisioning Profiles


加固方法:
./resign.sh \                           
"/Users/lin/Desktop/HuaYuann 2025-12-09 13-35-33/HuaYuann_encrypted.ipa" \
"/Users/lin/Library/Developer/Xcode/UserData/Provisioning Profiles/8f8333c0-9359-4824-93a1-a8c9889c4941.mobileprovision" \
"Apple Distribution: Xiamen Linbei Interactive Entertainment Technology Co.,LTD (7BBKRAB2HT)"


./resign.sh \
"/Users/lin/Desktop/HuaYuann 2025-12-09 13-35-33/HuaYuann.ipa" \
"/Users/lin/Library/Developer/Xcode/UserData/Provisioning Profiles/8f8333c0-9359-4824-93a1-a8c9889c4941.mobileprovision" \
"Apple Distribution: Xiamen Linbei Interactive Entertainment Technology Co.,LTD (7BBKRAB2HT)"

 
 /Users/lin/Desktop/HuaYuann 2025-12-09 13-35-33/HuaYuann.ipa 
```

三,

1.ipa的包,加固后  重新签名验证(修改脚本)

2.https://modoglobal.feishu.cn/base/KeAqbWPIia8NF7slZGmcUFtmnYc?table=tblECWV88NVUGBJO&view=vewN9YOs9B   文档

3.

```

```

星期四

1. 腾讯广告plugin 添加
2. ios  12系统玩家 打开APP就 crash  问题查询,解决
3. 调试页面,直接添加plugin 测试页面



星期五

1. ios  12系统玩家 打开APP就 crash  问题查询,解决

2. 
   ```
   	<key>TencentGDTActionID</key>
   	<string>1218291294</string>
   	<key>TencentGDTSecretKey</key>
   	<string>909bb1e7f80bac180025c07a7e407507</string>
   
   ```

3. IPHONEOS_DEPLOYMENT_TARGET
   Preprocessor

> 2025年12月15日 ~ 2025年12月20日

星期一:

1. 激励视频返回的错误"未能找到使用指定主机名的服务器"问题查询解决

```
ATAdLogger(UA_6.4.92) Message:广告源向三方sdk请求失败:unitID:11876700,adapterClass:ATTTRewardedVideoAdapter
ATAdLogger(UA_6.4.92) Message:didFailToLoadADSourceWithPlacementID:b68414cba689c9extra:{
  "adunit_id" : "b68414cba689c9",
  "adsource_price" : 164.6934,
  "network_placement_id" : "973222002",
  "req_id" : "c6b865d46b05d20291c3abd0eec8b15d",
  "abtest_id" : 10036218,
  "s_id" : 0,
  "usd_to_rmb_rate" : "7.0465",
  "ad_source_type" : 1,
  "rmb_to_usd_rate" : "0.1419",
  "network_firm_id" : 15,
  "adsource_id" : "11876700",
  "currency" : "CNY",
  "adsource_bid_type" : 0
}error:Error Domain=com.buadsdk Code=20001 "no ads" UserInfo={internal_reason={
    desc = "\U8bf7\U9002\U5f53\U5730\U8c03\U6574\U5e95\U4ef7\U8bbe\U7f6e\Uff0c\U6d4b\U8bd5\U9636\U6bb5\U53ef\U4ee5\U5148\U53bb\U6389\U5e95\U4ef7\Uff0c\U63d0\U5347\U5e7f\U544a\U586b\U5145\U7387";(请适当地调整底价设置，测试阶段可以先去掉底价，提升广告填充率)
    reason = 106;
    "request_id" = "FFA38CED-7DDC-4B4A-BB81-2BE3C8E88A1F";
    "status_code" = 20001;
}, origin_error_code=10002000, extra_reason=加载策略(1)失败, NSLocalizedDescription=no ads}

ATAdLogger(UA_6.4.92) Message:广告源向三方sdk请求失败:unitID:12098273,adapterClass:ATKSRewardedVideoAdapter
ATAdLogger(UA_6.4.92) Message:didFailToLoadADSourceWithPlacementID:b68414cba689c9extra:{
  "adunit_id" : "b68414cba689c9",
  "adsource_price" : 115.172,
  "network_placement_id" : "29682000031",
  "req_id" : "b0896fc923b30b7617ee52bbfef53e28",
  "abtest_id" : 10036218,
  "s_id" : 0,
  "usd_to_rmb_rate" : "7.0465",
  "ad_source_type" : 1,
  "rmb_to_usd_rate" : "0.1419",
  "network_firm_id" : 28,
  "adsource_id" : "12098273",
  "currency" : "CNY",
  "adsource_bid_type" : 0
}error:Error Domain=KSADErrorDomain Code=40003 "数据为空" UserInfo={NSLocalizedDescription=数据为空}
2025-12-16 09:11:04:153 HuaYuann[7805:1814250] [iOS_NativeLog]: [PluginAdapter_topon.m] [-[PluginAdapter_topon didFailToLoadADSourceWithPlacementID:extra:error:]] [Line 177]---📲📲📲 广告源--AD--失败--didFailToLoadADSourceWithPlacementID:b68414cba689c9---error:Error Domain=KSADErrorDomain Code=40003 "数据为空" UserInfo={NSLocalizedDescription=数据为空}
ATAdLogger(UA_6.4.92) Message:GDTRewardedVideo::gdt_rewardVideoAd:didFailWithError:Error Domain=GDTAdErrorDomain Code=109502 "该广告位请求量大而收入较低，出于成本考虑，填充受限，建议合并针对一次潜在曝光机会的多次请求，如合并低填充的相邻瀑布流层级，或减少在同一变现场景的广告位重复配置" UserInfo={NSLocalizedDescription=该广告位请求量大而收入较低，出于成本考虑，填充受限，建议合并针对一次潜在曝光机会的多次请求，如合并低填充的相邻瀑布流层级，或减少在同一变现场景的广告位重复配置}
```

```
 //创建配置
    KSCrashConfiguration* config =[KSCrashConfiguration defaultConfiguration];
    config.captureExceptions=YES;
    config.monitorAppHang=YES;
    // 初始化并启动
    [[KSCrash sharedInstance]installWithConfiguration: config];
    //设置回调
    [KSCrash sharedInstance].crashReportCallback= ^NSArray*(NSArray* reports){
        for(KSCrashReport*reportinreports){NSString*json=[report jsonString];
            [self uploadCrashReport:json];
                                                                              
        };
        [[KSCrash sharedInstance]purgeReports];
```

星期二

1. topon

2. Taku  crash
   ```
   pod 'AnyThinkGDTSDKAdapter','6.4.87'
   这个是1.1.0 版本前的库,没有crash
   需求: https://modoglobal.feishu.cn/docx/LXZRdeLoboXi9PxPu0CcDGeJnob
   需求更新后,crash 数量极高
   ```

   

星期三
1.调试页面

```
// 用户 & 登录态
//性能监控
/*
Feature 开关 / 灰度
开启/关闭功能
实验分组
强制刷新远端配置
*/
```

星期四
1.闪退查看

```
设备id: A0729A90-035A-45D9-B08F-29D9C80A4130
设备名: iPhone14ProMax
设备类型: ios
sdkOpenId: 175952628234062810937154
```

2. GDT 统计数据
3. 收集crash,并将信息存储,待下次进行可将crash 上传(可以添加可控开关)
