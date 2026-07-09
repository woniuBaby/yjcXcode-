iOS   sentrySDK无法符号化解析bug内容

# 一、项目配置 apple-ios-wdhysj

https://sentry.babigame.cn/organizations/sentry/insights/projects/

```Bash
# 如果是 Apple Silicon，用 brew 装的 sentry-cli 一般在 /opt/homebrew/bin
if [[ "$(uname -m)" == arm64 ]]; then
    export PATH="/opt/homebrew/bin:$PATH"
fi

# 打印 dSYM 相关环境变量，确认 Xcode 真的生成了 dSYM
echo "DWARF_DSYM_FOLDER_PATH: $DWARF_DSYM_FOLDER_PATH"
echo "DWARF_DSYM_FILE_NAME: $DWARF_DSYM_FILE_NAME"
echo "Full dSYM path: ${DWARF_DSYM_FOLDER_PATH}/${DWARF_DSYM_FILE_NAME}"
ls -R "$DWARF_DSYM_FOLDER_PATH"

if which sentry-cli >/dev/null; then
  export SENTRY_ORG=sentry                # 你的 org slug
  export SENTRY_PROJECT=apple-ios-wdhysj  # 你的 project slug
  export SENTRY_AUTH_TOKEN=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  # 你的 token

  ERROR=$(sentry-cli debug-files upload \
    --force-foreground \
    #--include-sources \ #源码建议不用传
    "$DWARF_DSYM_FOLDER_PATH")

  if [ ! $? -eq 0 ]; then
    echo "warning: sentry-cli - $ERROR"
  fi
else
  echo "warning: sentry-cli not installed, download from https://github.com/getsentry/sentry-cli/releases"
fi
```

#### Run Script 的 Input Files

脚本下方还有内容:添加 input file lists

```Plain
${DWARF_DSYM_FOLDER_PATH}/${DWARF_DSYM_FILE_NAME}/Contents/Resources/DWARF/${EXECUTABLE_NAME}
```

在 Target 的 Build Settings 里：

`ENABLE_USER_SCRIPT_SANDBOXING` 设为 `NO`。

`DEBUG_INFORMATION_FORMAT` 设为： `DWARF with dSYM File`

  官方文档:https://docs.sentry.io/platforms/apple/guides/ios/

# 二、 手动触发bug

可以捕获bug,并记录事件信息,但是无法具体符号化内容

![img](https://modoglobal.feishu.cn/space/api/box/stream/download/asynccode/?code=OWEyMWI3ZTI1NjI3NjkyZTA2MTRjYzExNjZkZDQyNGRfUGx0ektnQTdzRlJMamduMUNER3hvN0VtMU5DWEJ3ajRfVG9rZW46STRETmI5cGtmb21HdFZ4cVlXY2NjWUpFbmJJXzE3NjQwNjU4NDk6MTc2NDA2OTQ0OV9WNA)

上传的Dsym符号表  ID匹配

![img](https://modoglobal.feishu.cn/space/api/box/stream/download/asynccode/?code=ZTIyYWM2NGY2MTFmZjAyZmM0MTAwMDBkYzJiZjg2NWNfZzBMRzdaMUljelFrUnRDd2pPZVBPRUZQMkM2VWJJUzJfVG9rZW46VHlMbWJoR0ZUb1NwcDN4UmtGSGNvZFFnbnJkXzE3NjQwNjU4NDk6MTc2NDA2OTQ0OV9WNA)

# 三、问题 

无法找到对应的dsym ,可实际在调试界面 存在 (Debug ID 验证)

```Plain
A required debug information file was missing (1)收起
File Name HuaYuann
File Path /private/var/containers/Bundle/Application/8A8AFCED-3A7C-4867-88CD-B58E53233DD1/HuaYuann.app/

Debug ID b226f1dc-0c52-32eb-9f5a-5f6c5545234d
```

# 四、解决过程,排除其他因素

1. org slug / project slug /token 确认无误
2. 多种方式上传dsym,(脚本/手动/平台),确定上传成功,且ID 匹配
3. 先上传dsym,等待一小时,再手动触发bug (防止符号表未上传)
4. 多种 bug 测试,都可以捕获,但是依旧无法解析 

# 五、提出工单邮箱寻求解决

![img](https://modoglobal.feishu.cn/space/api/box/stream/download/asynccode/?code=YWFjNWJlNTM5ZGYyM2I2ZmQ5ZDFiMmE5NWMxZmI1YjJfaWVBQXh3RlNVVWxDYjBuUVV4ODNqSTFYTHEzV2l6OVNfVG9rZW46TFkzemJWemJObzMzbEd4enh6cGNVUnprbkxiXzE3NjQwNjU4NDk6MTc2NDA2OTQ0OV9WNA)

得到回复:

![img](https://modoglobal.feishu.cn/space/api/box/stream/download/asynccode/?code=M2MyZjc3MjM0NDI5MTQ4OTY5ZmJhNzI3ODQ1ZTExOTdfcXB4TjJWdm94bEszT0Y3NGJRUlhLQUR0WDBmcGp2RzJfVG9rZW46QlVUZGJsWEhmb1Q0NGV4UW5TUmNzV3J3bk9nXzE3NjQwNjU4NDk6MTc2NDA2OTQ0OV9WNA)

> 仅为付费用户提供托管 SaaS 平台的支持。您可以查阅我们的文档、Sentry 开发者文档或 Sentry 自托管指南

# 六、官方文档/git查询结果

1. Git 同类型 issues 得到回复 https://github.com/getsentry/self-hosted/issues/3441  

![img](https://modoglobal.feishu.cn/space/api/box/stream/download/asynccode/?code=NmI2Nzg4NjY0ZTMxNTY3MjVmZWY3N2EyNzg1ZTE0NjhfV2VQQmRZUW5mWWNHWmJFckpLZjlCTHYwMzlLZ0U1VjhfVG9rZW46TGpTNWJwbW5wb3hXR1l4Y0gxcGNDV09rbkZlXzE3NjQwNjU4NDk6MTc2NDA2OTQ0OV9WNA)

> 我们无法在自托管环境中轻松地对 iOS 进行符号化，因为 Apple 不允许重新分发其符号，也不提供符号服务器：
>
> iOS 代码库在自托管环境中不可用，因为 Apple 不提供公共符号服务器，也不允许我们自行分发调试信息文件 (DIF)。
>
> 这一点应该在我们的自托管文档中明确指出，而不是放在这个隐蔽的角落里，因此我会将此问题保留到文档相关的部分。

#### Sentry 官方明确自己不会、也不能，在自托管版本里再分发 iOS 系统符号。Sentry 对“符号不可再分发”的态度是基于 Apple 政策

我们是自托管:https://sentry.babigame.cn/

官方说明区分方式： 如果您访问的是`https://你的orgslug.sentry.io/`此类域名，就是**SaaS（托管版）**； 如果是您自己公司的域名（比如`https://sentry.example.com`），就是**自托管**。

- **SaaS用户**：可以使用Sentry官方支持。
- **自托管用户**：官方支持无法登陆您的环境帮助查看问题。

> 如果你是 SaaS 用户（用的是 sentry.io 官方托管版）：
>
> ​         在 SaaS 上，项目的 Debug Files → Symbol Servers 里，有一个内置的 iOS built‑in repository 默认是启用的；Sentry 会通过这个内置仓库去获取 iOS 系统库的符号（在 Apple 允许的前提下），用来符号化 UIKit、Foundation 等系统框架的堆栈。[Apple symbol servers]
>
> ​        在 自托管（self‑hosted） 环境里，这个内置的 iOS 仓库是 不可用 的：
>
> iOS 仓库在自托管中不可用，因为 Apple 不提供公共符号服务器，也不允许我们分发这些调试信息文件（DIFs）本身。[Apple symbol servers; Self‑hosted docs]

成为SaaS :https://sentry.zendesk.com/hc/en-us/articles/26365105087643-The-price-on-the-Manage-Subscription-page-differs-from-what-is-shown-on-the-Pricing-Page-why?utm_source=chatgpt.com

> 只有签订年度计划才能享受每月 80 美元和每月 26 美元的价格。
>
> 团队版套餐价格为每年 312 美元，企业版套餐价格为每年 960 美元。如果您选择年度套餐，则需要一次性预付全部费用。如果您只想选择月度套餐，最便宜的团队版套餐价格为每月 29 美元，企业版套餐价格为每月 89 美元。 
