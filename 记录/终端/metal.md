##  SDK中引入 .metal文件 处理



问题答案来自:

https://stackoverflow.com/questions/65257909/ios-framework-mtllibrary-newfunctionwithname-fails-when-cikernel-flag-is-set-on



> metal 转换成为 air 文件

```
规则输入:
xcrun metal -c -I $MTL_HEADER_SEARCH_PATHS -fcikernel "${INPUT_FILE_PATH}" -o "${SCRIPT_OUTPUT_FILE_0}"

规则输出:

$(DERIVED_FILE_DIR)/$(INPUT_FILE_BASE).air
```



>  air 文件metallib

```
规则输入:
xcrun metallib -fcikernel "${INPUT_FILE_PATH}" -o "${SCRIPT_OUTPUT_FILE_0}"

规则输出:
$(METAL_LIBRARY_OUTPUT_DIR)/$(INPUT_FILE_BASE).metallib
```



https://developer.apple.com/videos/play/wwdc2020/10021/    苹果官方解答

https://developer.apple.com/forums/thread/52287   另外一种解答(效果不佳)



