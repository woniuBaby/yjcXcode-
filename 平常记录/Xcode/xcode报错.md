* //常见报错 可能是searchPath/LibraryPath 路径错了(添加第三方的时候)

 Linker command failed with exit code 1 (use -v to see invocation)

***



* //enable moudle

第三方引用如果存在c的代码,需要再 buildsetting enable moudle 中做NO处理,引入了就需要换成yes了

***

* //第三方有压缩文件,other Link 添加 -lz

与压缩**,**解压缩有关的链接问题**,**都可以通过 **-lz**解决**.**

***



* //was built for iOS + iOS Simulator.

.xcodeproj Building for iOS, but the linked and embedded framework ‘XXX.framework' was built for iOS + iOS Simulator.



Buil Settings - Build Options - Validate Workspace 改为Yes

***



> The Legacy Build System will be removed in a future release. You can configure the selected build system and this deprecation message in File > Project Settings.

原因：

旧版生成系统将在未来版本中删除。您可以在“文件”>“工作区设置”中配置选定的生成系统和此弃用消息。

解决：

Xcode工具栏 ---> File ---> Workspace Settings ---> Build System：

将Legacy Build System 改为 New Build System，点击Done即可。


in file included from <built-in>:1: