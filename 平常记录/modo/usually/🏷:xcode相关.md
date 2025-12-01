

xcode 相关

```objc
//snapkit 和父视图一样大小
make.edges.equalTo(self)

//可为nil/NULL
_Nonnull.  _Nullable
  
//缩写地址
 $(SRCROOT)
    
//Xcode swift package 位置
/Users/mac/Library/Developer/Xcode/DerivedData/MGConference-fsybrunhcmiqdafwejuwvypbapea/SourcePackages/repositories
  
  //Xcode快捷操作
open -a Xcode.app
快捷注释: comd+option + /
格式对其  Ctrl+ i
  
  
//设备支持位置
/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport
  
  //修改 host 地址  可进行dns解析更换
/etc/hosts    https://tool.chinaz.com/dns/

//常见报错  可能是searchPath/LibraryPath 路径错了(添加第三方的时候)
 Linker command failed with exit code 1 (use -v to see invocation)

//enable moudle
   
第三方引用如果存在c的代码,需要再 buildsetting enable moudle 中做NO处理,引入了就需要换成yes了
   
   //第三方有压缩文件,other Link 添加 -lz
与压缩,解压缩有关的链接问题,都可以通过 -lz解决.

//was built for iOS + iOS Simulator.
.xcodeproj Building for iOS, but the linked and embedded framework ‘XXX.framework' was built for iOS + iOS Simulator.

Buil Settings - Build Options - Validate Workspace 改为Yes
   
   //swift package  路径
/Users/mac/Library/Developer/Xcode/DerivedData/MGConference-fsybrunhcmiqdafwejuwvypbapea/SourcePackages/repositories
```

### 动态静态库

```objc
系统的.framework是动态库，我们自己建立的.framework一般是静态库。

//查看是否是 动态还是静态库

cd /Users/***/Desktop/Framework/MAMapKit.framework

file MAMapKit

archive表明这是一个静态库

dynamically linked shared library表明这是一个动态库



## //删除SDK中的i386，x86_86架构

 1.使用终端进入到SDK内部
 cd /Users/leo/Desktop/testDir/NIMSDK.framework
 2.查看当前支持的架构
 lipo -info NIMSDK
 可以看到NIMSDK当前支持的架构：
 Architectures in the fat file: NIMSDK are: i386 x86_64 armv7 arm64
 3.删掉i386，x86_86

lipo -remove i386 NIMSDK -o NIMSDK

lipo -remove x86_64 NIMSDK -o NIMSDK


.a文件中

lipo /Users/mac/Desktop/msdn_sdk_ios/lib/ugc/libMgFaceLab.a -remove x86_64 -output libMgFaceLab.a

lipo -info /Users/mac/Desktop/msdn_sdk_ios/lib/ugc/libMgFaceLab.a 
```

###  armv7  arm64设备

```objc
//  Architecture指令集相关
Architecture ： 指你想支持的指令集。
Valid architectures : 指即将编译的指令集。
Build Active Architecture Only : 只是否只编译当前适用的指令集
excluded architectures  : 排除哪些框架

armv6 设备	
iPhone, iPhone2, iPhone3G;
第一代、第二代 iPod Touch

armv7 设备	
iPhone3GS, iPhone4, iPhone4S; 
iPad, iPad2, iPad3(The New iPad), iPad mini；
iPod Touch 3G, iPod Touch4

armv7s设备	
iPhone5, iPhone5C,
iPad4(iPad with Retina Display)

ARMv8/arm64设备	
iPhone5S,iPhone6s（plus）、iPhoneSE，iPhone7（plus）
iPad Air, iPad mini2(iPad mini with Retina Display)
```

### 字符串/字典转data

```objc
NSData *messageData =  [NSJSONSerialization dataWithJSONObject:dataDic options:NSJSONWritingPrettyPrinted error:nil];
NSData *messageData = [dicString dataUsingEncoding:NSUTF8StringEncoding];
```



```objc
//webrtc  编译操作
export PATH=/Users/mac/depot_tools:$PATH

export PATH=$PATH:/Users/mac/depot_tools

export PATH=$PATH:/Users/mac/Desktop/owt/depot_tools

export DEPOT_TOOLS_UPDATE=0

python scripts/build.py  编译

gclient sync -D  更新代码  

export GCLIENT_PY3 = 0

```



