如何将 Lookin 的 iOS Framework 嵌入到你的 iOS App 中 ？

首先，请确保你的 iOS App 的 Deployment Target 不低于 iOS 9.0

然后，在以下 2 种方式中任选一种即可：

1. 使用 CocoaPods（推荐）：

```
pod 'LookinServer', :configurations => ['Debug']
```

2. 使用 Swift Package Manager：

```
https://github.com/QMUI/LookinServer/
```



下载：

~~~
https://lookin.work/get/
~~~



appdelegate

```objc
pod 'LookinServer', :configurations => ['Debug']

#if DEBUG
#import <LookinServer/LookinServer.h> // 可能需要此头文件
#endif


// 在调试模式下检查 LookinServer 是否可用
- (void)checkLookinServerAvailability {
#if DEBUG
    Class lookinClass = NSClassFromString(@"LookinServer");
    if (lookinClass) {
        NSLog(@"✅ LookinServer 已集成");
        // 可以尝试启动
        SEL startSelector = NSSelectorFromString(@"start");
        if ([lookinClass instancesRespondToSelector:startSelector]) {
            NSLog(@"✅ LookinServer 支持启动方法");
        }
    } else {
        NSLog(@"❌ LookinServer 未集成");
    }
#endif
}
```

