## Metal 3 新功能

### MetalFX Upscaling

借助高性能 Upscaling 和自动抗锯齿，缩短渲染每一帧复杂场景所需的时间。选择一种时间或空间算法的组合来帮助提升性能。

### 快速资源载入

通过利用异步 I/O，以优化方式将资源数据从存储直接传输到 Metal 纹理和缓冲区。

### 离线着色器编译

Metal 编译器可以在项目构建时生成 GPU 二进制文件，无需在 App 内编译着色器，帮助游戏提升性能并缩短载入时间。

### 网格着色器

这个新的几何管线将顶点着色器替换为两个新的着色器阶段 (对象和网格)，实现了更加灵活的剔除和 LOD 选择，以及更加高效的几何着色和生成。

### 适用于 PyTorch 的 Metal 后端

PyTorch 版本 1.12 中的全新 Metal 后端支持 MPS Graph 和 Metal Performance Shaders 基元，实现了高性能的 GPU 加速训练。

[开始使用](https://developer.apple.com/metal/pytorch/)

### 全新的光线追踪功能

Metal 光线追踪功能作了最新的改进，缩短了构建加速架构所需的 GPU 时间，剔除等工作也可转移到 GPU 进行以减少 CPU 开销，而且可以通过直接访问图元数据来优化相交和着色处理。



![image-20230112151935676](/Users/mac/Library/Application Support/typora-user-images/image-20230112151935676.png)

iOS 8.0 +



官网关于metal 了解 WWDC视频  https://developer.apple.com/cn/news/?id=mx6sy2ml

官网 : https://developer.apple.com/cn/metal/

---



### metal 3 支持

6 月 11 日消息，苹果在 WWDC22 上发布了 iOS 16、iPadOS 16、macOS 13 Ventura 系统，并且已经发布了首个开发者预览版 Beta

据 Apple Insider 报道，今年晚些时候推出的新 Metal 3 API 将使 Mac 能够更高效地升级、智能地绘制帧以实现更流畅的游戏玩法，并更快地访问游戏数据。

作为新 macOS Ventura 的一部分，苹果 GPU 软件高级总监 Jeremy Sandmel 在公司的 WWDC22 活动中讨论了新的 Metal。

新版 Metal 引入了 MetalFX 框架。MetalFX 管理升级以获得更好的分辨率，并绘制额外的帧以提高游戏中的帧速率。游戏《无人深空》的 Mac 版本将利用 MetalFX 技术来提高其性能

Metal 3 还将具有“快速资源加载 API”，它可以加快从 silicon 到 RAM 和存储的管道，使设备能够更快地查找和绘制纹理。这将允许使用 API 的游戏体验更少的加载时间。

随着新 Metal 中包含的技术，《生化危机 8：村庄》被宣布用于 Mac。高级技术研究部经理 Masaru Ijuin 上台宣布将游戏移植到 macOS。《生化危机 8：村庄》将于 2022 年晚些时候登陆 Mac。

新 Metal 中专门用于 iPad 的新 API 是后台下载 API，它优化了前台的性能，同时设备因并发软件下载而受累。

苹果公布了 Metal 3 支持设备列表，iPhone 和 iPad 需要 A13 以上设备，需要 2017 款或更新的 Mac 设备。



![img](https://pics0.baidu.com/feed/622762d0f703918f6bb17ce427015f9d59eec407.jpeg@f_auto?token=7240606463f332246d4e013448ee22ad)



Metal 3 将于今年秋季随 macOS Ventura 和 iPadOS 16 一起发布。



Metal 于 2014 年首次推出，作为 iPhone 5s 的 Apple A7 处理器的优化层。随着功能更强大的 Apple Silicon 芯片的发布，该技术的范围扩大到更多设备。