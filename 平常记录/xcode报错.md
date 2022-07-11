* //常见报错 可能是searchPath/LibraryPath 路径错了(添加第三方的时候)

 Linker command failed with exit code 1 (use -v to see invocation)



* //enable moudle

第三方引用如果存在c的代码,需要再 buildsetting enable moudle 中做NO处理,引入了就需要换成yes了



* //第三方有压缩文件,other Link 添加 -lz

与压缩**,**解压缩有关的链接问题**,**都可以通过 **-lz**解决**.**



* //was built for iOS + iOS Simulator.

.xcodeproj Building for iOS, but the linked and embedded framework ‘XXX.framework' was built for iOS + iOS Simulator.



Buil Settings - Build Options - Validate Workspace 改为Yes

