```
Line参数
Channel ID：2008485589
Channel Secret ：327df9c3e07f4766bfce2c81d37b3284
安卓集成参考：https://developers.line.biz/en/docs/line-login-sdks/android-sdk/integrate-line-login/
iOS集成参考：https://developers.line.biz/en/docs/line-login-sdks/ios-sdk/swift/integrate-line-login/
LINE API SDK：https://developers.line.biz/en/docs/downloads/
```



> 登录 lineSDK

![img_v3_02s1_f0b9affe-3398-4e5c-82c3-0b1d8eae0a4g](/Users/lin/Library/Application Support/LarkShell/sdk_storage/5bbd7ac97bce8f6f1f6594334b1ce40a/resources/images/img_v3_02s1_f0b9affe-3398-4e5c-82c3-0b1d8eae0a4g.jpg)

注意:    官方SDK,不支持分享功能,仅有登录 (有漏洞 后关掉了)





LineAppID   "1655277592"

![img_v3_02s4_4a2a0e33-4112-44c2-a00f-ef77b80c170g](/Users/lin/Library/Application Support/LarkShell/sdk_storage/5bbd7ac97bce8f6f1f6594334b1ce40a/resources/images/img_v3_02s4_4a2a0e33-4112-44c2-a00f-ef77b80c170g.jpg)

登录前必须初始化

```objective-c
#if LineSDKCoreSupport
    NSString *LineAppID = [NativeBitityBundleUtil getObjectFromFrameworkConfigForKey:@"LineAppID"];
    if (LineAppID) {
        [[LineSDKLoginManager sharedManager] setupWithChannelID:LineAppID universalLinkURL:[NSURL URLWithString:UNIVERSAL_LINK]];
    }else {
        NSLog(@"LineAppID Not Set");
    }
#endif
```









> 分享