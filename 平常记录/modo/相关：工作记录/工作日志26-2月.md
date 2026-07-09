2026年02月02日~

星期一: 多域名替换

```
/Users/lin/Documents/HuaYuann/native/engine/ios/domainList.json
```

星期二:
RN资源 podfile 处理 不打包走main,打包走ENV

pod 'MDRNSource', :git => 'ssh://git@192.168.13.252:8022/modo_ios/mdrnsource.git', :branch => (ENV['RN_POD_BRANCH'] || 'main')





星期三:
脚本代码优化
封装!

```
Fastfile 可优化点清单（更结构化 / 方法封装）
1. 配置加载与集中（推荐优先做）
现状：huayuann_build 里大量 ENV[]、Dotenv、workspace/scheme/profile 等散落在一处，变量多、顺序敏感。
建议：
抽一个 load_build_config：在 Dotenv 之前保存 Jenkins 相关 ENV（如 RN_POD_BRANCH、NATIVE_BILITY_BRANCH），再 Dotenv.load，然后读取所有用到的 ENV，返回一个 配置对象（Hash 或 OpenStruct），例如：
type, update_message, branch
workspace, xcodeproj, scheme, bundleid, bundleid_la
profile_adhoc, profile_la_adhoc, pgyer_api_key, feishu_webhook
export_method, signing_style（由 type 推导）
lane 里只写：config = load_build_config(options)，后面一律用 config.xxx，逻辑更清晰、也方便单测。
2. Jenkins 分支参数写回 ENV（去前缀）
现状：RN_POD_BRANCH、NATIVE_BILITY_BRANCH 两段「去前缀并写回 ENV」重复模式。
建议：抽成 apply_jenkins_pod_branches(rn_branch, native_bility_branch)：
入参：两个 Jenkins 传入的原始字符串；
内部：rn_branch 去 rn/ 前缀写回 ENV["RN_POD_BRANCH"]，native_bility_branch 去 nativeBility/ 前缀写回 ENV["NATIVE_BILITY_BRANCH"]（空串则不写）；
可选：同时做 UI.message 打印。
效果：lane 里两行 if 变成一行方法调用，逻辑集中、易维护。
3. 参数打印
现状：二十多行 UI.message("key: value") 连写。
建议：抽成 print_build_params(config)（或 print_build_params(config, extra)）：
入参：上面的 config + 可选 extra（如 $ipa_name、$output_path 等全局）；
内部：用 Hash 组织 key => value，each { |k, v| UI.message("#{k}: #{v}") }；
敏感信息可在此方法里做脱敏或省略。
效果：主流程变短，以后增删打印项只改一处。
4. Git 分支准备（fetch / checkout / stash / 临时分支）
现状：一大段 Dir.chdir(GIT_ROOT) 里包含 fetch、checkout、stash、创建临时分支，并设置 $ORIGIN_BRANCH、$ORIGIN_STASH_ID、$TEMP_BUILD_BRANCH。
建议：抽成 prepare_git_build_branch(git_root, branch)：
入参：GIT_ROOT、要打包的 branch；
内部：完成上述所有 Git 操作并设置那三个全局变量；
无返回值或只返回 success。
效果：huayuann_build 里从「一屏 Git 代码」变成一行 prepare_git_build_branch(GIT_ROOT, config.branch)，主流程可读性明显提升。
5. 签名 / export_options 构造
现状：根据 signing_style 分支构造 export_options（manual 时填 provisioningProfiles）。
建议：抽成 build_export_options(signing_style, bundleid, bundleid_la, profile_adhoc, profile_la_adhoc)，返回 Hash，供 build_ios_app(export_options: build_export_options(...)) 使用。
效果：打包调用处只关心「用哪份配置」，不关心签名细节。
6. 飞书通知（成功 / 失败复用）
现状：成功时一段「组文案 → payload → Tempfile → curl」；rescue 里失败通知又一段几乎相同结构，仅文案和变量不同。
建议：抽成 send_feishu_notification(webhook_url, text)：
入参：feishu_webhook、要发送的 text；
内部：webhook 为空则 return；否则构造 { msg_type: "text", content: { text: text } }.to_json，用 Tempfile 写 body 再 sh("curl -s -X POST ...")。
成功与失败处都只组好 text 后调用该方法。
效果：去掉重复的 Tempfile + curl，以后改飞书格式只改一处。
7. 耗时计算与文案
现状：成功分支里「end_time - start_time → 分钟/秒 → duration_text」；rescue 里又类似一段。
建议：抽成 format_build_duration(start_time)，返回 "X分Y秒" 字符串（若 start_time 为 nil 则返回 "0分0秒" 或 "?"）。成功和失败通知都传 $BUILD_START_TIME 即可。
效果：两处耗时逻辑统一，避免复制粘贴错误。
8. domain_list 路径解析
现状：
ENV["DOMAIN_LIST_JSON_PATH"].to_s.strip.empty? ? options[:domain_list_path].to_s.strip : ENV["DOMAIN_LIST_JSON_PATH"].to_s.strip 写在一行。
建议：抽成 resolve_domain_list_path(options)：
优先 ENV DOMAIN_LIST_JSON_PATH，为空则用 options[:domain_list_path].to_s.strip，返回一个字符串。
效果：调用处写 replace_domain_list_if_needed(domain_list_path: resolve_domain_list_path(options))，语义清晰。
9. ensure 块：Git 清理 + 全局变量重置
现状：ensure 里一长段「切回原分支、reset、clean、删临时分支、stash pop、清空全局变量」。
建议：抽成 cleanup_git_after_build（无参）：
内部 Dir.chdir(GIT_ROOT) 做所有 Git 恢复与删除，最后统一清空 $ORIGIN_BRANCH、$TEMP_BUILD_BRANCH、$BUILD_START_TIME、$ipa_name 等全局变量。
ensure 里只写一行 cleanup_git_after_build。
效果：ensure 意图一目了然，修改清理逻辑时只改一个方法。
10. 成功流程「打包 → 蒲公英 → 飞书」分段
现状：build_ios_app → pgyer → 算耗时 → 组飞书文案 → 发飞书，全部线性写在 begin 里。
建议（可选）：再抽一个 notify_build_success(config, pgyer_result, start_time)：
入参为 config、蒲公英返回的 hash、开始时间；内部完成「耗时计算 + 飞书文案组装 + send_feishu_notification」。
效果：主流程变成「build → pgyer → notify_build_success」，读起来像提纲。
11. 文件顶部常量与注释
现状：全局变量、路径常量、流程注释混在文件头。
建议：
常量集中一块（如 PATH_CONSTANTS、GIT_*、DOMAIN_LIST_JSON、APPICON_FILE）；
流程说明保留，但可收束为「1. 配置 2. Git 3. Pod/资源 4. 打包 5. 上传 6. 通知 7. ensure 清理」几条，与重构后的方法名对应。
效果：新人看顶部就能理解整体结构。
12. dev / adhoc / app_store 与 huayuann_build 的接口
现状：dev/adhoc 传 branch、update_message、export_method、signing_style，但 huayuann_build 实际只用 ENV（BRANCH、CHANGELOG、BUILD_TYPE），options 未参与主流程；app_store 使用未定义的 workspace、scheme、bundleid 等。
建议：
明确约定：Jenkins 用 ENV，本地用 options 时，在 load_build_config 里统一：
branch = ENV["BRANCH"].presence || options[:branch] || "master" 等，这样 dev/adhoc 传参才有意义；
或删除 dev/adhoc 里无效的 options，避免误导；
app_store：从 ENV 或共用 load_build_config 读 workspace/scheme/bundleid/profile，避免未定义变量。
效果：入口行为清晰，避免「以为改了 options 会生效」的坑。

```

4. jenkins 参数化 UI视图化,多项git文件处理更换资源
   1.RN资源 main.jsbundle
   2.能力SDK: 本地项目 编译出的framwork 放到指定仓库(build 要将debug-->release) 打包需要用的是 release包,指定版本号(默认选择本地开发最新包)
   3.项目工程 最初一直在master 上开发,不合适,添加release 版本包

5. 打包错误日志 全局抓取,不局限打包期间(远程拉取代码/打包前before/打包完成后工程删除临时分支等)

   输出日志完善完整





----

2026年02月09日~2026年02月13日
星期一

1.问题查询

```
工单类型: 普通工单
是否高V:否
平台:混服
游戏:我的花园世界
账号:ys013b1sya4o
角色ID:3080256100296
区服:s296
角色名:s296.可口可乐
问题类型:游戏闪退
问题标题:游戏闪退
问题描述:玩家反馈一直游戏闪退，试过重装游戏，麻烦帮忙看一下
SDK openid:175665169085268240910513
设备类型:ios

对应设备id : 22AFB3CC-EE02-4166-A47B-2E9E435010A2
```

2.更换引擎固定资源

星期三

1.灵动岛相关bug

2. 闪退

星期四
```
14号 重庆 一天的火车(650 * 2) 晚上吃个饭/住房  + 200 +200  -->1700
15 中下午-->成都 150* 2 票,吃200,住300,晚饭200 -->1000
16 都江堰/青城山/熊猫-->300+  吃/500 住300   -->1100 (春节吃好+300)
17 18 19 黄龙/九寨沟/四姑娘 3天 --> 3000
20/21 峨眉山/乐山  -->800 * 2 -->1600

22  匀一天到(1000)  阿坝州  
23 必须走 -->车票预估2000

```

1.1.7版本

```
  #调试
  pod 'DoraemonKit/Core', '~> 3.0.7', :configurations => ['Debug'] #必选
  pod 'DoraemonKit/WithLogger', '~> 3.0.7', :configurations => ['Debug'] #可选
  
  
  #ifdef CC_DEBUG
#import <DoraemonKit/DoraemonManager.h>
#endif

    #ifdef CC_DEBUG
   [[DoraemonManager shareInstance] installWithPid:@"productId"];//productId为在“平台端操作指南”中申请的产品id
   #endif
```

星期五
```
📊 设备性能 - CPU: 6核, 内存: 42.6MB, maxLogLength: 8KB
📊 设备性能 - CPU: 6核, 内存: 56.7MB, maxLogLength: 8KB
📊 设备性能 - CPU: 6核, 内存: 96.8MB, maxLogLength: 8KB

可用内存 > 500MB 几乎不卡
200MB ~ 500MB  FPS波动,资源加载慢
100MB ~ 200MB  危险区  动画掉帧 页面卡顿
< 100MB  极危险区  系统开始压缩内存 随时可被系统杀死
```

```
    float availableMemory =
    (vmStats.free_count +
     vmStats.inactive_count +
     vmStats.purgeable_count) * vm_page_size / 1024.0 / 1024.0;
    return availableMemory;
可用缓存 计算  
```



----

2026年02月23日

```
游戏名称: 我的花园世界 - 花园-正式-混服-公测 [160]
角色显示ID: ys0904wfeut8
角色ID:25400657101436
角色名称: s1436.困困困
角色区服: 1436
角色VIP等级: 未知
渠道: 官方渠道
客服系统ID: 1613850922154221810
设备id: 30B50F31-3BFD-436B-8C73-BAFFD0CE8BE0
设备名: iPhone13ProMax
该玩家也是点击开始游戏就闪退麻烦看看@杜嘉威（运营二部-运营专员） @傅建生（玲珑坊-项目经理）
```



2026年02月24日 星期二

1. 打印进度

   ```
   2026-02-24 17:23:02.874 HuaYuann[730:63465] updateFirstProgress -------- 更新进度 40
   2026-02-24 17:23:02:874 HuaYuann[730:63465] [iOS_NativeLog]: [CocosMainView.m] [-[CocosMainView registerEmitter]_block_invoke_3] [Line 1231]updateProgress -------- 40.000000
   2026-02-24 17:23:02.874 HuaYuann[730:63465] updateFirstProgress -------- 更新进度 66
   2026-02-24 17:23:02:874 HuaYuann[730:63465] [iOS_NativeLog]: [CocosMainView.m] [-[CocosMainView registerEmitter]_block_invoke_3] [Line 1232]updateProgress11 -------- 首次加载时间稍长，请耐心等待...
   
   
   2026-02-24 17:23:03:196 HuaYuann[730:63465] [iOS_NativeLog]: [CustomProgressView.m] [-[CustomProgressView updateProgressViewUI]] [Line 200]进度条进度 ------------- updateProgressViewUI : 19
   2026-02-24 17:23:03:229 HuaYuann[730:63465] [iOS_NativeLog]: [CustomProgressView.m] [-[CustomProgressView updateProgressViewUI]] [Line 200]进度条进度 ------------- updateProgressViewUI : 20
   2026-02-24 17:23:03:262 HuaYuann[730:63465] [iOS_NativeLog]: [CustomProgressView.m] [-[CustomProgressView updateProgressViewUI]] [Line 200]进度条进度 ------------- updateProgressViewUI : 21
   
   2026-02-24 17:23:03:588 HuaYuann[730:63465] [iOS_NativeLog]: [Channel_cocos.m] [-[Channel_cocos onSendDataDict:extInfo:]] [Line 180]到这---------{"type":"response","data":{"status":1},"uk":"run|1771924983517|0.5055168284100667|96"}
   2026-02-24 17:23:03:588 HuaYuann[730:63465] [iOS_NativeLog]: [Channel_cocos.m] [-[Channel_cocos onSendDataDict:extInfo:]] [Line 180]到这---------{"type":"response","data":{"status":1},"uk":"run|1771924983517|0.23078510979403888|97"}
   2026-02-24 17:23:03:588 HuaYuann[730:63465] [iOS_NativeLog]: [Channel_cocos.m] [-[Channel_cocos onSendDataDict:extInfo:]] [Line 180]到这---------{"type":"response","data":{"status":1},"uk":"run|1771924983520|0.1739630289789681|98"}
   ```

   频繁

```
2026-02-24 17:31:02:032 HuaYuann[730:63465] [iOS_NativeLog]: [MDLogModeService.m] [-[MDLogModeService ping]] [Line 754]ping----------------------ping

2026-02-24 17:31:03:000 HuaYuann[730:64420] [iOS_NativeLog]: [MDLogModeService.m] [-[MDLogModeService webSocket:didReceivePong:]] [Line 798]收到ping回调-------------------------------

2026-02-24 17:31:32:062 HuaYuann[730:63465] [iOS_NativeLog]: [MDLogModeService.m] [-[MDLogModeService ping]] [Line 754]ping----------------------ping
2026-02-24 17:31:32:096 HuaYuann[730:63511] [iOS_NativeLog]: [MDLogModeService.m] [-[MDLogModeService webSocket:didReceivePong:]] [Line 798]收到ping回调-------------------------------
```

星期三  2026年02月25
```
可用内存（Available Memory）
Free memory（完全空闲）
Inactive memory（可回收）
Purgeable memory（可丢弃缓存）

>500MB   安全
200-500  轻微卡
100-200  明显卡
<100MB   高概率被杀
```

闪退记录

```
第一个:
游戏名称: 我的花园世界 - 花园-正式-混服-公测 [160]
角色显示ID: ys0904wfeut8
角色ID:25400657101436
角色名称: s1436.困困困
角色区服: 1436
角色VIP等级: 未知
渠道: 官方渠道
客服系统ID: 1613850922154221810
设备id: 30B50F31-3BFD-436B-8C73-BAFFD0CE8BE0
设备名: iPhone13ProMax
该玩家也是点击开始游戏就闪退麻烦看看

有日志 可查看


```

星期四

1.[Channel_cocos onSendDataDict] crash 修正,添加判空

2.添加性能 UI  以及打印

3.优化日志输出,超长日志折叠/广告等级变低

星期五  
计划:
1.crash 搜集到服务端
2.crash 总结
3.卡进度相关内容
4.RN
5.打包 (一键打包  涉及  更新分支)

实际:

1.查询ipad用户 点击开始crash

```
设备id 455C8D86-3A0B-44D3-A6FD-FC51CFC20B9B

设备id: 30B50F31-3BFD-436B-8C73-BAFFD0CE8BE0 昨天加的上面

iphone 12 :  AEC5E7B4-701D-423C-A8E8-FC6A77E59E1D
```

星期六
```
需要处理:
1.更换头像
2.性能日志打印逻辑(是否是开启白名单)
//开启 白名单/debug模式 才去打印性能调试
    //性能日志属于: 频繁打印
    //白名单:日志等级默认 all 会打印
    //debug模式: 日志等级默认 debug 不打印
    if (self.debugMode == YES) {
        [MDPerformanceCollector startLoggingEvery:2.0];  // 每 2 秒打一次
    }
    
  
3.星期四添加的 30B50F31-3BFD-436B-8C73-BAFFD0CE8BE0闪退
						 30B50F31-3BFD-436B-8C73-BAFFD0CE8BE0
```


---

![image-20260302100952645](/Users/lin/Library/Application Support/typora-user-images/image-20260302100952645.png)
