crash发生后会将symbol等信息可以写到文件 ,下次启动可以上传(debugview 中可以查看列表)

> 使用在delegate

```objc
- (void)thirdWithApplication:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{
    
    [self testCrashCollect];
}

-(void)testCrashCollect{
    // 1. 启动崩溃日志收集
    [[CrashLogManager sharedManager] startCollectCrashLogs];
    // 2. 检查并处理上次的崩溃日志
    [self handlePreviousCrashLogs];
}
- (void)handlePreviousCrashLogs {
    CrashLogManager *manager = [CrashLogManager sharedManager];
    
    if ([manager hasUnprocessedCrashLogs]) {
        NSLog(@"发现未处理的崩溃日志");
        
        // 延迟处理，确保UI已经加载完成
        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)),
          dispatch_get_main_queue(), ^{
            [manager processCrashLogs];
        });
    } else {
        NSLog(@"没有未处理的崩溃日志");
    }
}
```



> .h  内容

```objc
//
//  CrashLogManager.h
//  NativeBility
//
//  Created by lin on 2025/12/18.
//

#import <Foundation/Foundation.h>

NS_ASSUME_NONNULL_BEGIN

typedef NS_ENUM(NSInteger, CrashType) {
    CrashTypeSignal = 0,
    CrashTypeException = 1,
    CrashTypeCppException = 2,
    CrashTypeMainBlock = 3
};

@interface CrashLogInfo : NSObject

@property (nonatomic, copy) NSString *crashTime;
@property (nonatomic, copy) NSString *appVersion;
@property (nonatomic, copy) NSString *systemVersion;
@property (nonatomic, copy) NSString *deviceModel;
@property (nonatomic, copy) NSString *exceptionName;
@property (nonatomic, copy) NSString *exceptionReason;
@property (nonatomic, strong) NSArray<NSString *> *callStackSymbols;
@property (nonatomic, assign) CrashType crashType;
@property (nonatomic, copy) NSString *crashTypeString;
@property (nonatomic, copy) NSString *additionalInfo;

+ (instancetype)crashInfoWithException:(NSException *)exception;
+ (instancetype)crashInfoWithSignal:(int)signal signalInfo:(siginfo_t *)signalInfo;

@end

@interface CrashLogManager : NSObject

+ (instancetype)sharedManager;

/**
 * 获取崩溃日志文件夹名称
 */
- (NSString * _Nullable)getCrashLogFirstFile;

/**
 * 开始收集崩溃日志
 */
- (void)startCollectCrashLogs;

/**
 * 停止收集崩溃日志
 */
- (void)stopCollectCrashLogs;

/**
 * 获取所有崩溃日志文件路径
 */
- (NSArray<NSString *> *)getAllCrashLogFiles;

/**
 * 获取最新的崩溃日志文件路径
 */
- (NSString * _Nullable)getLatestCrashLogFile;

/**
 * 读取崩溃日志内容
 */
- (NSString * _Nullable)readCrashLogAtPath:(NSString *)path;

/**
 * 删除指定崩溃日志
 */
- (BOOL)deleteCrashLogAtPath:(NSString *)path;

/**
 * 删除所有崩溃日志
 */
- (void)deleteAllCrashLogs;

/**
 * 上传崩溃日志到服务器
 */
- (void)uploadCrashLogAtPath:(NSString *)path
                  completion:(void(^)(BOOL success, NSError * _Nullable error))completion;

/**
 * 检查是否有未处理的崩溃日志（用于下次启动时处理）
 */
- (BOOL)hasUnprocessedCrashLogs;

/**
 * 处理崩溃日志（调用后标记为已处理）
 */
- (void)processCrashLogs;

@end
NS_ASSUME_NONNULL_END

```

> .m

```objc
//
//  CrashLogManager.m
//  NativeBility
//
//  Created by lin on 2025/12/18.
//

#import "CrashLogManager.h"
//#import <UIKit/UIKit.h>
#import <sys/utsname.h>
#include <libkern/OSAtomic.h>
#include <execinfo.h>
#import <mach-o/dyld.h>

// 全局变量存储原来的异常处理函数
static NSUncaughtExceptionHandler *previousUncaughtExceptionHandler = NULL;
static NSString *const kCrashLogDirectoryName = @"CrashLogs";
static NSString *const kProcessedFlagFile = @"CrashLogs/.processed";

@implementation CrashLogInfo

+ (instancetype)crashInfoWithException:(NSException *)exception {
    CrashLogInfo *info = [[CrashLogInfo alloc] init];
    info.crashTime = [self currentTimeString];
    info.appVersion = [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleShortVersionString"];
    info.systemVersion = [UIDevice currentDevice].systemVersion;
    info.deviceModel = [self deviceModelName];
    info.exceptionName = exception.name;
    info.exceptionReason = exception.reason;
    info.callStackSymbols = exception.callStackSymbols;
    info.crashType = CrashTypeException;
    info.crashTypeString = [self crashTypeString:CrashTypeException];
    return info;
}

+ (instancetype)crashInfoWithSignal:(int)signal signalInfo:(siginfo_t *)signalInfo {
    CrashLogInfo *info = [[CrashLogInfo alloc] init];
    info.crashTime = [self currentTimeString];
    info.appVersion = [[NSBundle mainBundle] objectForInfoDictionaryKey:@"CFBundleShortVersionString"];
    info.systemVersion = [UIDevice currentDevice].systemVersion;
    info.deviceModel = [self deviceModelName];
    info.exceptionName = [self signalName:signal];
    info.exceptionReason = [NSString stringWithFormat:@"Signal %d was raised.", signal];
    info.callStackSymbols = [self backtrace];
    info.crashType = CrashTypeSignal;
    info.crashTypeString = [self crashTypeString:CrashTypeSignal];
    // 添加信号附加信息
    if (signalInfo) {
        info.additionalInfo = [NSString stringWithFormat:@"Signal Code: %d, Signal Address: %p",
                              signalInfo->si_code, signalInfo->si_addr];
    }
    
    return info;
}

+ (NSString *)currentTimeString {
    NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
    [formatter setDateFormat:@"yyyy-MM-dd HH:mm:ss"];
    return [formatter stringFromDate:[NSDate date]];
}

+ (NSString *)deviceModelName {
    struct utsname systemInfo;
    uname(&systemInfo);
    return [NSString stringWithCString:systemInfo.machine encoding:NSUTF8StringEncoding];
}

+ (NSString *)signalName:(int)signal {
    switch (signal) {
        case SIGABRT: return @"SIGABRT";  // 程序终止信号
        case SIGILL: return @"SIGILL";    // 非法指令
        case SIGSEGV: return @"SIGSEGV";  // 段错误（最常见）
        case SIGFPE: return @"SIGFPE";    // 浮点异常
        case SIGBUS: return @"SIGBUS";    // 总线错误
        case SIGPIPE: return @"SIGPIPE";  // 管道破裂
        case SIGTRAP: return @"SIGTRAP";  // 跟踪/断点陷阱
        default: return [NSString stringWithFormat:@"SIGNAL-%d", signal];
    }
}

+ (NSArray<NSString *> *)backtrace {
    void* callstack[128];
    int frames = backtrace(callstack, 128);
    char **strs = backtrace_symbols(callstack, frames);
    
    NSMutableArray *backtrace = [NSMutableArray arrayWithCapacity:frames];
    for (int i = 0; i < frames; i++) {
        [backtrace addObject:[NSString stringWithUTF8String:strs[i]]];
    }
    free(strs);
    
    return backtrace;
}

- (NSString *)description {
    NSMutableString *desc = [NSMutableString string];
    [desc appendFormat:@"========== 崩溃信息 ==========\n"];
    [desc appendFormat:@"崩溃时间: %@\n", self.crashTime];
    [desc appendFormat:@"App版本: %@\n", self.appVersion];
    [desc appendFormat:@"系统版本: %@\n", self.systemVersion];
    [desc appendFormat:@"设备型号: %@\n", self.deviceModel];
    [desc appendFormat:@"崩溃类型: %@\n", [self crashTypeString]];
    [desc appendFormat:@"异常名称: %@\n", self.exceptionName];
    [desc appendFormat:@"异常原因: %@\n", self.exceptionReason];
    
    if (self.additionalInfo) {
        [desc appendFormat:@"附加信息: %@\n", self.additionalInfo];
    }
    
    [desc appendString:@"调用栈:\n"];
    for (NSString *symbol in self.callStackSymbols) {
        [desc appendFormat:@"%@\n", symbol];
    }
    [desc appendString:@"========== 结束 ==========\n"];
    
    return desc;
}

- (NSString *)crashTypeString {
    switch (self.crashType) {
        case CrashTypeSignal: return @"Signal信号崩溃";
        case CrashTypeException: return @"异常崩溃";
        case CrashTypeCppException: return @"C++异常";
        case CrashTypeMainBlock: return @"主线程阻塞";
        default: return @"未知";
    }
}
+ (NSString *)crashTypeString:(CrashType)type {
    switch (type) {
        case CrashTypeSignal: return @"信号崩溃";
        case CrashTypeException: return @"异常崩溃";
        case CrashTypeCppException: return @"C++异常";
        case CrashTypeMainBlock: return @"主线程阻塞";
        default: return @"未知";
    }
}

@end

@implementation CrashLogManager {
    NSString *_crashLogDirectory;
}

#pragma mark - 单例

+ (instancetype)sharedManager {
    static CrashLogManager *instance = nil;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
    });
    return instance;
}

- (instancetype)init {
    self = [super init];
    if (self) {
        [self setupCrashLogDirectory];
    }
    return self;
}

#pragma mark - 目录设置

- (void)setupCrashLogDirectory {
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    NSString *documentsPath = [paths firstObject];
    _crashLogDirectory = [documentsPath stringByAppendingPathComponent:kCrashLogDirectoryName];
    
    NSFileManager *fileManager = [NSFileManager defaultManager];
    if (![fileManager fileExistsAtPath:_crashLogDirectory]) {
        [fileManager createDirectoryAtPath:_crashLogDirectory
               withIntermediateDirectories:YES
                                attributes:nil
                                     error:nil];
    }
}

#pragma mark - 开始/停止收集

- (void)startCollectCrashLogs {
    // 保存原来的异常处理器
    previousUncaughtExceptionHandler = NSGetUncaughtExceptionHandler();
    
    // 设置异常处理回调
    NSSetUncaughtExceptionHandler(&uncaughtExceptionHandler);
    
    // 设置信号处理
    [self setupSignalHandler];
    
    // 设置主线程卡顿检测（可选）
    // [self setupMainThreadBlockDetection];
    
    MDDebugLog(@"崩溃日志收集已启动");
}

- (void)stopCollectCrashLogs {
    // 恢复原来的异常处理
    NSSetUncaughtExceptionHandler(previousUncaughtExceptionHandler);
    
    // 恢复信号处理
    [self restoreSignalHandler];
    
    MDDebugLog(@"崩溃日志收集已停止");
}

#pragma mark - 异常处理

void uncaughtExceptionHandler(NSException *exception) {
    // 保存崩溃信息
    CrashLogInfo *crashInfo = [CrashLogInfo crashInfoWithException:exception];
    [[CrashLogManager sharedManager] saveCrashLog:crashInfo];
    
    // 调用原来的异常处理器
    if (previousUncaughtExceptionHandler) {
        previousUncaughtExceptionHandler(exception);
    }
    
    // 杀死进程
    kill(getpid(), SIGKILL);
}

#pragma mark - 信号处理

- (void)setupSignalHandler {
    signal(SIGABRT, signalHandler);
    signal(SIGILL, signalHandler);
    signal(SIGSEGV, signalHandler);
    signal(SIGFPE, signalHandler);
    signal(SIGBUS, signalHandler);
    signal(SIGPIPE, signalHandler);
    signal(SIGTRAP, signalHandler);
}

- (void)restoreSignalHandler {
    signal(SIGABRT, SIG_DFL);
    signal(SIGILL, SIG_DFL);
    signal(SIGSEGV, SIG_DFL);
    signal(SIGFPE, SIG_DFL);
    signal(SIGBUS, SIG_DFL);
    signal(SIGPIPE, SIG_DFL);
    signal(SIGTRAP, SIG_DFL);
}

void signalHandler(int signal) {
    // 获取信号信息
    siginfo_t *signalInfo = NULL;
    
    // 保存崩溃信息
    CrashLogInfo *crashInfo = [CrashLogInfo crashInfoWithSignal:signal signalInfo:signalInfo];
    [[CrashLogManager sharedManager] saveCrashLog:crashInfo];
    
    // 恢复默认信号处理并重新触发信号
    [CrashLogManager.sharedManager restoreSignalHandler];
    kill(getpid(), signal);
}

#pragma mark - 保存崩溃日志

- (void)saveCrashLog:(CrashLogInfo *)crashInfo {
    @try {
        // 生成文件名：时间戳 + 崩溃类型
        // 自定文件名格式（启动时间）
        NSString *startDate = [self currentTimeString:@"yyyy:MM:dd_HH:mm:ss"];
        
        NSString *filename = [NSString stringWithFormat:@"%@_%@_crash.log",
                              startDate, crashInfo.crashTypeString];
        NSString *filePath = [_crashLogDirectory stringByAppendingPathComponent:filename];
        
        // 写入文件
        NSString *logContent = [crashInfo description];
        [logContent writeToFile:filePath
                     atomically:YES
                       encoding:NSUTF8StringEncoding
                          error:nil];
        
        MDDebugLog(@"崩溃日志已保存: %@", filePath);
    } @catch (NSException *exception) {
        MDDebugLogError(@"保存崩溃日志失败: %@", exception);
    }
}
- (NSString *)currentTimeString:(NSString *)fmt {
    NSDateFormatter *df = [[NSDateFormatter alloc] init];
    df.dateFormat = fmt;
    return [df stringFromDate:[NSDate date]];
}

#pragma mark - 文件管理
- (NSString *)getCrashLogFirstFile {
    return kCrashLogDirectoryName;
}
- (NSArray<NSString *> *)getAllCrashLogFiles {
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSError *error = nil;
    NSArray *files = [fileManager contentsOfDirectoryAtPath:_crashLogDirectory error:&error];
    
    if (error) {
        MDDebugLogError(@"获取崩溃日志文件失败: %@", error);
        return @[];
    }
    
    // 按时间排序（最新的在前面）
    NSMutableArray *sortedFiles = [NSMutableArray array];
    for (NSString *file in files) {
        if ([file hasSuffix:@".log"]) {
            NSString *filePath = [_crashLogDirectory stringByAppendingPathComponent:file];
            [sortedFiles addObject:filePath];
        }
    }
    
    // 按修改时间排序
    [sortedFiles sortUsingComparator:^NSComparisonResult(NSString *path1, NSString *path2) {
        NSDictionary *attr1 = [fileManager attributesOfItemAtPath:path1 error:nil];
        NSDictionary *attr2 = [fileManager attributesOfItemAtPath:path2 error:nil];
        NSDate *date1 = [attr1 fileModificationDate];
        NSDate *date2 = [attr2 fileModificationDate];
        return [date2 compare:date1]; // 降序排列
    }];
    
    return sortedFiles;
}

- (NSString * _Nullable)getLatestCrashLogFile {
    NSArray *files = [self getAllCrashLogFiles];
    return files.firstObject;
}

- (NSString * _Nullable)readCrashLogAtPath:(NSString *)path {
    NSError *error = nil;
    NSString *content = [NSString stringWithContentsOfFile:path
                                                  encoding:NSUTF8StringEncoding
                                                     error:&error];
    if (error) {
        MDDebugLogError(@"读取崩溃日志失败: %@", error);
        return nil;
    }
    return content;
}

- (BOOL)deleteCrashLogAtPath:(NSString *)path {
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSError *error = nil;
    BOOL success = [fileManager removeItemAtPath:path error:&error];
    if (error) {
        MDDebugLogError(@"删除崩溃日志失败: %@", error);
    }
    return success;
}

- (void)deleteAllCrashLogs {
    NSArray *files = [self getAllCrashLogFiles];
    for (NSString *filePath in files) {
        [self deleteCrashLogAtPath:filePath];
    }
}
//删除标记
- (void)markAppNormalLaunch {
    // 在正常启动时删除处理标记
    NSString *documentsPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES).firstObject;
    NSString *processedFlagPath = [documentsPath stringByAppendingPathComponent:@"CrashLogs/.processed"];
    
    NSFileManager *fileManager = [NSFileManager defaultManager];
    if ([fileManager fileExistsAtPath:processedFlagPath]) {
        [fileManager removeItemAtPath:processedFlagPath error:nil];
    }
}

#pragma mark - 下次启动处理

- (BOOL)hasUnprocessedCrashLogs {
    // 检查处理标记文件
    NSString *processedFlagPath = [[_crashLogDirectory stringByDeletingLastPathComponent]
                                   stringByAppendingPathComponent:kProcessedFlagFile];
    NSFileManager *fileManager = [NSFileManager defaultManager];
    
    // 如果标记文件不存在，说明上次有崩溃且未处理
    if (![fileManager fileExistsAtPath:processedFlagPath]) {
        // 检查是否有崩溃日志文件
        NSArray *files = [self getAllCrashLogFiles];
        return files.count > 0;
    }
    
    return NO;
}

- (void)processCrashLogs {
    // 1. 获取所有崩溃日志
    NSArray *crashLogs = [self getAllCrashLogFiles];
    
    if (crashLogs.count == 0) {
        MDDebugLog(@"没有发现崩溃日志");
        return;
    }
    
    MDDebugLogWarn(@"发现 %ld 个崩溃日志待处理", (long)crashLogs.count);
    
    // 2. 处理每个崩溃日志（例如：上传到服务器、显示给用户等）
    for (NSString *logPath in crashLogs) {
        NSString *logContent = [self readCrashLogAtPath:logPath];
        if (logContent) {
            // 这里可以添加你的处理逻辑
//            MDDebugLog(@"处理崩溃日志:\n%@", logContent);
            
            // 上传到服务器--->先不加
//            [self uploadCrashLogAtPath:logPath completion:^(BOOL success, NSError *error) {
//                if (success) {
//                    MDDebugLog(@"崩溃日志上传成功: %@", logPath);
//                    // 上传成功后删除本地文件（可选）
//                    // [self deleteCrashLogAtPath:logPath];
//                } else {
//                    MDDebugLogError(@"崩溃日志上传失败: %@", error);
//                }
//            }];
        }
    }
    
    // 3. 创建已处理标记文件
    [self markCrashLogsAsProcessed];
}

- (void)markCrashLogsAsProcessed {
    NSString *processedFlagPath = [[_crashLogDirectory stringByDeletingLastPathComponent]
                                   stringByAppendingPathComponent:kProcessedFlagFile];
    NSFileManager *fileManager = [NSFileManager defaultManager];
    
    // 创建标记文件
    [@"processed" writeToFile:processedFlagPath
                   atomically:YES
                     encoding:NSUTF8StringEncoding
                        error:nil];
}

#pragma mark - 上传功能

- (void)uploadCrashLogAtPath:(NSString *)path
                   completion:(void(^)(BOOL success, NSError * _Nullable error))completion {
    // 这里实现上传逻辑
    // 示例：使用NSURLSession上传
    NSString *logContent = [self readCrashLogAtPath:path];
    if (!logContent) {
        if (completion) {
            completion(NO, [NSError errorWithDomain:@"CrashLogManager"
                                               code:-1
                                           userInfo:@{NSLocalizedDescriptionKey: @"无法读取日志文件"}]);
        }
        return;
    }
    
    // 转换为Base64（可选）
    NSData *logData = [logContent dataUsingEncoding:NSUTF8StringEncoding];
    NSString *base64String = [logData base64EncodedStringWithOptions:0];
    
    // 构建请求参数
    NSDictionary *params = @{
        @"log_content": base64String ?: @"",
        @"file_name": [path lastPathComponent],
        @"timestamp": @([[NSDate date] timeIntervalSince1970])
    };
    
    // 这里应该是你的服务器地址
    NSString *serverURL = @"https://your-server.com/api/crash-log";
    
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:serverURL]];
    request.HTTPMethod = @"POST";
    request.HTTPBody = [NSJSONSerialization dataWithJSONObject:params options:0 error:nil];
    [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    
    NSURLSessionDataTask *task = [[NSURLSession sharedSession] dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
        BOOL success = (error == nil && [(NSHTTPURLResponse *)response statusCode] == 200);
        dispatch_async(dispatch_get_main_queue(), ^{
            if (completion) {
                completion(success, error);
            }
        });
    }];
    
    [task resume];
}

@end

```

> 效果

```
crash日志: ========== 崩溃信息 ==========
崩溃时间: 2025-12-18 18:59:01
App版本: 1.1.4
系统版本: 16.7.12
设备型号: iPhone10,3
崩溃类型: 异常崩溃
异常名称: NSRangeException
异常原因: *** -[__NSArrayI objectAtIndexedSubscript:]: index 10 beyond bounds [0 .. 2]
调用栈:
0   CoreFoundation                      0x0000000192f75418 55B9BA28-4C5C-3FE7-9C47-4983337D6E83 + 37912
1   libobjc.A.dylib                     0x000000018c251c28 objc_exception_throw + 56
2   CoreFoundation                      0x00000001931043fc 55B9BA28-4C5C-3FE7-9C47-4983337D6E83 + 1672188
3   CoreFoundation                      0x0000000192f850a0 55B9BA28-4C5C-3FE7-9C47-4983337D6E83 + 102560
4   HuaYuann                            0x00000001050f2504 __53-[CrashButtonDebugVC crashObjectiveCArrayOutOfBounds]_block_invoke + 300
5   libdispatch.dylib                   0x0000000199d1c780 B51E7CDB-ABC9-35AF-B8BB-2DCE23BC4D6E + 411520
6   libdispatch.dylib                   0x0000000199cf3d48 B51E7CDB-ABC9-35AF-B8BB-2DCE23BC4D6E + 245064
7   libdispatch.dylib                   0x0000000199d05780 B51E7CDB-ABC9-35AF-B8BB-2DCE23BC4D6E + 317312
8   libdispatch.dylib                   0x0000000199cfdd64 B51E7CDB-ABC9-35AF-B8BB-2DCE23BC4D6E + 286052
9   libdispatch.dylib                   0x0000000199cfda88 B51E7CDB-ABC9-35AF-B8BB-2DCE23BC4D6E + 285320
10  CoreFoundation                      0x0000000192ffd9ac 55B9BA28-4C5C-3FE7-9C47-4983337D6E83 + 596396
11  CoreFoundation                      0x0000000192fe1648 55B9BA28-4C5C-3FE7-9C47-4983337D6E83 + 480840
12  CoreFoundation                      0x0000000192fe5d20 CFRunLoopRunSpecific + 584
13  GraphicsServices                    0x00000001cb0b5998 GSEventRunModal + 160
14  UIKitCore                           0x000000019527834c 1242978A-2C2C-3781-8D6C-9777EDCE2804 + 3609420
15  UIKitCore                           0x0000000195277fc4 UIApplicationMain + 312
16  HuaYuann                            0x0000000100f6ece0 main + 148
17  dyld                                0x00000001b07a4344 199941A5-95EE-3054-8E54-AE6387A9FA9A + 82756
========== 结束 ==========
```

