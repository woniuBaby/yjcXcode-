```
账号:
modomaster1
Americalol2020
```



```
推特参数
应用ID：31830647
API密钥：UsuF02lW2NOBN6tHm8duNmwnM
API密钥密文：G1Z8AQeukZEED2nMhK19xihGaAJAFP643DmvENntWZVEWsPm0H
身份验证令牌：AAAAAAAAAAAAAAAAAAAAAHey5QEAAAAA4yjPvEEfisGyYl80pfouAKkqaCc%3D0iNDYdtVNtlw4Q0r2UxRtuSMBArve0WoyWfvJnR7bxu4ySmtVY
参考文档：https://docs.x.com/fundamentals/authentication/oauth-2-0/authorization-code
```



```
https://developer.x.com/en/portal/projects/1988890488800198656/apps/31830968/auth-settings
```

```
Access Token   1300246217134280705-hDQHeIczgNK26KCp223xFxWapgSBgU
Access Token Secret  Q07LpR1Z1DaGhsqAF5CKk9uqGaWDL03O9KApby9sZScV1
Bearer Token AAAAAAAAAAAAAAAAAAAAALiz5QEAAAAA097x4SwgbG5C4u6ASIgErWv46UQ%3DtLHWVp5tsQPVYk1I55YwLsVVTXLhFQtqMN5tA3Ax1y2yvI1MCR

API Key      OFtujZdw0QRHYQo1cfxdxsL78

API Key Secre     DZBonrjAURF2im8UW6aA7hBWy8n7tIbY2iWvoykhmhUKrmwSjw

call back url   twitter-OFtujZdw0QRHYQo1cfxdxsL78://
Client ID    cFhsQjI0NUhfZFhfWkMtMFg0cXg6MTpjaQ
Client Secret  DB-j3A5W1KUZRtDhjxEhTOh57m3apsQTUeT9szPc3pZzPkYHiD
```

> 分享

NSString *encodedString = [NSString stringWithFormat:@"twitter://post?message=%@\n%@",title, url];
encodedString = [encodedString stringByAddingPercentEncodingWithAllowedCharacters:[NSCharacterSet URLQueryAllowedCharacterSet]];
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:encodedString]];

![img_v3_02s1_24b702b6-00f9-41f5-bcd1-c02e2ad04f5g](/Users/lin/Library/Application Support/LarkShell/sdk_storage/5bbd7ac97bce8f6f1f6594334b1ce40a/resources/images/img_v3_02s1_24b702b6-00f9-41f5-bcd1-c02e2ad04f5g.jpg)

```objective-c
//强制拉起 只能连接+ 文字
NSString *encodedString = [NSString stringWithFormat:@"twitter://post?message=%@\n%@",title, url];
encodedString = [encodedString stringByAddingPercentEncodingWithAllowedCharacters:[NSCharacterSet URLQueryAllowedCharacterSet]];
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:encodedString]];
```

Twitter Card 页面模板

想要在twitter中 分享内容链接,且有预览功能,需要在链接配置一些内容:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <!-- 必须：Twitter Card 类型（大图预览） -->
    <meta name="twitter:card" content="summary_large_image">

    <!-- 必须：标题 -->
    <meta name="twitter:title" content="你的标题（最多 70 字）">

    <!-- 必须：描述 -->
    <meta name="twitter:description" content="你的描述文本，展示在卡片下方，最多 200 字">

    <!-- 必须：图片（1200x628 推荐） -->
    <meta name="twitter:image" content="https://yourdomain.com/images/share_image.jpg">

    <!-- 可选：你的 Twitter 用户名 -->
    <meta name="twitter:site" content="@your_account">

    <!-- 可选：作者账号 -->
    <meta name="twitter:creator" content="@your_creator">

    <!-- Open Graph（兼容 Facebook、Line、WhatsApp、Telegram、iMessage） -->
    <meta property="og:type" content="website">
    <meta property="og:title" content="你的标题（同上）">
    <meta property="og:description" content="你的描述（同上）">
    <meta property="og:image" content="https://yourdomain.com/images/share_image.jpg">
    <meta property="og:url" content="https://yourdomain.com/share?id=123">
    <meta property="og:site_name" content="Your Site Name">

    <title>分享页面</title>

    <style>
        body { 
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; 
            padding: 20px;
            line-height: 1.6;
        }
    </style>
</head>

<body>
    <h1>分享页面示例</h1>
    <p>此页面仅用于在 Twitter 显示预览卡片。</p>
</body>
</html>
```



> 登录

```objective-c
//tw 登录
NSString *userAccessToken = [[NSUserDefaults standardUserDefaults] stringForKey:@"TwitterUserAccessToken"];
 NSMutableDictionary *dic = [NSMutableDictionary dictionary];
      [dic setValue:@"ZXZwalYybnl2OU01WHEzaGp4Rmg6MTpjaQ" forKey:@"clientId"];
      [dic setValue:@"twitter-ZXZwalYybnl2OU01WHEzaGp4Rmg6MTpjaQ://" forKey:@"redirectUri"];
      Callback *back = [[Callback alloc] init];
      back.onHandler = ^(Msg *err, NSObject *result) {

          DebugLog(@"result ---- %@ \n err ----- %@",result.mj_JSONObject,err.mj_JSONObject);
          [self showPlugin:@"login" Adapter:@"twitter" Api:@"login" options:dic Result:result.mj_JSONString error:err.mj_JSONString];
```



