# 			互动SDK （Android）使用zego的API信息



### 一 版本信息

  -  版本号liveroom sdk v6.3.0

### 二 Android 运行环境 

- 支持版本: 4.1+

- 支持框架: arm64-v8a / armeabi-v7a /x86 / 模拟器 x86

### 三 API列表

- 3.1 zego的完整api使用说明参考

  

- 3.2 互动sdk中所引用的具体函数列表，如下

```objective-c

初始化	
initWithAppID:appSignature:completionBlock:	初始化 SDK
setUserID:userName:	设置用户 ID 及用户名
setConfig:	设置配置信息，如果没有特殊说明，必须确保在 InitSDK 前调用
  
环境配置
version	ZegoLiveRoom SDK 版本号 
setVerbose:	是否打开调试信息
setUseTestEnv:	是否启用测试环境
  
setRoomDelegate:	设置 room 代理对象
setRoomConfig:userStateUpdate:	设置房间配置信息
loginRoom:roomName:role:withCompletionBlock:	登录房间
switchRoom:roomName:role:withCompletionBlock:	切换房间
logoutRoom	退出房间
  
  
推流	
setLiveEventDelegate:	设置直播事件代理对象
setPublisherDelegate:	设置 Publisher 代理对象
startPublishing:title:flag:extraInfo:	开始发布直播
stopPublishing	停止直播
startPublishing2:title:flag:extraInfo:channelIndex:	开始发布直播，可推两路流
stopPublishing:	停止直播，与可推两路流的开始直播接口成对使用
  
拉流	
setPlayerDelegate:	设置 Player 代理对象
startPlayingStream:inView:	播放直播流
updatePlayView:ofStream:	更新播放视图
stopPlayingStream:	停止播放流

  
推流基础配置	
requireHardwareEncoder	硬件编码开关
setAVConfig:	设置视频配置
  
推流高级配置
addPublishTarget:streamID:completion:	添加转推地址
deletePublishTarget:streamID:completion:	删除转推地址
updateStreamExtraInfo:	更新流附加信息
muteAudioPublish:	推流时是否发送音频数据
muteVideoPublish:	推流时是否发送视频数据
  
拉流基础配置	
requireHardwareDecoder:	开关硬件解码
enableSpeaker:	（声音输出）静音开关
  
拉流高级配置
activateAudioPlayStream:active:	拉流是否接收音频数据
activateVideoPlayStream:active:	拉流是否接收视频数据
  
  
视频采集设置
enableCamera:	开启视频采集
setFrontCam:	切换前后摄像头
enableTorch:	开关手电筒
setAppOrientation:	设置手机方向
enableCamera:channelIndex:	开启视频采集，适用于一端同时推两路流
setFrontCam:channelIndex:	切换前后摄像头，适用于一端同时推两路流

视频预览设置	
setPreviewView:	设置本地预览视图
startPreview	启动本地预览
stopPreview	结束本地预览
setPreviewViewMode:	设置本地预览视频视图的模式
setPreviewRotation:	设置预览渲染朝向
setVideoMirrorMode:	是否启用预览和推流镜像
setPreviewView:channelIndex:	设置本地预览视图，适用于一端同时推两路流
startPreview:	启动本地预览，适用于一端同时推两路流
stopPreview:	结束本地预览，适用于一端同时推两路流
setPreviewViewMode:channelIndex:	设置本地预览视频视图的模式，适用于一端同时推两路流
setVideoMirrorMode:channelIndex:	是否启用预览和推流镜像，适用于一端同时推两路流
  
  
音频采集设置	
setAudioDeviceMode:	设置音频设备模式
enableMic:	开启麦克风
setAECMode	设置回声消除模式
enableAEC:	回声消除开关
enableNoiseSuppress:	音频采集噪声抑制开关
setNoiseSuppressMode:	设置音频采集降噪等级
  
实时消息
setIMDelegate:	设置实时消息代理对象
setRoomDelegate:	设置 room 代理对象

自定义消息	
sendCustomCommand:content:completion:	发送自定义信令
发给一人时，支持大房间，每个房间QPS限制为200，消息大小限制为1k字节
发给多人时，不支持大房间，每个房间QPS限制为10，消息大小限制为1k字节
sendRoomMessage:type:category:completion:	房间发送广播消息
  
  
视频外部采集
setVideoCaptureFactory:channelIndex:	设置外部采集模块
视频外部采集工厂	
zego_create:	创建采集设备
zego_destroy:	销毁采集设备
视频外部采集设备	
zego_allocateAndStart:	初始化采集使用的资源（例如启动线程等）回调
zego_stopAndDeAllocate	停止并且释放采集占用的资源
zego_supportBufferType	支持的 buffer 类型
zego_startCapture	启动采集，采集的数据通过 [client -onIncomingCapturedData:withPresentationTimeStamp:] 通知 SDK
zego_stopCapture	停止采集
zego_startPreview	启动预览回调
zego_stopPreview	停止预览回调

视频外部采集设备 Client
destroy	销毁
onIncomingCapturedData:withPresentationTimeStamp:	接收视频帧数据
  
  
视频外部滤镜
setVideoFilterFactory:channelIndex:	设置外部滤镜模块
  
视频外部滤镜工厂	
zego_create	创建外部滤镜
zego_destroy:	销毁采集设备
滤镜设置	
zego_allocateAndStart:	初始化采集使用的资源（例如启动线程等）回调
zego_stopAndDeAllocate	停止并且释放采集占用的资源
destroy	销毁
  
  
  
媒体次要信息
setMediaSideDelegate:	设置回调，接收媒体次要信息
setMediaSideFlags:onlyAudioPublish:mediaInfoType:seiSendType:channelIndex:	发送媒体次要信息开关，支持 SEI
sendMediaSideInfo:packet:channelIndex:	发送媒体次要信息
  
  
音浪设置
setSoundLevelDelegate:	设置代理对象
  
水印设置	
setWaterMarkImagePath:	设置水印的图片路径
setPublishWaterMarkRect:	设置水印在采集视频中的位置
setWaterMarkImagePath:channelIndex:	设置水印的图片路径
setPublishWaterMarkRect:channelIndex:	设置水印在采集视频中的位置

基础功能事件回调
onStreamUpdated:streams:roomID:	流信息更新
  
  
推流事件回调
onPublishStateUpdate:streamID:streamInfo:	推流状态更新
onPublishQualityUpdate:quality:	发布质量更新
onCaptureVideoFirstFrame	采集视频的首帧通知
onCaptureAudioFirstFrame	采集音频的首帧通知
onSendLocalAudioFirstFrame	推流音频首帧通知

拉流事件回调	
onPlayStateUpdate:streamID:	播放流事件
onPlayQualityUpate:quality:	观看质量更新
onVideoSizeChangedTo:ofStream:	视频宽高变化通知
  
视频采集事件回调	
onCaptureVideoSizeChangedTo:	采集视频的宽度和高度变化通知
onRemoteCameraStatusUpdate:ofStream:reason:	所拉流的摄像头状态通知
onRemoteMicStatusUpdate:ofStream:reason:	所拉流的麦克风状态通知
  
  
实时消息事件回调
onRecvRoomMessage:messageList:	收到房间的广播消息
onRecvConversationMessage:conversationId:message: 收到会话消息
  
用户状态更新事件回调	
onUserUpdate:updateType:	房间成员更新回调
onUpdateOnlineCount:room:	收到在线人数更新
  
```

