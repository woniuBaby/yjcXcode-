### AVFoundation框架

1.AVAsset：用于获取一个多媒体文件的信息，至关于获取一个视频或音频文件，是一个抽象类，不能直接使用。数组

2.AVURLAsset：AVAsset的子类，经过URL路径建立一个包含多媒体信息的对象。session

```
NSURL *url = <#A URL that identifies an audiovisual asset such as a movie file#>;
AVURLAsset *anAsset = [[AVURLAsset alloc] initWithURL:url options:nil];
```

3.AVCaptureSession：用于捕捉视频和音频，负责协调视频和音频的输入流和输出流。app

```
AVCaptureSession *captureSession = [[AVCaptureSession alloc] init]; 
if ([_captureSession canSetSessionPreset:AVCaptureSessionPreset1280x720]) _captureSession.sessionPreset = AVCaptureSessionPreset1280x720;
```

4.AVCaptureDevice：表示输入设备，如照相机或麦克风。框架

```
AVCaptureDevice *device = [self getCameraDeviceWithPosition:AVCaptureDevicePositionBack];

- (AVCaptureDevice *)getCameraDeviceWithPosition:(AVCaptureDevicePosition)position {
    NSArray *devices = [AVCaptureDevice devicesWithMediaType:AVMediaTypeVideo];
    for (AVCaptureDevice *device in devices) {
        if ([device position] == position) {
            if ([device supportsAVCaptureSessionPreset:AVCaptureSessionPreset1280x720]) return device;
            return nil;
        }
    }
    return nil;
}
```

5.AVCaptureDeviceInput：视频或音频的输入流，把该对象添加到AVCaptureSession对象中管理。async

```
NSError *error;
AVCaptureDeviceInput *input =
        [AVCaptureDeviceInput deviceInputWithDevice:device error:&error];
if (!input) {
    // Handle the error appropriately.
}
if ([captureSession canAddInput:captureDeviceInput]) {
    [captureSession addInput:captureDeviceInput];
}
```

6.AVCaptureOutput：视频或音频的输出流，一般使用它的子类：AVCaptureAudioDataOutput，AVCaptureVideoDataOutput，AVCaptureStillImageOutput，AVCaptureFileOutput等，把该对象添加到AVCaptureSession对象中管理。ide

```
AVCaptureMovieFileOutput *movieOutput = [[AVCaptureMovieFileOutput alloc] init];
if ([captureSession canAddOutput:movieOutput]) {
    [captureSession addOutput:movieOutput];
}
```

7.AVCaptureVideoPreviewLayer：预览图层，实时查看摄像头捕捉的画面。ui

```
AVCaptureVideoPreviewLayer *captureVideoPreviewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:captureSession];
captureVideoPreviewLayer.videoGravity = AVLayerVideoGravityResizeAspectFill;
captureVideoPreviewLayer.frame = <#Set layer frame#>;
```

8.AVCaptureConnection：AVCaptureSession和输入输出流之间的链接，能够用来调节一些设置，如光学防抖。atom

```
AVCaptureConnection *captureConnection = [movieOutput connectionWithMediaType:AVMediaTypeVideo];
// 打开影院级光学防抖
captureConnection.preferredVideoStabilizationMode = AVCaptureVideoStabilizationModeCinematic;
```

9.AVCaptureDeviceFormat：输入设备的一些设置，能够用来修改一些设置，如ISO，慢动做，防抖等。url

```
AVCaptureDevice *captureDevice = [self.captureDeviceInput device];
NSError *error;
if ([captureDevice lockForConfiguration:&error]) {
    CGFloat minISO = captureDevice.activeFormat.minISO;
    CGFloat maxISO = captureDevice.activeFormat.maxISO;
    // 调节ISO为全范围的70%
    CGFloat currentISO = (maxISO - minISO) * 0.7 + minISO;
    [captureDevice setExposureModeCustomWithDuration:AVCaptureExposureDurationCurrent ISO:currentISO completionHandler:nil];
    [captureDevice unlockForConfiguration];
}else{
    // Handle the error appropriately.
}
```



### 初始化相机

```
/// 负责输入和输出设备之间的数据传递
@property (nonatomic, strong) AVCaptureSession *captureSession;
/// 负责从AVCaptureDevice得到视频输入流
@property (nonatomic, strong) AVCaptureDeviceInput *captureDeviceInput;
/// 负责从AVCaptureDevice得到音频输入流
@property (nonatomic, strong) AVCaptureDeviceInput *audioCaptureDeviceInput;
/// 视频输出流
@property (nonatomic, strong) AVCaptureMovieFileOutput *captureMovieFileOutput;
/// 相机拍摄预览图层
@property (nonatomic, strong) AVCaptureVideoPreviewLayer *captureVideoPreviewLayer;

... 公用方法

/// 获取摄像头设备
- (AVCaptureDevice *)getCameraDeviceWithPosition:(AVCaptureDevicePosition)position {
    NSArray *devices = [AVCaptureDevice devicesWithMediaType:AVMediaTypeVideo];
    for (AVCaptureDevice *device in devices) {
        if ([device position] == position) {
            if ([device supportsAVCaptureSessionPreset:AVCaptureSessionPreset1280x720]) return device;
            return nil;
        }
    }
    return nil;
}

... 建立自定义相机

// 建立AVCaptureSession
_captureSession = [[AVCaptureSession alloc] init];
if ([_captureSession canSetSessionPreset:AVCaptureSessionPreset1280x720]) _captureSession.sessionPreset = AVCaptureSessionPreset1280x720;

// 获取摄像设备
AVCaptureDevice *videoCaptureDevice = [self getCameraDeviceWithPosition:AVCaptureDevicePositionBack];
if (!videoCaptureDevice)  {
    // Handle the error appropriately.
}

// 获取视频输入流
NSError *error = nil;
_captureDeviceInput = [AVCaptureDeviceInput deviceInputWithDevice:videoCaptureDevice error:&error];
if (error) {
    // Handle the error appropriately.
}

// 获取录音设备
AVCaptureDevice *audioCaptureDevice = [[AVCaptureDevice devicesWithMediaType:AVMediaTypeAudio] firstObject];

// 获取音频输入流
_audioCaptureDeviceInput = [AVCaptureDeviceInput deviceInputWithDevice:audioCaptureDevice error:&error];
if (error) {
    // Handle the error appropriately.
}

// 将视频和音频输入添加到AVCaptureSession
if ([_captureSession canAddInput:_captureDeviceInput] && [_captureSession canAddInput:_audioCaptureDeviceInput]) {
    [_captureSession addInput:_captureDeviceInput];
    [_captureSession addInput:_audioCaptureDeviceInput];
}

// 建立输出流
_captureMovieFileOutput = [[AVCaptureMovieFileOutput alloc] init];

// 将输出流添加到AVCaptureSession
if ([_captureSession canAddOutput:_captureMovieFileOutput]) {
    [_captureSession addOutput:_captureMovieFileOutput];
    // 根据设备输出得到链接
    AVCaptureConnection *captureConnection = [_captureMovieFileOutput connectionWithMediaType:AVMediaTypeVideo];
    // 判断是否支持光学防抖
    if ([videoCaptureDevice.activeFormat isVideoStabilizationModeSupported:AVCaptureVideoStabilizationModeCinematic]) {
        // 若是支持防抖就打开防抖
        captureConnection.preferredVideoStabilizationMode = AVCaptureVideoStabilizationModeCinematic;
    }
}

// 保存默认的AVCaptureDeviceFormat
// 之因此保存是由于修改摄像头捕捉频率以后，防抖就没法再次开启，试了下只可以用这个默认的format才能够，因此把它存起来，关闭慢动做拍摄后在设置会默认的format开启防抖
_defaultFormat = videoCaptureDevice.activeFormat;
_defaultMinFrameDuration = videoCaptureDevice.activeVideoMinFrameDuration;
_defaultMaxFrameDuration = videoCaptureDevice.activeVideoMaxFrameDuration;

// 建立预览图层
_captureVideoPreviewLayer = [[AVCaptureVideoPreviewLayer alloc] initWithSession:_captureSession];
_captureVideoPreviewLayer.videoGravity = AVLayerVideoGravityResizeAspectFill;//填充模式
_captureVideoPreviewLayer.frame = self.bounds;

// 相机的预览图层是一个CALayer，因此能够建立一个UIView，在view的layer上addSublayer就能够
// 由于这里是写在view的init方法里，因此直接调用了self.layer的addSublayer方法
[self.layer addSublayer:_captureVideoPreviewLayer];

// 开始捕获
[self.captureSession startRunning];
```



### 配置操做界面

能够在相机的预览图层所在的view上面直接addSubview咱们须要的视图，个人作法是直接建立一个和当前预览图层同样大的UIView作控制面板，背景色为透明。而后总体盖在相机预览图层上面，全部的手势方法，按钮点击等都在咱们的控制面板上做响应，具体代码其实就是经过代理传递控制面板的操做让相机界面去作对应的处理，这里就不贴无用代码了。spa



### 相机设置

1.切换到后摄像头

```
#pragma mark - 切换到后摄像头
- (void)cameraBackgroundDidClickChangeBack {
    AVCaptureDevice *toChangeDevice;
    AVCaptureDevicePosition toChangePosition = AVCaptureDevicePositionBack;
    toChangeDevice = [self getCameraDeviceWithPosition:toChangePosition];
    AVCaptureDeviceInput *toChangeDeviceInput = [AVCaptureDeviceInput deviceInputWithDevice:toChangeDevice error:nil];
    [self.captureSession beginConfiguration];
    [self.captureSession removeInput:self.captureDeviceInput];
    if ([self.captureSession canAddInput:toChangeDeviceInput]) {
        [self.captureSession addInput:toChangeDeviceInput];
        self.captureDeviceInput = toChangeDeviceInput;
    }
    [self.captureSession commitConfiguration];
}
```

2.切换到前摄像头

```
- (void)cameraBackgroundDidClickChangeFront {
    AVCaptureDevice *toChangeDevice;
    AVCaptureDevicePosition toChangePosition = AVCaptureDevicePositionFront;
    toChangeDevice = [self getCameraDeviceWithPosition:toChangePosition];
    AVCaptureDeviceInput *toChangeDeviceInput = [AVCaptureDeviceInput deviceInputWithDevice:toChangeDevice error:nil];
    [self.captureSession beginConfiguration];
    [self.captureSession removeInput:self.captureDeviceInput];
    if ([self.captureSession canAddInput:toChangeDeviceInput]) {
        [self.captureSession addInput:toChangeDeviceInput];
        self.captureDeviceInput = toChangeDeviceInput;
    }
    [self.captureSession commitConfiguration];
}
```

3.打开闪光灯

```
- (void)cameraBackgroundDidClickOpenFlash {
    AVCaptureDevice *captureDevice = [self.captureDeviceInput device];
    NSError *error;
    if ([captureDevice lockForConfiguration:&error]) {
        if ([captureDevice isTorchModeSupported:AVCaptureTorchModeOn]) [captureDevice setTorchMode:AVCaptureTorchModeOn];
    }else{
        // Handle the error appropriately.
    }
}
```

4.关闭闪光灯

```
- (void)cameraBackgroundDidClickCloseFlash {
    AVCaptureDevice *captureDevice = [self.captureDeviceInput device];
    NSError *error;
    if ([captureDevice lockForConfiguration:&error]) {
        if ([captureDevice isTorchModeSupported:AVCaptureTorchModeOff]) [captureDevice setTorchMode:AVCaptureTorchModeOff];
    }else{
        // Handle the error appropriately.
    }
}
```

5.调节焦距

```
// 焦距范围0.0-1.0
- (void)cameraBackgroundDidChangeFocus:(CGFloat)focus {
    AVCaptureDevice *captureDevice = [self.captureDeviceInput device];
    NSError *error;
    if ([captureDevice lockForConfiguration:&error]) {
        if ([captureDevice isFocusModeSupported:AVCaptureFocusModeContinuousAutoFocus]) [captureDevice setFocusModeLockedWithLensPosition:focus completionHandler:nil];
    }else{
        // Handle the error appropriately.
    }
}
```

6.数码变焦

```
// 数码变焦 1-3倍
- (void)cameraBackgroundDidChangeZoom:(CGFloat)zoom {
    AVCaptureDevice *captureDevice = [self.captureDeviceInput device];
    NSError *error;
    if ([captureDevice lockForConfiguration:&error]) {
        [captureDevice rampToVideoZoomFactor:zoom withRate:50];
    }else{
        // Handle the error appropriately.
    }
}
```

7.调节ISO，光感度

```
// 调节ISO，光感度 0.0-1.0
- (void)cameraBackgroundDidChangeISO:(CGFloat)iso {
    AVCaptureDevice *captureDevice = [self.captureDeviceInput device];
    NSError *error;
    if ([captureDevice lockForConfiguration:&error]) {
        CGFloat minISO = captureDevice.activeFormat.minISO;
        CGFloat maxISO = captureDevice.activeFormat.maxISO;
        CGFloat currentISO = (maxISO - minISO) * iso + minISO;
        [captureDevice setExposureModeCustomWithDuration:AVCaptureExposureDurationCurrent ISO:currentISO completionHandler:nil];
        [captureDevice unlockForConfiguration];
    }else{
        // Handle the error appropriately.
    }
}
```

8.点击屏幕自动对焦

```
// 当前屏幕上点击的点坐标
- (void)cameraBackgroundDidTap:(CGPoint)point {
    AVCaptureDevice *captureDevice = [self.captureDeviceInput device];
    NSError *error;
    if ([captureDevice lockForConfiguration:&error]) {
        CGPoint location = point;
        CGPoint pointOfInerest = CGPointMake(0.5, 0.5);
        CGSize frameSize = self.captureVideoPreviewLayer.frame.size;
        if ([captureDevice position] == AVCaptureDevicePositionFront) location.x = frameSize.width - location.x;
        pointOfInerest = CGPointMake(location.y / frameSize.height, 1.f - (location.x / frameSize.width));
        [self focusWithMode:AVCaptureFocusModeAutoFocus exposureMode:AVCaptureExposureModeAutoExpose atPoint:pointOfInerest];

        [[self.captureDeviceInput device] addObserver:self forKeyPath:@"ISO" options:NSKeyValueObservingOptionNew context:NULL];
    }else{
        // Handle the error appropriately.
    }
}

-(void)focusWithMode:(AVCaptureFocusMode)focusMode exposureMode:(AVCaptureExposureMode)exposureMode atPoint:(CGPoint)point{
    AVCaptureDevice *captureDevice = [self.captureDeviceInput device];
    NSError *error;
    if ([captureDevice lockForConfiguration:&error]) {
        if ([captureDevice isFocusModeSupported:focusMode]) [captureDevice setFocusMode:AVCaptureFocusModeAutoFocus];
        if ([captureDevice isFocusPointOfInterestSupported]) [captureDevice setFocusPointOfInterest:point];
        if ([captureDevice isExposureModeSupported:exposureMode]) [captureDevice setExposureMode:AVCaptureExposureModeAutoExpose];
        if ([captureDevice isExposurePointOfInterestSupported]) [captureDevice setExposurePointOfInterest:point];
    }else{
        // Handle the error appropriately.
    }
}
```

9.获取录制时视频的方向
由于相机的特殊性，不可以用常规的控制器的方向来获取当前的方向，由于用户可能关闭屏幕旋转，这里用重力感应来计算当前手机的放置状态。

```
@property (nonatomic, strong) CMMotionManager *motionManager;
@property (nonatomic, assign) UIDeviceOrientation deviceOrientation;
...

_motionManager = [[CMMotionManager alloc] init];
_motionManager.deviceMotionUpdateInterval = 1/15.0;
if (_motionManager.deviceMotionAvailable) {
    [_motionManager startDeviceMotionUpdatesToQueue:[NSOperationQueue currentQueue] withHandler:^(CMDeviceMotion * _Nullable motion, NSError * _Nullable error) {
        [self performSelectorOnMainThread:@selector(handleDeviceMotion:) withObject:motion waitUntilDone:YES];
    }];
} else {
    NSLog(@"No device motion on device");
}

...

/// 重力感应回调
- (void)handleDeviceMotion:(CMDeviceMotion *)deviceMotion{
    double x = deviceMotion.gravity.x;
    double y = deviceMotion.gravity.y;

    CGAffineTransform videoTransform;

    if (fabs(y) >= fabs(x)) {
        if (y >= 0) {
            videoTransform = CGAffineTransformMakeRotation(M_PI);
            _deviceOrientation = UIDeviceOrientationPortraitUpsideDown;
        } else {
            videoTransform = CGAffineTransformMakeRotation(0);
            _deviceOrientation = UIDeviceOrientationPortrait;
        }
    } else {
        if (x >= 0) {
            videoTransform = CGAffineTransformMakeRotation(-M_PI_2);
            _deviceOrientation = UIDeviceOrientationLandscapeRight;    // Home键左侧水平拍摄
        } else {
            videoTransform = CGAffineTransformMakeRotation(M_PI_2);
            _deviceOrientation = UIDeviceOrientationLandscapeLeft;     // Home键右侧水平拍摄
        }
    }
    // 告诉操做界面当前屏幕的方向，作按钮跟随屏幕方向旋转的操做
    [self.backgroundView setOrientation:_deviceOrientation];
}
```

11.慢动做拍摄

```
- (void)cameraBackgroundDidClickOpenSlow {
    [self.captureSession stopRunning];
    CGFloat desiredFPS = 240.0;
    AVCaptureDevice *videoDevice = self.captureDeviceInput.device;
    AVCaptureDeviceFormat *selectedFormat = nil;
    int32_t maxWidth = 0;
    AVFrameRateRange *frameRateRange = nil;
    for (AVCaptureDeviceFormat *format in [videoDevice formats]) {
        for (AVFrameRateRange *range in format.videoSupportedFrameRateRanges) {
            CMFormatDescriptionRef desc = format.formatDescription;
            CMVideoDimensions dimensions = CMVideoFormatDescriptionGetDimensions(desc);
            int32_t width = dimensions.width;
            if (range.minFrameRate <= desiredFPS && desiredFPS <= range.maxFrameRate && width >= maxWidth) {
                selectedFormat = format;
                frameRateRange = range;
                maxWidth = width;
            }
        }
    }
    if (selectedFormat) {
        if ([videoDevice lockForConfiguration:nil]) {
            NSLog(@"selected format: %@", selectedFormat);
            videoDevice.activeFormat = selectedFormat;
            videoDevice.activeVideoMinFrameDuration = CMTimeMake(1, (int32_t)desiredFPS);
            videoDevice.activeVideoMaxFrameDuration = CMTimeMake(1, (int32_t)desiredFPS);
            [videoDevice unlockForConfiguration];
        }
    }
    [self.captureSession startRunning];
}
```

12.慢动做拍摄关

```
- (void)cameraBackgroundDidClickCloseSlow {
    [self.captureSession stopRunning];
    CGFloat desiredFPS = 60.0;
    AVCaptureDevice *videoDevice = self.captureDeviceInput.device;
    AVCaptureDeviceFormat *selectedFormat = nil;
    int32_t maxWidth = 0;
    AVFrameRateRange *frameRateRange = nil;
    for (AVCaptureDeviceFormat *format in [videoDevice formats]) {
        for (AVFrameRateRange *range in format.videoSupportedFrameRateRanges) {
            CMFormatDescriptionRef desc = format.formatDescription;
            CMVideoDimensions dimensions = CMVideoFormatDescriptionGetDimensions(desc);
            int32_t width = dimensions.width;
            if (range.minFrameRate <= desiredFPS && desiredFPS <= range.maxFrameRate && width >= maxWidth) {
                selectedFormat = format;
                frameRateRange = range;
                maxWidth = width;
            }
        }
    }
    if (selectedFormat) {
        if ([videoDevice lockForConfiguration:nil]) {
            NSLog(@"selected format: %@", selectedFormat);
            videoDevice.activeFormat = _defaultFormat;
            videoDevice.activeVideoMinFrameDuration = _defaultMinFrameDuration;
            videoDevice.activeVideoMaxFrameDuration = _defaultMaxFrameDuration;
            [videoDevice unlockForConfiguration];
        }
    }
    [self.captureSession startRunning];
}
```

13.防抖开启

```
- (void)cameraBackgroundDidClickOpenAntiShake {
    AVCaptureConnection *captureConnection = [_captureMovieFileOutput connectionWithMediaType:AVMediaTypeVideo];
    NSLog(@"change captureConnection: %@", captureConnection);
    AVCaptureDevice *videoDevice = self.captureDeviceInput.device;
    NSLog(@"set format: %@", videoDevice.activeFormat);
    if ([videoDevice.activeFormat isVideoStabilizationModeSupported:AVCaptureVideoStabilizationModeCinematic]) {
        captureConnection.preferredVideoStabilizationMode = AVCaptureVideoStabilizationModeCinematic;
    }
}
```

14.防抖关闭

```
#pragma mark - 防抖关
- (void)cameraBackgroundDidClickCloseAntiShake {
    AVCaptureConnection *captureConnection = [_captureMovieFileOutput connectionWithMediaType:AVMediaTypeVideo];
    NSLog(@"change captureConnection: %@", captureConnection);
    AVCaptureDevice *videoDevice = self.captureDeviceInput.device;
    if ([videoDevice.activeFormat isVideoStabilizationModeSupported:AVCaptureVideoStabilizationModeOff]) {
        captureConnection.preferredVideoStabilizationMode = AVCaptureVideoStabilizationModeOff;
    }
}
```

15.录制视频

```
#pragma mark - 录制
- (void)cameraBackgroundDidClickPlay {
    // 根据设备输出得到链接
    AVCaptureConnection *captureConnection = [self.captureMovieFileOutput connectionWithMediaType:AVMediaTypeVideo];
    // 根据链接取得设备输出的数据
    if (![self.captureMovieFileOutput isRecording]) {
        captureConnection.videoOrientation = (AVCaptureVideoOrientation)_deviceOrientation; // 视频方向和手机方向一致
        NSString *outputFilePath = [kCachePath stringByAppendingPathComponent:[self movieName]];
        NSURL *fileURL = [NSURL fileURLWithPath:outputFilePath];
        [self.captureMovieFileOutput startRecordingToOutputFileURL:fileURL recordingDelegate:self];
        _currentMoviePath = outputFilePath;
    }
}

- (void)captureOutput:(AVCaptureFileOutput *)captureOutput didStartRecordingToOutputFileAtURL:(NSURL *)fileURL fromConnections:(NSArray *)connections {
    NSLog(@"开始录制");
}

- (void)captureOutput:(AVCaptureFileOutput *)captureOutput didFinishRecordingToOutputFileAtURL:(NSURL *)outputFileURL fromConnections:(NSArray *)connections error:(NSError *)error {
    NSLog(@"录制完成");
}
```

16.暂停录制

```
[self.captureMovieFileOutput stopRecording];
```

17.调节视频的速度
慢动做拍摄的时候要调节摄像头的捕捉频率，快速的时候直接调节视频速度就能够了。
慢动做下拍摄的视频视频的播放时长仍是实际拍摄的时间，这里根据设置的慢速倍率，把视频的时长拉长。

```
/// 处理速度视频
- (void)setSpeedWithVideo:(NSDictionary *)video completed:(void(^)())completed {
    dispatch_async(dispatch_get_global_queue(0, 0), ^{
        NSLog(@"video set thread: %@", [NSThread currentThread]);
        // 获取视频
        AVURLAsset* videoAsset = [[AVURLAsset alloc]initWithURL:[NSURL fileURLWithPath:video[kMoviePath]] options:nil];
        // 视频混合
        AVMutableComposition* mixComposition = [AVMutableComposition composition];
        // 视频轨道
        AVMutableCompositionTrack *compositionVideoTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeVideo preferredTrackID:kCMPersistentTrackID_Invalid];
        // 音频轨道
        AVMutableCompositionTrack *compositionAudioTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeAudio preferredTrackID:kCMPersistentTrackID_Invalid];

        // 视频的方向
        CGAffineTransform videoTransform = [videoAsset tracksWithMediaType:AVMediaTypeVideo].lastObject.preferredTransform;
        if (videoTransform.a == 0 && videoTransform.b == 1.0 && videoTransform.c == -1.0 && videoTransform.d == 0) {
            NSLog(@"垂直拍摄");
            videoTransform = CGAffineTransformMakeRotation(M_PI_2);
        }else if (videoTransform.a == 0 && videoTransform.b == -1.0 && videoTransform.c == 1.0 && videoTransform.d == 0) {
            NSLog(@"倒立拍摄");
            videoTransform = CGAffineTransformMakeRotation(-M_PI_2);
        }else if (videoTransform.a == 1.0 && videoTransform.b == 0 && videoTransform.c == 0 && videoTransform.d == 1.0) {
            NSLog(@"Home键右侧水平拍摄");
            videoTransform = CGAffineTransformMakeRotation(0);
        }else if (videoTransform.a == -1.0 && videoTransform.b == 0 && videoTransform.c == 0 && videoTransform.d == -1.0) {
            NSLog(@"Home键左侧水平拍摄");
            videoTransform = CGAffineTransformMakeRotation(M_PI);
        }
        // 根据视频的方向同步视频轨道方向
        compositionVideoTrack.preferredTransform = videoTransform;
        compositionVideoTrack.naturalTimeScale = 600;

        // 插入视频轨道
        [compositionVideoTrack insertTimeRange:CMTimeRangeMake(kCMTimeZero, CMTimeMake(videoAsset.duration.value, videoAsset.duration.timescale)) ofTrack:[[videoAsset tracksWithMediaType:AVMediaTypeVideo] firstObject] atTime:kCMTimeZero error:nil];
        // 插入音频轨道
        [compositionAudioTrack insertTimeRange:CMTimeRangeMake(kCMTimeZero, CMTimeMake(videoAsset.duration.value, videoAsset.duration.timescale)) ofTrack:[[videoAsset tracksWithMediaType:AVMediaTypeAudio] firstObject] atTime:kCMTimeZero error:nil];

        // 适配视频速度比率
        CGFloat scale = 1.0;
        if([video[kMovieSpeed] isEqualToString:kMovieSpeed_Fast]){
            scale = 0.2f;  // 快速 x5
        } else if ([video[kMovieSpeed] isEqualToString:kMovieSpeed_Slow]) {
            scale = 4.0f;  // 慢速 x4
        }

        // 根据速度比率调节音频和视频
        [compositionVideoTrack scaleTimeRange:CMTimeRangeMake(kCMTimeZero, CMTimeMake(videoAsset.duration.value, videoAsset.duration.timescale)) toDuration:CMTimeMake(videoAsset.duration.value * scale , videoAsset.duration.timescale)];
        [compositionAudioTrack scaleTimeRange:CMTimeRangeMake(kCMTimeZero, CMTimeMake(videoAsset.duration.value, videoAsset.duration.timescale)) toDuration:CMTimeMake(videoAsset.duration.value * scale, videoAsset.duration.timescale)];

        // 配置导出
        AVAssetExportSession* _assetExport = [[AVAssetExportSession alloc] initWithAsset:mixComposition presetName:AVAssetExportPreset1280x720];
        // 导出视频的临时保存路径
        NSString *exportPath = [kCachePath stringByAppendingPathComponent:[self movieName]];
        NSURL *exportUrl = [NSURL fileURLWithPath:exportPath];

        // 导出视频的格式 .MOV
        _assetExport.outputFileType = AVFileTypeQuickTimeMovie;
        _assetExport.outputURL = exportUrl;
        _assetExport.shouldOptimizeForNetworkUse = YES;

        // 导出视频
        [_assetExport exportAsynchronouslyWithCompletionHandler:
         ^(void ) {
             dispatch_async(dispatch_get_main_queue(), ^{
                 [_processedVideoPaths addObject:exportPath];
                 // 将导出的视频保存到相册
                 ALAssetsLibrary *library = [[ALAssetsLibrary alloc] init];
                 if (![library videoAtPathIsCompatibleWithSavedPhotosAlbum:[NSURL URLWithString:exportPath]]){
                     NSLog(@"cache can't write");
                     completed();
                     return;
                 }
                 [library writeVideoAtPathToSavedPhotosAlbum:[NSURL URLWithString:exportPath] completionBlock:^(NSURL *assetURL, NSError *error) {
                     if (error) {
                         completed();
                         NSLog(@"cache write error");
                     } else {
                         completed();
                         NSLog(@"cache write success");
                     }
                 }];
             });
         }];
    });
}
```

18.将多个视频合并为一个视频

```
- (void)mergeVideosWithPaths:(NSArray *)paths completed:(void(^)(NSString *videoPath))completed {
    if (!paths.count) return;

    dispatch_async(dispatch_get_main_queue(), ^{
        AVMutableComposition* mixComposition = [[AVMutableComposition alloc] init];
        AVMutableCompositionTrack *audioTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeAudio preferredTrackID:kCMPersistentTrackID_Invalid];
        AVMutableCompositionTrack *videoTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeVideo preferredTrackID:kCMPersistentTrackID_Invalid];
        videoTrack.preferredTransform = CGAffineTransformRotate(CGAffineTransformIdentity, M_PI_2);

        CMTime totalDuration = kCMTimeZero;

//        NSMutableArray<AVMutableVideoCompositionLayerInstruction *> *instructions = [NSMutableArray array];

        for (int i = 0; i < paths.count; i++) {
            AVURLAsset *asset = [AVURLAsset assetWithURL:[NSURL fileURLWithPath:paths[i]]];


            AVAssetTrack *assetAudioTrack = [[asset tracksWithMediaType:AVMediaTypeAudio] firstObject];
            AVAssetTrack *assetVideoTrack = [[asset tracksWithMediaType:AVMediaTypeVideo]firstObject];

            NSLog(@"%lld", asset.duration.value/asset.duration.timescale);

            NSError *erroraudio = nil;
            BOOL ba = [audioTrack insertTimeRange:CMTimeRangeMake(kCMTimeZero, asset.duration) ofTrack:assetAudioTrack atTime:totalDuration error:&erroraudio];
            NSLog(@"erroraudio:%@--%d", erroraudio, ba);

            NSError *errorVideo = nil;

            BOOL bl = [videoTrack insertTimeRange:CMTimeRangeMake(kCMTimeZero, asset.duration) ofTrack:assetVideoTrack atTime:totalDuration error:&errorVideo];
            NSLog(@"errorVideo:%@--%d",errorVideo,bl);

//            AVMutableVideoCompositionLayerInstruction *instruction = [AVMutableVideoCompositionLayerInstruction videoCompositionLayerInstructionWithAssetTrack:videoTrack];
//            UIImageOrientation assetOrientation = UIImageOrientationUp;
//            BOOL isAssetPortrait = NO;
//            // 根据视频的实际拍摄方向来调整视频的方向
//            CGAffineTransform videoTransform = assetVideoTrack.preferredTransform;
//            if (videoTransform.a == 0 && videoTransform.b == 1.0 && videoTransform.c == -1.0 && videoTransform.d == 0) {
//                NSLog(@"垂直拍摄");
//                assetOrientation = UIImageOrientationRight;
//                isAssetPortrait = YES;
//            }else if (videoTransform.a == 0 && videoTransform.b == -1.0 && videoTransform.c == 1.0 && videoTransform.d == 0) {
//                NSLog(@"倒立拍摄");
//                assetOrientation = UIImageOrientationLeft;
//                isAssetPortrait = YES;
//            }else if (videoTransform.a == 1.0 && videoTransform.b == 0 && videoTransform.c == 0 && videoTransform.d == 1.0) {
//                NSLog(@"Home键右侧水平拍摄");
//                assetOrientation = UIImageOrientationUp;
//            }else if (videoTransform.a == -1.0 && videoTransform.b == 0 && videoTransform.c == 0 && videoTransform.d == -1.0) {
//                NSLog(@"Home键左侧水平拍摄");
//                assetOrientation = UIImageOrientationDown;
//            }
//            CGFloat assetScaleToFitRatio = 720.0 / assetVideoTrack.naturalSize.width;
//            if (isAssetPortrait) {
//                assetScaleToFitRatio = 720.0 / assetVideoTrack.naturalSize.height;
//                CGAffineTransform assetSacleFactor = CGAffineTransformMakeScale(assetScaleToFitRatio, assetScaleToFitRatio);
//                [instruction setTransform:CGAffineTransformConcat(assetVideoTrack.preferredTransform, assetSacleFactor) atTime:totalDuration];
//            } else {
//                /**
//                 竖直方向视频尺寸：720*1280
//                 水平方向视频尺寸：720*405
//                 水平方向视频须要剧中的y值：（1280 － 405）／ 2 ＝ 437.5
//                 **/
//                CGAffineTransform assetSacleFactor = CGAffineTransformMakeScale(assetScaleToFitRatio, assetScaleToFitRatio);
//                [instruction setTransform:CGAffineTransformConcat(CGAffineTransformConcat(assetVideoTrack.preferredTransform, assetSacleFactor), CGAffineTransformMakeTranslation(0, 437.5)) atTime:totalDuration];
//            }
//            // 把新的插入到最上面，最后是按照数组顺序播放的。
//            [instructions insertObject:instruction atIndex:0];
//            totalDuration = CMTimeAdd(totalDuration, asset.duration);
//            // 在当前视频时间点结束后须要清空尺寸，不然若是第二个视频尺寸比第一个小，它会显示在第二个视频的下方。
//            [instruction setCropRectangle:CGRectZero atTime:totalDuration];
        }

//        AVMutableVideoCompositionInstruction *mixInstruction = [AVMutableVideoCompositionInstruction videoCompositionInstruction];
//        mixInstruction.timeRange = CMTimeRangeMake(kCMTimeZero, totalDuration);
//        mixInstruction.layerInstructions = instructions;

//        AVMutableVideoComposition *mixVideoComposition = [AVMutableVideoComposition videoComposition];
//        mixVideoComposition.instructions = [NSArray arrayWithObject:mixInstruction];
//        mixVideoComposition.frameDuration = CMTimeMake(1, 25);
//        mixVideoComposition.renderSize = CGSizeMake(720.0, 1280.0);
//
        NSString *outPath = [kVideoPath stringByAppendingPathComponent:[self movieName]];
        NSURL *mergeFileURL = [NSURL fileURLWithPath:outPath];

        AVAssetExportSession *exporter = [[AVAssetExportSession alloc] initWithAsset:mixComposition presetName:AVAssetExportPresetHighestQuality];
        exporter.outputURL = mergeFileURL;
        exporter.outputFileType = AVFileTypeQuickTimeMovie;
//        exporter.videoComposition = mixVideoComposition;
        exporter.shouldOptimizeForNetworkUse = YES;
        [exporter exportAsynchronouslyWithCompletionHandler:^{
             dispatch_async(dispatch_get_main_queue(), ^{
                 completed(outPath);
             });
         }];
    });
}
```