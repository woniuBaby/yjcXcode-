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



//关于动态库 与 静态库
系统的.framework是动态库，我们自己建立的.framework一般是静态库。

//查看是否是 动态还是静态库
cd /Users/***/Desktop/Framework/MAMapKit.framework
file MAMapKit


archive表明这是一个静态库
dynamically linked shared library表明这是一个动态库

//获取ssh
cd ~
cd .ssh
cat id_rsa.pub

//删除SDK中的i386，x86_86架构 1.使用终端进入到SDK内部 cd /Users/leo/Desktop/testDir/OGLFramework.framework 2

.查看当前支持的架构 lipo -info OGLFramework  

可以看到OGLFramework当前支持的架构： Architectures in the fat file: OGLFramework are: i386 x86_64 armv7 arm64 3.删掉i386，x86_86
lipo -remove i386 OGLFramework -o OGLFramework
lipo -remove x86_64 OGLFramework -o OGLFramework  (需要手动输入 复制未起作用)

.a文件中
lipo /Users/mac/Desktop/msdn_sdk_ios/lib/ugc/libMgFaceLab.a -remove x86_64 -output libMgFaceLab.a

lipo -info /Users/mac/Desktop/msdn_sdk_ios/lib/ugc/libMgFaceLab.a 