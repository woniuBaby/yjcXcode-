合并beta分支 操作记录

```
git Commit

git rebase

解决冲突

git push --force

gitlab。  merge request
```



http://gitlab.cmvideo.cn/play_capacity/mglive/mglive-sdk-ios/-/merge_requests/6







````objc
//删除SDK中的i386，x86_86架构 1.使用终端进入到SDK内部 cd /Users/leo/Desktop/testDir/OGLFramework.framework 2

.查看当前支持的架构 lipo -info OGLFramework  

可以看到OGLFramework当前支持的架构： Architectures in the fat file: OGLFramework are: i386 x86_64 armv7 arm64 3.删掉i386，x86_86
lipo -remove i386 OGLFramework -o OGLFramework
lipo -remove x86_64 OGLFramework -o OGLFramework  (需要手动输入 复制未起作用)
````





[MGLLog addLogModule:@"MGLiveRoomClient" fuction:MGLFuncName info:@"" result:@""];



[MGLLog addLogModule:@"MGLiveRoomClient" fuction:MGLFuncName info:[NSString stringWithFormat:@"roomID:%@ userID:%@ userName:%@",roomID,userID,userName] result:[NSString stringWithFormat:@"%d",errorCode]];

###  MGLiveGlobalConfiguration

```
bool isSuccess = [ZegoLiveRoomApi requireHardwareEncoder:bRequire];

[MGLLogManager addLogInfo:@"MGLiveGlobalConfiguration" fuction:MGLFuncName info:[NSString stringWithFormat:@"硬编:%d(1打开 0关闭)",bRequire] result:isSuccess];

return isSuccess;
```





日志模块:

```
MGLive  
MGLiveRoomClient
MGLocalStream
MGPlayerGateWay
MGLiveGlobalConfiguration
CaptureDevice
setAVConfig
MGError
MGLSQM   event description

DeviceSupport 设备支持
MGLiveOnlineConfig


```



![image-20230317193642689](/Users/mac/Library/Application Support/typora-user-images/image-20230317193642689.png)



![image-20230320192727688](/Users/mac/Library/Application Support/typora-user-images/image-20230320192727688.png)

http://confluence.cmvideo.cn/confluence/pages/viewpage.action?pageId=160142297
错误码定义confluence地址，其中错误码按错误来源、场景类型等多个维度已梳理部分错误码，大家在开发过程中需新增错误码，可在相应的错误码类型下新增分配，知会我这边同步更新错误码文档

```objc
//添加错误方法
[MGLCheckDataTool getMGErrorCode:MG_LIVE_ERROR_BITTEMPLATE_BITRATE detailCode:0 level:1 phase:0 live:[MGLCommon sharedInstance].live];
```





0:rtmp 1: 主播 2：赛事



在线配置流控策略中设置最小值的zego ios接口

2023年03月25日  任务
接口改动：

1. 镜像参数修改为0-关闭，1-打开，但如果预览过程中设置了镜像在推流后也要保持
   参数修改为bool类型，true：开，false：关 ---------- ✅
2. 新增获取信息接口
3. 删除老接口，只留新接口  ✅
4.  creatLocalStream增加action参数(0：预览，1:直播)，预览场景不参考码率模版，默认720的清晰度，fps为15，直播场景根据码率模版设置   ✅
5. 埋点事件ID以及字段优化  ------------  ✅
6. 初始化 修改接口 名字 MGLiveScene scene 0尬聊 1二台    ------- ✅



码率：
   两路都设置（setAudioBitrate）
声道：
   主播：采集、编码；（采集Android:enableCaptureStereo  ios:enableAudioCaptureStereo）,编码：setAudioChannelCountByChannel
   赛事：编码

aac-lc：SDK调用setLatencyMode接口设置为Normal2

aac-he：SDK调用setLatencyMode接口设置为Normal



----



2023年03月28日

1.在线配置需要增加码率自适应控制开关
zego_rtmp_enable_rate_control 默认false



**[0328 20:21:17.972][184652][I][mt:3367303][lrcbc:411][cb][roomState]:OnTempBroken error:65500008, room:703-8BA7532C372E44C2AFA39B414D75F320**

**[0328 20:21:19.972][186651][I][mt:3367303][lrcbc:411][cb][roomState]:OnTempBroken error:50001009, room:703-8BA7532C372E44C2AFA39B414D75F320**

**[0328 20:21:20.118][186798][I][mt:3367303][lrcbc:380][cb][roomState]:OnReconnect error:0, room:703-8BA7532C372E44C2AFA39B414D75F320**

### <font color=red>遗留问题</font>

//待加 --- ⏰⏰⏰⏰⏰⏰⏰

common 统一移除

初始化状态 

---



2023年04月12日15:52:51

SDK新增功能描述：
1. onError回调中增加开播状态字段（key为“PugcAction”，0为预览状态，1为开播中，app调用createLocalStream（）接口会传递更新该值），用于app确认该错误码是什么状态产生的错误码；
2. 确认createLocalStream、createPlayerGateWay、createLiveRoomClient返回对象为NULL，有onError错误码抛出；
3. 网络质量检测方案描述及伪码，请参考实现http://confluence.cmvideo.cn/confluence/pages/viewpage.action?pageId=165477739
4. 错误码更新（error code字段变为4位），关注错误码文档中标红部分修改（重点是字段新增和level变化部分）  @阎晋超 @伍发远
5. 埋点字段type 删除

```
   if ( (MaintotalBytes - lastMaintotalBytes)  <= stuckThreshold ) {
              mainStuckCount ++;
      }
      else {
            // 未出现卡顿，重置计数器及统计对比初始值
            mainStuckCount = 0;
            lastMaintotalBytes = MaintotalBytes;
      }

      if (  (AuxtotalBytes - lastAuxtotalBytes) <= stuckThreshold ) {
            AuxStuckCount ++;
      }
      else {
           // 未出现卡顿，重置计数器及统计对比初始值
           AuxStuckCount = 0;
           lastAuxtotalBytes  = AuxtotalBytes;
       }
        // 任意一路流出现卡顿，则抛错误码
        if((AuxStuckCount >= stuckCheckCount) || (mainStuckCount >= stuckCheckCount) ) {
              onError（“推流卡顿，切换网络重试”）；
              AuxStuckCount = 0；
              mainStuckCount = 0；
              lastMaintotalBytes = MaintotalBytes;
              lastAuxtotalBytes  = AuxtotalBytes;
        }

        重置定时器；
        return；
```



---

Rtmp 推流地址:

陈文鹏16:42
rtmp://devlivepull.migucloud.com/live/1ULFPHS2_C0
阎晋超16:44
rtmp://ilvbpush.migucloud.com/live/show39
