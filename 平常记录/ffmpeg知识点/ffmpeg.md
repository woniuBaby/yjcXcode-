

* 视频格式将code ID 转换成 hvc1

```

ffmpeg -i hdr10_3840x1920_30fps.mp4 -vcodec copy -acodec copy -tag:v hvc1 hdr10_3840x1920_30fps_hvc1.mp4



ffmpeg -i out.mov -vcodec copy -acodec copy -tag:v hvc1 out_hvc1.mov



ffmpeg -i European_Cup_4k_hlg_50fps.ts -vcodec copy -acodec copy -tag:v hvc1 European_Cup_4k_hlg_50fps_hvc1.ts
```





- **视频格式转换（视频容器转换**)

```
比如一个avi文件，想转为mp4，或者一个mp4想转为ts。

ffmpeg -i input.avi output.mp4 

或者

ffmpeg -i input.mp4 output.ts 

ffmpeg -i 北海道夏天的尾巴4KHDR.webm output.mp4 
```





- 、** 视频剪切**

```
经常要测试视频，但是只需要测几秒钟，可是视频却有几个G，咋办？切啊！
 下面的命令，就可以从时间为00:00:15开始，截取5秒钟的视频。

ffmpeg -ss 00:00:15 -t 00:00:05 -i input.mp4 -vcodec copy -acodec copy output.mp4 
```



