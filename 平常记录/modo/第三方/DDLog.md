```objc
//错误
define DDLogError(frmt, ...)   LOG_MAYBE(NO,                LOG_LEVEL_DEF, DDLogFlagError,   0, nil, __PRETTY_FUNCTION__, frmt, ##__VA_ARGS__)
#define DDLogWarn(frmt, ...)    LOG_MAYBE(LOG_ASYNC_ENABLED, LOG_LEVEL_DEF, 
//警告
DDLogFlagWarning, 0, nil, __PRETTY_FUNCTION__, frmt, ##__VA_ARGS__)
#define DDLogInfo(frmt, ...)    LOG_MAYBE(LOG_ASYNC_ENABLED, LOG_LEVEL_DEF, DDLogFlagInfo,    0, nil, __PRETTY_FUNCTION__, frmt, ##__VA_ARGS__)
//调试
#define DDLogDebug(frmt, ...)   LOG_MAYBE(LOG_ASYNC_ENABLED, LOG_LEVEL_DEF, DDLogFlagDebug,   0, nil, __PRETTY_FUNCTION__, frmt, ##__VA_ARGS__)
//冗长
#define DDLogVerbose(frmt, ...) LOG_MAYBE(LOG_ASYNC_ENABLED, LOG_LEVEL_DEF, DDLogFlagVerbose, 0, nil, __PRETTY_FUNCTION__, frmt, ##__VA_ARGS__)
```

```objc
//关闭xcode系统日志: 这样系统日志基本全部屏蔽。
✔ 设置 Scheme
OS_ACTIVITY_MODE = disable
✔ 再加一条（更强）
DISABLE_OS_LOG = 1
```







修改默认日志路径

> ![这里写图片描述](https://img-blog.csdn.net/20180526101400834?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x6eTUyMDMwOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)这个代码就不用我粘贴了吧！xcode有自动补全！



注意:

```
#ifdef DEBUG
static const MGLDDLogLevel ddLogLevel = MGLDDLogLevelDebug;
#else
static const MGLDDLogLevel ddLogLevel = MGLDDLogLevelVerbose;
#endif
```



# ios 手动集成 CocoaLumberjack--详细文档

https://blog.csdn.net/lzy520308/article/details/80459043

---

MODO  相关
```objc
#pragma mark ------------|| 日志配置 ||------------

#if CocoaLumberjackSupport

    // ------------- CocoaLumberjackSupport ------------- //
    #import "DDDynamicLogLevel.h"

    #ifdef LOG_LEVEL_DEF
    #   undef LOG_LEVEL_DEF
    #endif
    #define LOG_LEVEL_DEF [DDDynamicLogLevel ddLogLevel]

       //信息 普通打印
    #define MDDebugLog(fmt, ...) DDLogInfo((@"[iOS_NativeLog]: [%@] [%s] [Line %d]---📄📄📄 " fmt),[[NSString stringWithUTF8String:__FILE__] lastPathComponent], __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
    #define MDDebugLogWS(fmt, ...) DDLogInfo((@"[iOS_NativeLog]: [%@] [%s] [Line %d]---🌐🌐🌐 " fmt),[[NSString stringWithUTF8String:__FILE__] lastPathComponent], __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
    #define MDDebugLogCOCOS(fmt, ...) DDLogInfo((@"[iOS_NativeLog]: [%@] [%s] [Line %d]---🎮🎮🎮 " fmt),[[NSString stringWithUTF8String:__FILE__] lastPathComponent], __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
    #define MDDebugLogJS(fmt, ...) DDLogInfo((@"[iOS_NativeLog]: [%@] [%s] [Line %d]---☕️☕️☕️ " fmt),[[NSString stringWithUTF8String:__FILE__] lastPathComponent], __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)

    //debug
    #define MDDebugLogDebug(fmt, ...) DDLogDebug((@"[iOS_NativeLog]: [%@] [%s] [Line %d]--🔧🔧🔧" fmt),[[NSString stringWithUTF8String:__FILE__] lastPathComponent], __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
    //警告
    #define MDDebugLogWarn(fmt, ...) DDLogWarn((@"[iOS_NativeLog]: [%@] [%s] [Line %d] --⚠️⚠️ ⚠️" fmt),[[NSString stringWithUTF8String:__FILE__] lastPathComponent], __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
    //报错
    #define MDDebugLogError(fmt, ...) DDLogError((@"[iOS_NativeLog]: [%@] [%s] [Line %d] --❌❌❌ " fmt),[[NSString stringWithUTF8String:__FILE__] lastPathComponent], __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)
    //冗长重复
    #define MDDebugLogVerbose(fmt, ...) DDLogVerbose((@"[iOS_NativeLog]: [%@] [%s] [Line %d] --🐢🐢🐢 " fmt),[[NSString stringWithUTF8String:__FILE__] lastPathComponent], __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__)

#else

    // ------------- CocoaLumberjack Un Support ------------- //
    #define MDDebugLog(fmt, ...) NSLog((@"NativeBility_iOS：%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
    #define MDDebugLogWS(fmt, ...) NSLog((@"NativeBility_iOS：%s [Line %d] " fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
    #define MDRNDebugLog(fmt, ...)
    #define MDDebugLogWarn(fmt, ...) NSLog((@"NativeBility_iOS：%s [Line %d] --⚠️⚠️ ⚠️" fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
    #define MDDebugLogError(fmt, ...) NSLog((@"NativeBility_iOS：%s [Line %d] --❌❌❌" fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);

    #define MDNTLog(fmt, ...)

#endif
```





> 代码 MDLogModeService

```
```



> 特殊的RN日志

```objc
//MDLogModeService -->setBeginLevelMode方法

#if CocoaLumberjackSupport && SocketRocketSupport && ReactSupport
    RCTSetLogThreshold(RCTLogLevelTrace);
    __weak typeof(self) weakSelf = self;
    RCTSetLogFunction(^(RCTLogLevel level, RCTLogSource source, NSString *fileName, NSNumber *lineNumber, NSString *message) {
        __strong typeof(weakSelf) strongSelf = weakSelf;
        if (strongSelf) {
            MDDebugLog(@"[javascript] %@",message);
        }
    });
#endif
```

