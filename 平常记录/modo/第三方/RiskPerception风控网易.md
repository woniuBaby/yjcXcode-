RiskPerception风控网易

官网:

https://dun.163.com/dashboard#/m/risk-manage/service/?pid=YD00871651425494

文档:

https://support.dun.163.com/documents/761315885761396736?docId=765722596493144064

modo:
pod 'RiskPerceptionModo',:git =>"ssh://git@192.168.13.252:8022/modo_ios_public/riskperceptionmodo.git"#易盾风控

```
产品秘钥
SecretId：b299dad0a21392a75bd6d61667260539 复制
SecretKey：583c78eb40ae7777c7f2f1d3c0e3a8c6 复制
```

```
账号：dev@babigame.cn
密码：Modo2025

当前咱们接入双端SDK哈
参数如下
productId   YD00871651425494
iOS businessId    29f95b0fa48f9e3cae5bdf3ba087f109
```

> 需求

```
①请登录易盾官网（https://dun.163.com/dashboard?v=0116&locale=zh-CN#/m/overview/）,智能风控-服务管理-开通的服务-点击查看产品密钥，获取秘钥信息；
②在服务管理页获取产品编号和业务id（产品编号、businessID），注意不在秘钥凭证中；
③接入详情请见：https://support.dun.163.com/documents/761315885761396736?docId=778684330614894592接入教程和后端接入-智能风控数据服务接口-在线检测接口https://support.dun.163.com/documents/761315885761396736?docId=834511620372037632；
④从官网下载sdk，下载位置https://dun.163.com/dashboard?v=0116&locale=zh-CN#/m/overview/，智能风控-服务管理-sdk版本，建议下载最新版
⑤返回结果中，建议保存taskid字段，以便后续沟通反馈时作为数据查询的标识。
⑥上线前建议将易盾隐私协议合并至app隐私协议中https://support.dun.163.com/documents/761315885761396736?docId=875474991229304832
```



> 需要配置plist

```objc
产品 ID RiskPerceptionSDKProductId  --->  YD00871651425494
业务 ID，RiskPerceptionSDKBusinessId ---> 29f95b0fa48f9e3cae5bdf3ba087f109 必填
  
  //网易易盾sdk https://support.dun.163.com/documents/761315885761396736?docId=765722596493144064
#if __has_include(<RiskPerception/NTESRiskUniPerception.h>)
#import <RiskPerception/NTESRiskUniPerception.h>
#define RiskPerceptionSDKSupport 1
#endif
  
#if RiskPerceptionSDKSupport
    NSString *RiskPerceptionSDKProductId = [NativeBitityBundleUtil getObjectFromFrameworkConfigForKey:@"RiskPerceptionSDKProductId"];
    NSString *reason = [NSString stringWithFormat:@"易盾提供的产品id productId 为空,请检查key是否正确设置。"];
    if (ex_isEmptyString(RiskPerceptionSDKProductId) == false) {
        NSAssert(true, reason);
    } else {
        [[NTESRiskUniPerception fomentBevelDeadengo] init:RiskPerceptionSDKProductId callback:^(int code, NSString * _Nonnull msg, NSString * _Nonnull content) {
            MDDebugLog(@"RiskUniPerceptionSDK code: %d, msg: %@, content: %@", code, msg, content);
            if (code == 200) {
                MDDebugLog(@"RiskUniPerceptionSDK 初始化 成功");
            } else if (code == 201) {
                MDDebugLogError(@"RiskUniPerceptionSDK uninit 未初始化");
            } else if (code == 203) {
                MDDebugLogError(@"RiskUniPerceptionSDK parm invalid 参数不合法");
            } else{
                MDDebugLogError(@"RiskUniPerceptionSDK error 其他错误");
            }
        }];
    }
#endif
```





> 相关代码

```objc
//设置用户信息接口
- (void)setRoleInfo:(NSDictionary *)configDict{
    Callback *callback = configDict[PluginCallBack];
    NSDictionary *dataDict = configDict[PluginDataDict];

    NSDictionary *params = @{
        @"businessId" : dataDict[@"businessId"]?: @"",              // 当前业务 ID，必填 29f95b0fa48f9e3cae5bdf3ba087f109
        @"roleId" : dataDict[@"roleId"] ?: @"",                     // 用户/玩家的角色 ID，必填
        @"roleName" : dataDict[@"roleName"] ?: @"",                 // 用户/玩家的角色名称，可选，不需要可填空
        @"roleAccount" : dataDict[@"roleAccount"] ?: @"",           // 用户/玩家的账号，可选，不需要可填空
        @"roleServer" : dataDict[@"roleServer"] ?: @"",             // 用户/玩家的角色的服务器名称，可选，不需要可填空
        @"GameVersion" : dataDict[@"GameVersion"] ?: @"",           // 游戏版本号
        @"AssetVersion" : dataDict[@"AssetVersion"] ?: @"",         // 资源文件版本
        @"channel" : dataDict[@"channel"] ?: @""                    // 渠道信息，供用户传入该app的渠道信息（App Store）
    };
    if (ex_isEmpty(params[@"businessId"])) {
        MDDebugLogError(@"riskperception businessId 为空 (必填)");
        Msg_riskperception *error = [Msg_riskperception msgWithCode:@"203" arg:@{@"describe":@"当前业务 ID businessId 为空 (必填)"}];
        [callback failWithMsg:error];
        return;
    }
    
    if (ex_isEmpty(params[@"roleId"])) {
        MDDebugLogError(@"riskperception roleId 为空 (必填)");
        Msg_riskperception *error = [Msg_riskperception msgWithCode:@"203" arg:@{@"describe":@"用户/玩家的角色 ID 为空 (必填)"}];
        [callback failWithMsg:error];
        return;
    }

    NSString *channel = params[@"channel"];
    [NTESRiskUniConfiguration setChannel:channel];//渠道信息 比如：App Store CN/App Store EU）
    // 游戏类型应用需要上传的信息，对应一个 json 字符串，可选，不需要可填空
    NSDictionary *object = @{
        @"GameVersion" : params[@"roleAccount"],
        @"AssetVersion" : params[@"AssetVersion"]
    };
    NSData *jsonData = [NSJSONSerialization dataWithJSONObject:object options:NSJSONWritingPrettyPrinted error:nil];
    NSString *jsonString = [[NSString alloc]initWithData:jsonData encoding:NSUTF8StringEncoding];

    int serverType = [dataDict[@"serverType"] intValue] ?: 1;       // 设置数据上报的服务器归属地，应用/游戏根据自身发行地区来控制
    //服务器类型(1:中国大陆;2:中国台湾3:其他区域。4：欧盟区域  默认大陆,不需要设置，如需接入台湾服务需要单独开通，请与商务联系。)
    [NTESRiskUniConfiguration setServerType:serverType];

    int serverId = [dataDict[@"serverId"] intValue] ?: -1;          // 用户/玩家的角色所属服务器的 ID，可选，不需要可填-1

    
    int rec = [[NTESRiskUniPerception fomentBevelDeadengo] setRoleInfo:params[@"businessId"] roleId:params[@"roleId"] roleName:params[@"roleName"] roleAccount:params[@"roleAccount"] roleServer:params[@"roleServer"] serverId:serverId gameJson:jsonString];
    MDDebugLog(@"易盾风控传值:%@ ---> 结果: %d",dataDict,rec);
    [callback sucessWithResult:@(rec)];
}
```









```
//git 账号列表 
git config --list
修改 名字以及 邮箱
git config --global user.name "yanjinchao"
git config --global user.email "yanjinchao@modo.cn"


//brew 操作
// 查询：
brew search 软件名
// 安装：
brew install 软件名
// 卸载：
brew uninstall 软件名
// 更新 Homebrew：
brew update 
// 查看 Homebrew 配置信息：
brew config 
```

