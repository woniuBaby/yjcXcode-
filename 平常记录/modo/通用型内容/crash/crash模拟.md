模拟crash: 12个按钮+ 方法

```objc
#import "SDKDebugUsuallyVC.h"

NS_ASSUME_NONNULL_BEGIN

@interface CrashButtonDebugVC : SDKDebugUsuallyVC

@end

NS_ASSUME_NONNULL_END
```

.m 文件修改为.mm  里面有C++内容

```objc
//
//  CrashButtonDebugVC.m
//  NativeBility
//
//  Created by lin on 2025/12/18.
//

#import "CrashButtonDebugVC.h"
#include <exception>
#include <string>

@interface CrashButtonDebugVC ()

@property (nonatomic, strong) UIScrollView *scrollView;
@property (nonatomic, strong) NSMutableArray<UIButton *> *crashButtons;

@end

@implementation CrashButtonDebugVC

#pragma mark - Lifecycle

- (void)viewDidLoad {
    [super viewDidLoad];
    [self setTitle:@"crash按钮" withType:BlankView];
    [self setupUI];
    [self setupCrashButtons];
}

#pragma mark - UI Setup

- (void)setupUI {
    
    // 创建ScrollView
    self.scrollView = [[UIScrollView alloc] initWithFrame:self.blankView.bounds];
    self.scrollView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
    [self.blankView addSubview:self.scrollView];
    
    self.crashButtons = [NSMutableArray array];
}

#pragma mark - 创建Crash按钮

- (void)setupCrashButtons {
    CGFloat buttonWidth = self.blankView.bounds.size.width - 40;
    CGFloat buttonHeight = 50;
    CGFloat spacing = 20;
    CGFloat yOffset = 20;
    
    // 1. Objective-C异常 - 数组越界
    [self addButtonWithTitle:@"1. Objective-C异常 (数组越界)"
                    subtitle:@"NSRangeException"
                       color:[UIColor systemRedColor]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashObjectiveCArrayOutOfBounds)];
    yOffset += (buttonHeight + spacing);
    
    // 2. Objective-C异常 - 消息发送给已释放对象
    [self addButtonWithTitle:@"2. Objective-C异常 (消息发送给nil)"
                    subtitle:@"unrecognized selector"
                       color:[UIColor systemOrangeColor]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashObjectiveCUnrecognizedSelector)];
    yOffset += (buttonHeight + spacing);
    
    // 3. 信号崩溃 - SIGSEGV (段错误)
    [self addButtonWithTitle:@"3. SIGSEGV信号 (段错误)"
                    subtitle:@"访问野指针"
                       color:[UIColor.systemPurpleColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashSignalSegmentationFault)];
    yOffset += (buttonHeight + spacing);
    
    // 4. 信号崩溃 - SIGABRT (中止信号)
    [self addButtonWithTitle:@"4. SIGABRT信号 (中止)"
                    subtitle:@"断言失败"
                       color:[UIColor.systemIndigoColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashSignalAbort)];
    yOffset += (buttonHeight + spacing);
    
    // 5. C++异常
    [self addButtonWithTitle:@"5. C++异常"
                    subtitle:@"std::runtime_error"
                       color:[UIColor.systemBlueColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashCppException)];
    yOffset += (buttonHeight + spacing);
    
    // 6. 堆栈溢出
    [self addButtonWithTitle:@"6. 堆栈溢出"
                    subtitle:@"无限递归"
                       color:[UIColor.systemTealColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashStackOverflow)];
    yOffset += (buttonHeight + spacing);
    
    // 7. 死锁
    [self addButtonWithTitle:@"7. 死锁"
                    subtitle:@"主线程同步等待"
                       color:[UIColor.systemGreenColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashDeadlock)];
    yOffset += (buttonHeight + spacing);
    
    // 8. 内存访问错误
    [self addButtonWithTitle:@"8. 内存访问错误"
                    subtitle:@"错误的指针访问"
                       color:[UIColor.systemYellowColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashMemoryAccessError)];
    yOffset += (buttonHeight + spacing);
    
    // 9. 浮点异常
    [self addButtonWithTitle:@"9. 浮点异常"
                    subtitle:@"除以零"
                       color:[UIColor.systemPinkColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashFloatingPoint)];
    yOffset += (buttonHeight + spacing);
    
    // 10. 主线程卡顿（模拟）
    [self addButtonWithTitle:@"10. 主线程卡顿(模拟)"
                    subtitle:@"阻塞5秒"
                       color:[UIColor.systemGrayColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(simulateMainThreadBlock)];
    yOffset += (buttonHeight + spacing);
    
    // 11. 总线错误
    [self addButtonWithTitle:@"11. 总线错误"
                    subtitle:@"SIGBUS"
                       color:[UIColor.systemBrownColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashBusError)];
    yOffset += (buttonHeight + spacing);
    
    // 12. 非法指令
    [self addButtonWithTitle:@"12. 非法指令"
                    subtitle:@"SIGILL"
                       color:[UIColor.systemCyanColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashIllegalInstruction)];
    yOffset += (buttonHeight + spacing);
    
    // 13. 管道破裂
    [self addButtonWithTitle:@"13. 管道破裂"
                    subtitle:@"SIGPIPE"
                       color:[UIColor.systemMintColor colorWithAlphaComponent:0.9]
                       frame:CGRectMake(20, yOffset, buttonWidth, buttonHeight)
                      action:@selector(crashBrokenPipe)];
    yOffset += (buttonHeight + spacing);
    
    // 安全区域底部间距
    yOffset += 20;
    
    // 设置ScrollView内容大小
    self.scrollView.contentSize = CGSizeMake(self.blankView.bounds.size.width, yOffset);
}

- (void)addButtonWithTitle:(NSString *)title
                  subtitle:(NSString *)subtitle
                     color:(UIColor *)color
                     frame:(CGRect)frame
                    action:(SEL)action {
    
    // 创建容器视图
    UIView *buttonContainer = [[UIView alloc] initWithFrame:frame];
    buttonContainer.backgroundColor = [color colorWithAlphaComponent:0.1];
    buttonContainer.layer.cornerRadius = 12;
    buttonContainer.layer.borderWidth = 1;
    buttonContainer.layer.borderColor = [color colorWithAlphaComponent:0.3].CGColor;
    
    // 创建按钮
    UIButton *button = [UIButton buttonWithType:UIButtonTypeSystem];
    button.frame = CGRectMake(0, 0, frame.size.width, frame.size.height);
    button.backgroundColor = [UIColor clearColor];
    [button addTarget:self action:action forControlEvents:UIControlEventTouchUpInside];
    
    // 创建垂直堆栈视图
    UIStackView *stackView = [[UIStackView alloc] initWithFrame:CGRectMake(15, 0, frame.size.width - 30, frame.size.height)];
    stackView.axis = UILayoutConstraintAxisVertical;
    stackView.alignment = UIStackViewAlignmentLeading;
    stackView.distribution = UIStackViewDistributionFillEqually;
    stackView.spacing = 2;
    
    // 主标题
    UILabel *titleLabel = [[UILabel alloc] init];
    titleLabel.text = title;
    titleLabel.font = [UIFont systemFontOfSize:16 weight:UIFontWeightSemibold];
    titleLabel.textColor = [UIColor labelColor];
    titleLabel.textAlignment = NSTextAlignmentLeft;
    
    // 副标题
    UILabel *subtitleLabel = [[UILabel alloc] init];
    subtitleLabel.text = subtitle;
    subtitleLabel.font = [UIFont systemFontOfSize:13 weight:UIFontWeightRegular];
    subtitleLabel.textColor = [color colorWithAlphaComponent:0.8];
    subtitleLabel.textAlignment = NSTextAlignmentLeft;
    
    [stackView addArrangedSubview:titleLabel];
    [stackView addArrangedSubview:subtitleLabel];
    
    [buttonContainer addSubview:stackView];
    [buttonContainer addSubview:button];
    [self.scrollView addSubview:buttonContainer];
    
    [self.crashButtons addObject:button];
    
    // 添加触摸反馈效果
    UILongPressGestureRecognizer *longPress = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(handleButtonLongPress:)];
    longPress.minimumPressDuration = 0.1;
    [button addGestureRecognizer:longPress];
}

#pragma mark - 按钮长按效果

- (void)handleButtonLongPress:(UILongPressGestureRecognizer *)gesture {
    UIView *button = gesture.view;
    
}

#pragma mark - Crash 方法实现

// MARK: 1. Objective-C异常 - 数组越界
- (void)crashObjectiveCArrayOutOfBounds {
    NSLog(@"即将触发: Objective-C数组越界异常");
    
    // 显示警告
    [self showWarningAlertWithMessage:@"将触发数组越界异常\nNSRangeException"];
    
    // 延迟执行，让警告先显示
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        @try {
            NSArray *array = @[@1, @2, @3];
            // 越界访问
            NSNumber *number = array[10];
            NSLog(@"这行不会执行: %@", number);
        } @catch (NSException *exception) {
            // 重新抛出，让崩溃处理器捕获
            @throw exception;
        }
    });
}

// MARK: 2. Objective-C异常 - 消息发送给已释放对象
- (void)crashObjectiveCUnrecognizedSelector {
    NSLog(@"即将触发: Objective-C unrecognized selector");
    
    [self showWarningAlertWithMessage:@"将触发unrecognized selector异常"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 创建一个对象，然后释放它，再发送消息
        @autoreleasepool {
            NSString *string = [[NSString alloc] initWithString:@"test"];
            // 强制转换为id类型，然后调用不存在的方法
            id object = (id)string;
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-performSelector-leaks"
            [object performSelector:@selector(thisMethodDoesNotExist)];
#pragma clang diagnostic pop
        }
    });
}

// MARK: 3. 信号崩溃 - SIGSEGV (段错误)
- (void)crashSignalSegmentationFault {
    NSLog(@"即将触发: SIGSEGV段错误");
    
    [self showWarningAlertWithMessage:@"将触发SIGSEGV段错误\n访问非法内存地址"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 访问空指针
        volatile int *nullPointer = NULL;
        *nullPointer = 42;
    });
}

// MARK: 4. 信号崩溃 - SIGABRT (中止信号)
- (void)crashSignalAbort {
    NSLog(@"即将触发: SIGABRT中止信号");
    
    [self showWarningAlertWithMessage:@"将触发SIGABRT中止信号\n调用abort()函数"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 调用abort函数
        abort();
    });
}

// MARK: 5. C++异常
- (void)crashCppException {
    NSLog(@"即将触发: C++异常");
    
    [self showWarningAlertWithMessage:@"将触发C++异常\nstd::runtime_error"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 在C++上下文中抛出异常
        try {
            throw std::runtime_error("This is a test C++ exception from iOS app");
        } catch (...) {
            // 不处理，让terminate handler捕获
            std::terminate();
        }
    });
}

// MARK: 6. 堆栈溢出
- (void)crashStackOverflow {
    NSLog(@"即将触发: 堆栈溢出");
    
    [self showWarningAlertWithMessage:@"将触发堆栈溢出\n无限递归"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.64 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        [self recursiveFunction:0];
    });
}

- (void)recursiveFunction:(int)depth {
    // 没有终止条件，导致无限递归
    char buffer[1024]; // 分配栈空间加速溢出
    memset(buffer, 0, sizeof(buffer));
    
    // 递归调用
    [self recursiveFunction:depth + 1];
}

// MARK: 7. 死锁
- (void)crashDeadlock {
    NSLog(@"即将触发: 死锁");
    
    [self showWarningAlertWithMessage:@"将触发死锁\n主线程同步等待自己"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 主线程同步等待自己，导致死锁
        dispatch_sync(dispatch_get_main_queue(), ^{
            NSLog(@"这行永远不会执行");
        });
    });
}

// MARK: 8. 内存访问错误
- (void)crashMemoryAccessError {
    NSLog(@"即将触发: 内存访问错误");
    
    [self showWarningAlertWithMessage:@"将触发内存访问错误\n访问已释放内存"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 创建一个对象，释放它，然后访问
        NSObject * __unsafe_unretained unsafeObject;
        @autoreleasepool {
            NSObject *object = [[NSObject alloc] init];
            unsafeObject = object;
        }
        // 此时object已被释放，unsafeObject是野指针
        NSLog(@"访问野指针: %@", unsafeObject);
    });
}

// MARK: 9. 浮点异常
- (void)crashFloatingPoint {
    NSLog(@"即将触发: 浮点异常");
    
    [self showWarningAlertWithMessage:@"将触发浮点异常\n除以零"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 整数除以零会导致SIGFPE
        volatile int x = 10;
        volatile int y = 0;
        volatile int z = x / y;
        NSLog(@"结果: %d", z); // 不会执行
    });
}

// MARK: 10. 主线程卡顿（模拟）
- (void)simulateMainThreadBlock {
    NSLog(@"模拟主线程卡顿");
    
    [self showWarningAlertWithMessage:@"将模拟主线程卡顿\n阻塞5秒"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        NSLog(@"开始阻塞主线程...");
        
        // 创建一个阻塞主线程的视图
        UIView *blockingView = [[UIView alloc] initWithFrame:self.blankView.bounds];
        blockingView.backgroundColor = [UIColor colorWithWhite:0 alpha:0.7];
        
        UILabel *label = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 300, 100)];
        label.center = blockingView.center;
        label.text = @"主线程阻塞中...\n5秒后恢复";
        label.textColor = [UIColor whiteColor];
        label.textAlignment = NSTextAlignmentCenter;
        label.numberOfLines = 0;
        label.font = [UIFont boldSystemFontOfSize:20];
        
        [blockingView addSubview:label];
        [self.blankView addSubview:blockingView];
        
        // 阻塞主线程5秒
        [NSThread sleepForTimeInterval:5.0];
        
        // 恢复
        [blockingView removeFromSuperview];
        NSLog(@"主线程恢复");
        
        UIAlertController *alert = [UIAlertController
            alertControllerWithTitle:@"主线程已恢复"
                             message:@"阻塞5秒已完成，主线程恢复正常"
                      preferredStyle:UIAlertControllerStyleAlert];
        [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
        [self presentViewController:alert animated:YES completion:nil];
    });
}

// MARK: 11. 总线错误
- (void)crashBusError {
    NSLog(@"即将触发: 总线错误");
    
    [self showWarningAlertWithMessage:@"将触发总线错误SIGBUS\n未对齐的内存访问"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 尝试访问未对齐的内存地址（在某些架构上会触发SIGBUS）
        char *memory = (char *)malloc(10);
        // 在某些平台上，访问奇数地址的int会触发总线错误
        int *unalignedPointer = (int *)(memory + 1);
        *unalignedPointer = 42;
        free(memory);
    });
}

// MARK: 12. 非法指令
- (void)crashIllegalInstruction {
    NSLog(@"即将触发: 非法指令");
    
    [self showWarningAlertWithMessage:@"将触发非法指令SIGILL"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 在某些平台上，可以尝试执行非法指令
        // 注意：这种方法不一定在所有设备上都有效
#if defined(__arm__) || defined(__arm64__)
        __asm__ volatile (".word 0xf7f0a000" : : ); // 未定义的ARM指令
#elif defined(__i386__) || defined(__x86_64__)
        __asm__ volatile ("ud2" : : ); // x86未定义指令
#endif
    });
}

// MARK: 13. 管道破裂
- (void)crashBrokenPipe {
    NSLog(@"即将触发: 管道破裂");
    
    [self showWarningAlertWithMessage:@"将触发管道破裂SIGPIPE\n写入已关闭的管道"];
    
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        // 创建一个管道，关闭读取端，然后写入
        int pipefd[2];
        if (pipe(pipefd) == 0) {
            close(pipefd[0]); // 关闭读取端
            const char *msg = "test";
            write(pipefd[1], msg, strlen(msg)); // 这会触发SIGPIPE
            close(pipefd[1]);
        }
    });
}

#pragma mark - 警告弹窗

- (void)showWarningAlertWithMessage:(NSString *)message {
    UIAlertController *alert = [UIAlertController
        alertControllerWithTitle:@"⚠️ 警告"
                         message:message
                  preferredStyle:UIAlertControllerStyleAlert];
    
    [alert addAction:[UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:nil]];
    [alert addAction:[UIAlertAction actionWithTitle:@"取消" style:UIAlertActionStyleCancel handler:nil]];
    
    [self presentViewController:alert animated:YES completion:nil];
}

#pragma mark - 辅助方法

- (void)showCrashLogs {
    // 显示崩溃日志的方法
    UIAlertController *alert = [UIAlertController
        alertControllerWithTitle:@"崩溃日志"
                         message:@"查看崩溃日志功能需要集成CrashLogManager"
                  preferredStyle:UIAlertControllerStyleAlert];
    
    [alert addAction:[UIAlertAction actionWithTitle:@"确定" style:UIAlertActionStyleDefault handler:nil]];
    [self presentViewController:alert animated:YES completion:nil];
}

@end

```

