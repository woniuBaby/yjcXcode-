RNDemo:

```objective-c
1:Undefined symbol: _OBJC_CLASS_$_JsbBridge

header search path 移除cocos 相关内容(看下图)
  
  
2. Value of type 'VKCaptchaHandler' has no member 'handleCaptchaData'
  #  pod 'VKID', '2.9.0'   先注释掉
```

AppDelegate 中 选择 TestTableViewController

```objc
  self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
  
  UIViewController *vvvvc;
  vvvvc = [[TestTableViewController alloc] initWithStyle:UITableViewStyleGrouped];
//  vvvvc = [[RNTestViewController alloc] init];
  
  self.window.rootViewController = vvvvc;
  [self.window makeKeyAndVisible];
```





