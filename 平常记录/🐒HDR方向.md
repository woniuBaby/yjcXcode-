* 方向:

```
1.自研播放器 

2.系统播放器,了解系统使用的过程



最终 目的:播放器实现hdr
```

* 目前

```
1.HDR 基础点 …

2.播放器 基础  解码 /显示 过程

3.了解渲染
```

* 查询方向

```
ijkplayer

HDR 必须走videobox?

cuva  hdr yuv sei

metal 或者 opengl + vediotoolbox
```



* metal  

```
1.metal 构建三角形 渲染
2.图片的渲染  外部给一张图片   三角形—>四边形
3.数据渲染  yuv数据—渲染出来
4.hdr  yuv渲染
```



```
self.player     eligibleForHDRPlayback
eligibleForHDRPlayback 不是OC 的属性
如果属性是 ture  
1.意味着可以播放 HDR视频
2.可以显示HDR视频
```





![image-20220705102408714](/Users/mac/Library/Application Support/typora-user-images/image-20220705102408714.png)

> pixelbuffer 对比

```objective-c


po newSampleBuffer
CMSampleBuffer 0x1021678c0 retainCount: 1 allocator: 0x2253f01b8
 invalid = NO
 dataReady = YES
 makeDataReadyCallback = 0x0
 makeDataReadyRefcon = 0x0
 formatDescription = <CMVideoFormatDescription 0x283090a50 [0x2253f01b8]> {
 mediaType:'vide' 
 mediaSubType:'420v' 
 mediaSpecific: {
  codecType: '420v'  dimensions: 1280 x 720 
 } 
 extensions: {{
    CVBytesPerRow = 1928;
    CVImageBufferChromaLocationBottomField = left;
    CVImageBufferChromaLocationTopField = left;
    CVImageBufferColorPrimaries = "ITU_R_709_2";
    CVImageBufferTransferFunction = "ITU_R_709_2";
    CVImageBufferYCbCrMatrix = "ITU_R_709_2";
    CVPixelAspectRatio =     {
        HorizontalSpacing = 0;
        VerticalSpacing = 0;
    };
    Version = 2;
}}
}
 sbufToTrackReadiness = 0x0
 numSamples = 1
 outputPTS = {INVALID}(computed from PTS, duration and attachments)
 sampleTimingArray[1] = {
  {PTS = {INVALID}, DTS = {INVALID}, duration = {INVALID}},
 }
 sampleAttachmentsArray[0] = {
  sample 0:
   DisplayImmediately = true
 }
 imageBuffer = 0x280f2a3a0


```

```objective-c
(lldb) po sampleBuffer
CMSampleBuffer 0x103804630 retainCount: 1 allocator: 0x2253f01b8
 invalid = NO
 dataReady = YES
 makeDataReadyCallback = 0x0
 makeDataReadyRefcon = 0x0
 formatDescription = <CMVideoFormatDescription 0x28193ca20 [0x2253f01b8]> {
 mediaType:'vide' 
 mediaSubType:'420f' 
 mediaSpecific: {
  codecType: '420f'  dimensions: 3840 x 1920 
 } 
 extensions: {{
    CVBytesPerRow = 5764;
    CVFieldCount = 1;
    CVImageBufferChromaLocationBottomField = Left;
    CVImageBufferChromaLocationTopField = Left;
    CVImageBufferColorPrimaries = "ITU_R_2020";
    CVImageBufferTransferFunction = "SMPTE_ST_2084_PQ";
    CVImageBufferYCbCrMatrix = "ITU_R_2020";
    CVPixelAspectRatio =     {
        HorizontalSpacing = 1;
        VerticalSpacing = 1;
    };
    ContentLightLevelInfo = {length = 4, bytes = 0x00000000};
    MasteringDisplayColorVolume = {length = 24, bytes = 0x21349baa199608fc8a4839083d1340420098968000000001};
    Version = 2;
}}
}
 sbufToTrackReadiness = 0x0
 numSamples = 1
 outputPTS = {181504/16000 = 11.344}(based on outputPresentationTimeStamp)
 sampleTimingArray[1] = {
  {PTS = {181504/16000 = 11.344}, DTS = {INVALID}, duration = {INVALID}},
 }
 imageBuffer = 0x282688000
```

