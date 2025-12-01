\#define MGLDDLogError(frmt, ...)  LOG_OBJC_MAYBE(LOG_ASYNC_ERROR,  LOG_LEVEL_DEF, LOG_FLAG_ERROR,  0, frmt, ##__VA_ARGS__)

\#define DDLogDebug(frmt, ...)  LOG_OBJC_MAYBE(LOG_ASYNC_WARN,  LOG_LEVEL_DEF, LOG_FLAG_WARN,  0, frmt, ##__VA_ARGS__)

\#define MGLDDLogInfo(frmt, ...)  LOG_OBJC_MAYBE(LOG_ASYNC_INFO,  LOG_LEVEL_DEF, LOG_FLAG_INFO,  0, frmt, ##__VA_ARGS__)

\#define MGLDDLogDebug(frmt, ...)  LOG_OBJC_MAYBE(LOG_ASYNC_DEBUG,  LOG_LEVEL_DEF, LOG_FLAG_DEBUG,  0, frmt, ##__VA_ARGS__)

\#define MGLDDLogVerbose(frmt, ...)





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