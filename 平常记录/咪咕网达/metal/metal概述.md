##  metal 概述



Apple 官网  https://developer.apple.com/cn/metal/

​	在 [WWDC](https://baike.baidu.com/item/WWDC?fromModule=lemma_inlink) 2014 上，Apple为游戏开发者推出了新的平台技术 Metal，该技术能够为 3D 图像提高 10 倍的[渲染](https://baike.baidu.com/item/渲染/464729?fromModule=lemma_inlink)性能，并支持大家熟悉的游戏引擎及公司。

- (渲染在电脑绘图中是指用软件从[模型](https://baike.baidu.com/item/模型/1741186?fromModule=lemma_inlink)生成[图像](https://baike.baidu.com/item/图像/773234?fromModule=lemma_inlink)的过程。模型是用严格定义的[语言](https://baike.baidu.com/item/语言/2291095?fromModule=lemma_inlink)或者[数据结构](https://baike.baidu.com/item/数据结构/1450?fromModule=lemma_inlink)对于三维物体的描述，它包括[几何](https://baike.baidu.com/item/几何/303227?fromModule=lemma_inlink)、[视点](https://baike.baidu.com/item/视点/5956687?fromModule=lemma_inlink)、[纹理](https://baike.baidu.com/item/纹理/5401676?fromModule=lemma_inlink)以及[照明](https://baike.baidu.com/item/照明/899297?fromModule=lemma_inlink)信息。)

Metal 是一种低层次的渲染应用程序编程接口，提供了软件所需的最低层，保证软件可以运行在不同的图形芯片上。Metal 提升了 A7 与 A8 处理器效能，让其性能完全发挥。

​	Metal 是一项全新的技术，专为开发高临场感主机游戏的开发者打造，可让开发者全力发挥 A7 和 A8 芯片的性能。该技术经过优化，使处理器和[图形处理器](https://baike.baidu.com/item/图形处理器/8694767?fromModule=lemma_inlink)能够协同工作来实现最优性能。它专为多线程而设计，并提供各种出色工具将所有素材整合在[Xcode](https://baike.baidu.com/item/Xcode?fromModule=lemma_inlink)中。 

---



**Metal是由苹果公司所开发的一个应用程序接口(API)，兼顾图形与计算功能，面向底层、低开销的硬件加速**。其类似于将OpenGL与OpenCL的功能集成到了同一个API上，最初支持它的系统是iOS8。Metal使得iOS可以实现其他平台的类似功能，例如Khronos Group的跨平台Vulkan与Microsoft Windows上的Direct 3D 12。

Metal也通过引入计算着色器来进一步提高GPGPU编程的能力。

Metal使用一种**基于C++ 11的新着色语言，其实现借助了Clang和LLVM**。

### 历史

- 文字解

**2014年6月2日，Metal开始支持iOS设备**（仅支持AppleA7或更新款处理器的iPhone、iPad）；**2015年6月8日，Metal开始支持运行OS X El Capitan的Mac设备**（仅2012年中或更新款机种）。

**2017年6月5日，Apple于WWDC宣布了Metal的第二个版本，支持macOS High Sierra、iOS 11和tvOS 11**。Metal 2不是Metal的独立API，并且由需要的硬件支持。**Metal 2在Xcode中实现了更高效的分析和调试，加速了机器学习、降低了CPU工作负载、支持macOS上的虚拟现实以及Apple A11处理器的特性**。

**2019年6月3日，Metal API更新到第三个版本，支持macOS Catalina、iOS 13和iPadOS 13**。

**2020年的苹果全球开发者大会(WWDC)上，苹果宣布将Mac迁移到Apple Silicon**。使用Apple Silicon的Mac将使用Apple GPU，支持之前在macOS和iOS上实现的特色功能，并将能够利用为Apple GPU架构所定制的基于图块的延迟渲染（TBDR）功能



- 图解诞生历史:如下图所示：

![img](https://pic3.zhimg.com/80/v2-5420fe6ba3dd74547d75db4407d7fdaa_720w.webp)

如上图，在之前利用GPU来加速渲染，只有 DirectX 和 OpenGL两种选择，其中DirectX是微软的Windows专用的。在2013年AMD宣布开始做一个 Mantle项目更好的利用显卡的先进特性，它宣称可以把draw call速度提高9倍之多，不过这个项目后来被AMD取消了。苹果这边借鉴了Mantle项目，在2014年6月的WWDC上宣布Metal的诞生，它只要运行在A7的CPU及以上机型，可以用Metal SHading Language(MSL)来编写shader文件，并且提供了MetalKit以及Metal Performance Shader(MPS)等框架。在2017年的WWDC，苹果宣布了Metal 2的诞生，它不但更好利用了先进GPU特性，并且支持了VR，AR以及机器学习（ML）等。







###  相关技术

---

**SpriteKit**

SpriteKit 让开发者可以开发高性能、省电节能的 2D 游戏。在 iOS 8 中，我们新添了多项增强功能，这将使 2D 游戏体验更加精彩。这些新技术有助于使游戏角色的动作更加自然，并让开发者可以更轻松地在游戏中加入力场、检测碰撞和生成新的灯光效果。

**SceneKit**

SceneKit 专为休闲 3D 游戏而设计，可让开发者渲染 3D 游戏场景。SceneKit 内置了物理引擎、粒子发生器和各种易用工具，可以轻松快捷地为 3D 物体编写动作。不仅如此，它还与 SpriteKit 完全集成，所以开发者可以直接在 3D 游戏中加入 SpriteKit 的素材



---





## Metal之实现视频采集与实时渲染

(视频采集 以及 metal即刻渲染) https://betheme.net/news/txtlist_i192067v.html?action=onClick

- 通过AVFoundation进行视频数据的采集，并将采集到的原始数据存储到CMSampleBufferRef中，即视频帧数据（视频帧其实本质也是一张图片）。
- 通过CoreVideo将CMSampleBufferRef中存储的图像数据，转换为Metal可以直接使用的纹理。
- 将Metal纹理进行渲染，并即刻显示到屏幕上。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20210313150732854.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZvcmV2ZXJfd2o=,size_16,color_FFFFFF,t_70#pic_center)


