本案例的目的在于理解本地视频文件渲染的过程

案例的实现思路：

- 1、使用自定义的CJLAssetReader工具类，读取mov/mp4视频文件
- 2、Metal渲染回调 还原成`CMSampleBufferRef`图像数据，然后将读取到`CVPixelBufferRef`视频像素缓存区
- 3、通过CoreVideo获取Y纹理，UV纹理
- 4、在自定义着色器将颜色编码格式由YUV转换为RGB，显示到屏幕上

整体的流程图如下



![img](https:////upload-images.jianshu.io/upload_images/2251862-49f004bbabe72f15.png?imageMogr2/auto-orient/strip|imageView2/2/w/802)

整体流程

主要分为三部分

- viewDidLoad函数：绘制前的准备工作
- MTKViewDelegate协议：视频文件渲染
- Metal着色文件：处理顶点数据，以及将YUV转换为RGB格式显示

## 准备工作

在此之前还需要作一些准备

- 创建OC与C共用的.h文件
- AVassetreader工具类

**共用文件**
主要包含以下内容

- 顶点数据结构体



```cpp
//顶点数据结构
typedef struct {
    //顶点坐标(x,y,z,w)
    vector_float4 position;
    //纹理坐标(s,t)
    vector_float2 textureCoordinate;
}CJLVertex;
```

- 转换矩阵结构体，用于YUV转换为RGB



```cpp
//转换矩阵：YUV-->RGB
typedef struct {
    //三维矩阵:转换矩阵
    matrix_float3x3 matrix;
    //偏移量
    vector_float3 offset;
}CJLConvertMatrix;
```

- 顶点函数输入索引，用于viewController数据传入metal的入口



```cpp
typedef enum CJLVertexInputIndex
{
    CJLVertexInputIndexVertices = 0,
}CJLVertexInputIndex;
```

- 片元函数缓存区索引



```cpp
typedef enum CJLFragmentBufferIndex
{
    CJLFragmentInputIndexMatrix = 0,
}CJLFragmentBufferIndex;
```

- 片元函数纹理索引



```cpp
typedef enum CJLFragmentTextureIndex
{
    //Y纹理
    CJLFragmentTextureIndexTextureY = 0,
    //UV纹理
    CJLFragmentTextureIndexTextureUV = 1,
}CJLFragmentTextureIndex;
```

**工具类**
`AVAssetReader`是`AVFoundation`中的一个`读取器对象`，主要有以下两种功能：

- 1、直接从存储中读取原始未解码的媒体样本，获取解码为可渲染形式的样本：从mp4文件中拿到h264，并对其进行解码拿到可渲染的样本
- 2、混合资产的多个音轨，并使用和组合多个视频音轨

AVAssetReader类结构如下图所示



![img](https:////upload-images.jianshu.io/upload_images/2251862-a76c5c6aeface086.png?imageMogr2/auto-orient/strip|imageView2/2/w/815)

类结构

其中，AVAssetReaderOutPut包含三种类型的输出

- AVAssetReaderTrackOutput：用于从`AVAssetReader`存储中读取单个轨道的媒体样本
- AVAssetReaderAudioMixOutput：用于读取音频样本
- AVAssetReaderVideoCompositionOutput：用于读取一个或多个轨道中的帧合成的视频帧

下图是利用`AVAssetReader`读取视频文件并传入GPU渲染的图示

![img](https:////upload-images.jianshu.io/upload_images/2251862-98adf5bfeca80446.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200)

视频文件渲染数据流程图示



- 从`AVAssetReader`存储中M获取mov/mp4视频文件，将视频文件解压缩，即解码，还原成`CMSampleBufferRef`图像数据
- 从`CMSampleBufferRef`中将图像数据读取到`CVPixelBufferRef`视频像素缓存区
- 利用`CVPixelBufferRef`像素缓存区数据 和 `CVMetalTextureCacheRef`纹理缓存区数据 创建metal纹理缓存区`CVMetalTextureRef`
- 将metal纹理缓存区`CVMetalTextureRef`的数据转换成metal纹理`id<MTLTexture>`
- 将mental纹理`id<MTLTexture>`传递到`GPU`中的片元着色函数

工具类`CJLAssetReader`中主要提供了两个函数

- `initWithUrl`函数：初始化
- `readBuffer`函数：从mov/mp4文件读取CMSampleBufferRef数据
  函数的具体实现这里不做详细讲解，有兴趣的可以看看文末完整代码，也可以直接在项目中使用。

## viewDidLoad函数

该函数主要是绘制前的准备工作，主要有五部分，如下图所示



![img](https:////upload-images.jianshu.io/upload_images/2251862-1c9872ef1281034b.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200)

iewDidLoad函数流程

- setupMTKView函数：MTKView设置
- setupCJLAsset函数：工具类设置
- setupPipeline函数：渲染管道设置
- setupVertex函数：顶点数据设置
- setupMatrix函数：转换矩阵设置

**setupMTKView函数**

主要是设置MTKView及视口大小



```objectivec
//1.MTKView 设置
- (void)setupMTKView
{
    //1.初始化mtkView
    self.mtkView = [[MTKView alloc] initWithFrame:self.view.bounds device:MTLCreateSystemDefaultDevice()];
    
    //设置self.view = self.mtkView;
    self.view = self.mtkView;
    
    //设置代理
    self.mtkView.delegate = self;
    
    //2、获取视口size
    self.viewPortSize = (vector_uint2){self.mtkView.drawableSize.width, self.mtkView.drawableSize.height};
    
}
```

**setupCJLAsset函数**

工具类的初始化主要分为3步

- 获取视频文件路径
- 通过视频路径初始化自定义的工具类
- 创建纹理缓存区对象



```objectivec
//2.CCAssetReader设置
- (void)setupCJLAsset
{
    //注意CCAssetReader 支持MOV/MP4文件都可以
    //1.视频文件路径
    NSURL *url = [[NSBundle mainBundle] URLForResource:@"sinan" withExtension:@"mp4"];
    
    //2.初始化CCAssetReader
    self.reader = [[CJLAssetReader alloc] initWithUrl:url];
    
    //3._textureCache的创建(通过CoreVideo提供给CPU/GPU高速缓存通道读取纹理数据)
    CVMetalTextureCacheCreate(NULL, NULL, self.mtkView.device, NULL, &_textureCache);
}
```

**setupPipeline函数**
渲染管道的设置在以前的案例中已经提及了多次，这里仅简述下步骤

- 获取metal文件 & 加载function
- 创建渲染描述符 & 设置function
- 初始化渲染管道状态
- 创建commandQueue渲染命令队列
  具体的代码青草考文末案例的完整代码

**setupVertex函数**
顶点数据初始化以及顶点缓存区的创建

- 初始化顶点数据
- 创建MTLBuffer顶点缓存区
- 计算顶点个数



```objectivec
//4.顶点数据设置
- (void)setupVertex
{
    //1.顶点坐标(x,y,z,w);纹理坐标(x,y)
       //注意: 为了让视频全屏铺满,所以顶点大小均设置[-1,1]
    static const CJLVertex quadVertices[] =
    {   // 顶点坐标，分别是x、y、z、w；    纹理坐标，x、y；
        { {  1.0, -1.0, 0.0, 1.0 },  { 1.f, 1.f } },
        { { -1.0, -1.0, 0.0, 1.0 },  { 0.f, 1.f } },
        { { -1.0,  1.0, 0.0, 1.0 },  { 0.f, 0.f } },
        
        { {  1.0, -1.0, 0.0, 1.0 },  { 1.f, 1.f } },
        { { -1.0,  1.0, 0.0, 1.0 },  { 0.f, 0.f } },
        { {  1.0,  1.0, 0.0, 1.0 },  { 1.f, 0.f } },
    };
    
    //2.创建顶点缓存区
    self.vertices = [self.mtkView.device newBufferWithBytes:quadVertices length:sizeof(quadVertices) options:MTLResourceStorageModeShared];
    
    //3.计算顶点个数
    self.numVertices = sizeof(quadVertices) / sizeof(CJLVertex);
}
```

**setupMatrix函数**
主要是设置`YUV->RGB转换的矩阵`，转换矩阵主要有3种，其中`BT.709`最好

- BT.601：SDTV的标准



```undefined
matrix_float3x3 kColorConversion601DefaultMatrix = (matrix_float3x3){
     (simd_float3){1.164,  1.164, 1.164},
     (simd_float3){0.0, -0.392, 2.017},
     (simd_float3){1.596, -0.813,   0.0},
 };
```

- BT.601 全系列



```undefined
matrix_float3x3 kColorConversion601FullRangeMatrix = (matrix_float3x3){
     (simd_float3){1.0,    1.0,    1.0},
     (simd_float3){0.0,    -0.343, 1.765},
     (simd_float3){1.4,    -0.711, 0.0},
 };
```

- BT.709：HDTV的标准



```undefined
 matrix_float3x3 kColorConversion709DefaultMatrix[] = {
     (simd_float3){1.164,  1.164, 1.164},
     (simd_float3){0.0, -0.213, 2.112},
     (simd_float3){1.793, -0.533,   0.0},
 };
```

颜色编码格式的转换选择其中一种即可

转换矩阵的设置，主要有以下几步

- 初始化转化矩阵
- 初始化偏移量



```undefined
vector_float3 kColorConversion601FullRangeOffset = (vector_float3){ -(16.0/255.0), -0.5, -0.5};
```

- 创建转换矩阵结构体：通过矩阵、偏移量创建



```cpp
//3.创建转化矩阵结构体.
    CJLConvertMatrix matrix;
    //设置转化矩阵
    /*
     kColorConversion601DefaultMatrix；
     kColorConversion601FullRangeMatrix；
     kColorConversion709DefaultMatrix；
     */
    matrix.matrix = kColorConversion601FullRangeMatrix;
    //设置offset偏移量
    matrix.offset = kColorConversion601FullRangeOffset;
```

- 创建转换矩阵缓存区，用于颜色编码格式的转换



```objectivec
self.convertMatrix = [self.mtkView.device newBufferWithBytes:&matrix length:sizeof(CJLConvertMatrix) options:MTLResourceStorageModeShared];
```

## MTKViewDelegate协议

主要是回调视图渲染代理方法`drawInMTKView`，将视频文件数据渲染到屏幕上，其流程如下

![img](https:////upload-images.jianshu.io/upload_images/2251862-926d5738caed5ed8.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200)

drawInMTKView代理方法流程



这里大部分的步骤都是Metal中渲染的基础步骤，这些步骤将不再讲解，主要讲解以下3步

- 2、从工具类中读取图像数据
- 9.设置纹理
- 10.设置片元函数转化矩阵

**2、从工具类中读取图像数据**
通过工具类的`readBuffer`函数从存储中读取视频文件图像数据，还原为`CMSampleBufferRef`类型



```objectivec
CMSampleBufferRef sampleBuffer = [self.reader readBuffer];
```

**9.setupTextureWithEncoder函数：设置纹理**

主要是将sampleBuffer数据 设置到renderEncoder 中，即将读取的数据转换为metal纹理，主要有以下步骤，如图所示



![img](https:////upload-images.jianshu.io/upload_images/2251862-87de9ddbe104fbd4.png?imageMogr2/auto-orient/strip|imageView2/2/w/826)

setupTextureWithEncoder函数流程

- 将从CMSampleBuffer读取CVPixelBuffer：
  从`CMSampleBufferRef`中将图像数据读取到`CVPixelBufferRef`视频像素缓存区



```objectivec
CVPixelBufferRef pixelBuffer = CMSampleBufferGetImageBuffer(CMSampleBufferRef);
```

- 设置Y纹理、UV纹理

  ```
  纹理通用的设置步骤总结
  ```

  如下：

  - 获取纹理的宽高
  - 初始化像素格式
  - 创建CoreVideo框架Metal空纹理
  - 根据视频像素缓存区 创建 Metal 纹理缓存区
  - 判断纹理缓存区是否创建成功
  - 如果创建成功，则将纹理缓存区数据转换为metal纹理，并释放

以Y纹理为例，设置的代码如下



```objectivec
//textureY 设置
    {
        //2.获取纹理的宽高
        size_t width = CVPixelBufferGetWidth(pixelBuffer);
        size_t height = CVPixelBufferGetHeight(pixelBuffer);
        
        //3.像素格式:普通格式，包含一个8位规范化的无符号整数组件。
        MTLPixelFormat pixelFormat = MTLPixelFormatR8Unorm;
        
        //4.创建CoreVideo的Metal纹理
        CVMetalTextureRef texture = NULL;
        
        /*5. 根据视频像素缓存区 创建 Metal 纹理缓存区
        CVReturn CVMetalTextureCacheCreateTextureFromImage(CFAllocatorRef allocator,
        CVMetalTextureCacheRef textureCache,
        CVImageBufferRef sourceImage,
        CFDictionaryRef textureAttributes,
        MTLPixelFormat pixelFormat,
        size_t width,
        size_t height,
        size_t planeIndex,
        CVMetalTextureRef  *textureOut);
        
        功能: 从现有图像缓冲区创建核心视频Metal纹理缓冲区。
        参数1: allocator 内存分配器,默认kCFAllocatorDefault
        参数2: textureCache 纹理缓存区对象
        参数3: sourceImage 视频图像缓冲区
        参数4: textureAttributes 纹理参数字典.默认为NULL
        参数5: pixelFormat 图像缓存区数据的Metal 像素格式常量.注意如果MTLPixelFormatBGRA8Unorm和摄像头采集时设置的颜色格式不一致，则会出现图像异常的情况；
        参数6: width,纹理图像的宽度（像素）
        参数7: height,纹理图像的高度（像素）
        参数8: planeIndex.如果图像缓冲区是平面的，则为映射纹理数据的平面索引。对于非平面图像缓冲区忽略。
        参数9: textureOut,返回时，返回创建的Metal纹理缓冲区。
        */
        CVReturn status = CVMetalTextureCacheCreateTextureFromImage(NULL, self.textureCache, pixelBuffer, NULL, pixelFormat, width, height, 0, &texture);
        
        //6.判断textureCache 是否创建成功
        if (status == kCVReturnSuccess)
        {
            //7.转成Metal用的纹理
            textureY = CVMetalTextureGetTexture(texture);
            
            //8.使用完毕释放
            CFRelease(texture);
        }
    }
```

- 判断Y纹理 和UV 纹理是否都读取成功，如果读取成功，则分别将其传入片元着色函数
  通过`MTLRenderCommandEncoder`的`setFragmentTexture:atIndex:`函数传递



```objectivec
//10.判断textureY 和 textureUV 是否读取成功
    if (textureY != nil && textureUV != nil)
    {
        //11.向片元函数设置textureY 纹理
        [encoder setFragmentTexture:textureY atIndex:CJLFragmentTextureIndexTextureY];
        
        //12.向片元函数设置textureUV 纹理
        [encoder setFragmentTexture:textureUV atIndex:CJLFragmentTextureIndexTextureUV];
    }
    
    //13.使用完毕,则将sampleBuffer 及时释放
    CFRelease(CMSampleBufferRef);
```

**10.设置片元函数转化矩阵**
主要是将转换矩阵通过`setFragmentBuffer:offset:atIndex:`函数传入片元着色函数，用于将YUV格式转换为RGB格式



```csharp
//10.设置片元函数转化矩阵
        [commandEncoder setFragmentBuffer:self.convertMatrix offset:0 atIndex:CJLFragmentInputIndexMatrix];
```

## Metal着色文件

主要是对顶点数据以及纹理的处理

- 用于顶点函数输出/片元函数输入的结构体



```cpp
typedef struct
{
    float4 clipSpacePosition [[position]]; // position的修饰符表示这个是顶点
    
    float2 textureCoordinate; // 纹理坐标
    
} RasterizerData;
```

- 顶点着色函数：原样输出顶点坐标和纹理坐标



```csharp
//RasterizerData 返回数据类型->片元函数
// vertex_id是顶点shader每次处理的index，用于定位当前的顶点
// buffer表明是缓存数据，0是索引
vertex RasterizerData
vertexShader(uint vertexID [[ vertex_id ]],
             constant CJLVertex *vertexArray [[ buffer(CJLVertexInputIndexVertices) ]])
{
    RasterizerData out;
    //顶点坐标
    out.clipSpacePosition = vertexArray[vertexID].position;
    //纹理坐标
    out.textureCoordinate = vertexArray[vertexID].textureCoordinate;
    return out;
}
```

- 片元着色函数
  由于读取视频文件时采用的是YUV颜色编码格式，而最终屏幕的显示是RGB格式，所以需要在片元着色函数中将YUV格式转换为RGB格式



```cpp
//YUV->RGB 参考学习链接: https://mp.weixin.qq.com/s/KKfkS5QpwPAdYcEwFAN9VA
// stage_in表示这个数据来自光栅化。（光栅化是顶点处理之后的步骤，业务层无法修改）
// texture表明是纹理数据，CCFragmentTextureIndexTextureY是索引
// texture表明是纹理数据，CCFragmentTextureIndexTextureUV是索引
// buffer表明是缓存数据， CCFragmentInputIndexMatrix是索引
fragment float4
samplingShader(RasterizerData input [[stage_in]],
               texture2d<float> textureY [[ texture(CJLFragmentTextureIndexTextureY) ]],
               texture2d<float> textureUV [[ texture(CJLFragmentTextureIndexTextureUV) ]],
               constant CJLConvertMatrix *convertMatrix [[ buffer(CJLFragmentInputIndexMatrix) ]])
{
    //1.获取纹理采样器
    constexpr sampler textureSampler (mag_filter::linear,
                                      min_filter::linear);
    /*
     2. 读取YUV 纹理对应的像素点值，即颜色值
        textureY.sample(textureSampler, input.textureCoordinate).r
        从textureY中的纹理采集器中读取,纹理坐标对应上的R值.(Y)
        textureUV.sample(textureSampler, input.textureCoordinate).rg
        从textureUV中的纹理采集器中读取,纹理坐标对应上的RG值.(UV)
     */
    //r 表示 第一个分量，相当于 index 0
    //rg 表示 数组中前面两个值，相当于 index 的0 和 1，用xy也可以
    float3 yuv = float3(textureY.sample(textureSampler, input.textureCoordinate).r,
                        textureUV.sample(textureSampler, input.textureCoordinate).rg);
    
    //3.将YUV 转化为 RGB值.convertMatrix->matrix * (YUV + convertMatrix->offset)
    float3 rgb = convertMatrix->matrix * (yuv + convertMatrix->offset);
    
    //4.返回颜色值(RGBA)
    return float4(rgb, 1.0);
}
```

## 总结

`视频文件的解码`方式有以下两种

- 通过`AVAssetReader`自定义解码
  从MP4中拿到视频文件，将视频文件解压缩，即解码，还原成`CMSampleBufferRef`，然后在进行渲染
- 通过`AVFoundation`解码
  可以使用`AVFoundation`直接将mp4解压成想要的`CMSampleBufferRef`，不需要自己去解压，AVFoundation`视频解压本质`是通过 `封装的硬解码/硬编码`完成的，不需要亲自去做，这种方式是最简便的。

完整的代码见[Github](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fchenjialin1016%2FOpenGL-Demo) ：22_metal_视频文件渲染_OC