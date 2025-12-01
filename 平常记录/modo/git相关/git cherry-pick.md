git cherry-pick e0eb0a34b3716391d2c41d8717948b29e333ed1b  添加指定某次提交

```objective-c
yanjinchaodeMacBook-Pro:msdn_sdk_ios mac$ git cherry-pick e0eb0a34b3716391d2c41d8717948b29e333ed1b

Auto-merging MGLiveRoom/MGLiveSDK/Classes/ExternalVideoCapture/MGExternalVideoCameraCapture.mm

CONFLICT (content): Merge conflict in MGLiveRoom/MGLiveSDK/Classes/ExternalVideoCapture/MGExternalVideoCameraCapture.mm

error: could not apply e0eb0a3... feat-fix(ios-demo): demo 修复美颜数值报错记录

hint: After resolving the conflicts, mark them with

hint: "git add/rm <pathspec>", then run

hint: "git cherry-pick --continue".

hint: You can instead skip this commit with "git cherry-pick --skip".

hint: To abort and get back to the state before "git cherry-pick",

hint: run "git cherry-pick --abort".

yanjinchaodeMacBook-Pro:msdn_sdk_ios mac$ git add MGLiveRoom/MGLiveSDK/Classes/ExternalVideoCapture/MGExternalVideoCameraCapture.mm

yanjinchaodeMacBook-Pro:msdn_sdk_ios mac$ git cherry-pick --continue

[pugc_tsg 69f42cd] feat-fix(ios-demo): demo 修复美颜数值报错记录

 Date: Fri Feb 10 15:47:05 2023 +0800
```



![image-20230213143551175](/Users/mac/Library/Application Support/typora-user-images/image-20230213143551175.png)

