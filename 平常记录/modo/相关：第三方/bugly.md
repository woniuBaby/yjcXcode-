https://bugly.qq.com/v2/product/apps/4a6f981132?pid=2

1. 首先需要个人信息中的ID-->个人中心-->账号绑定

2. pod 'Bugly'
3. 上传符号表,目前只支持 bugly根据上传(https://bugly.qq.com/docs/user-guide/symbol-configuration-ios/?v=1.0.0)  下载工具后 ,终端运行 buglyqq-upload-symbol.jar       需要配置java 环境

```
java -jar buglyoa-upload-symbol.jar 
-appid <APP ID> 
-appkey <APP KEY> 
-version <App Version> 
-platform <App Platform> 
-inputSymbol <Original Symbol File Path>


java -jar buglyqq-upload-symbol.jar \
-appid 4a6f981132 \
-appkey 0ca992f1-90df-4893-a459-8774b504b535 \
-version 1.1.8.4 \
-platform IOS \
-inputSymbol "/Users/lin/Library/Developer/Xcode/Archives/2026-03-04/HuaYuann 2026-3-4, 18.28.xcarchive/dSYMs/Tquic.dSYM"

其他dSYM 
HuaYuanLAExtension.appex.dSYM
Tquic.dSYM
ActivitySDK.framework.dSYM
```

