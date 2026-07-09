1. https://xingyun.xiaojukeji.com/docs/dokit/#/intro
   网址

2. git地址
   https://github.com/didi/DoKit/blob/master/Doc/iOS-ReleaseNotes.md

3. 代码实现
   ```objective-c
       pod 'DoraemonKit/Core', '~> 3.1.7', :configurations => ['Debug'] #必选
       pod 'DoraemonKit/WithGPS', '~> 3.1.7', :configurations => ['Debug'] #可选
       pod 'DoraemonKit/WithLoad', '~> 3.1.7', :configurations => ['Debug'] #可选
       pod 'DoraemonKit/WithLogger', '~> 3.1.7', :configurations => ['Debug'] #可选
       pod 'DoraemonKit/WithDatabase', '~> 3.1.7', :configurations => ['Debug'] #可选
       #pod 'DoraemonKit/WithMLeaksFinder', '~> 3.1.7', :configurations => ['Debug'] #可选
       pod 'DoraemonKit/WithWeex', '~> 3.1.7', :configurations => ['Debug'] #可选
   
   #import "AppDelegate.h"
   
   
   #ifdef CC_DEBUG
   #import <DoraemonKit/DoraemonManager.h>//性能SDK
   #else
         
   - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
       [[DoraemonManager shareInstance] installWithPid:@"productId"];//productId为在“平台端操作指南”中申请的产品id
       }
   
   ```

   