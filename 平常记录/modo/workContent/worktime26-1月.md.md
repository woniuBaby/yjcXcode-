>  2026年01月04日 - 2026年01月09日

星期日 

灵动岛-->数据扁平化

- **OC 层：完整解析服务端 JSON → 结构化 Model**
- **传给 Live Activity 的 ContentState：只放“已扁平化、已计算好的展示数据”**
  1. 灵动岛 SDK 方法名更换
  2. demo 增删图片,修改方法
  3. debug页面 环境调试修改,新增 自定义域名修改
  4. 新增游戏 debug 选择

星期一

1. 灵动岛插件,添加返回参数

2. 添加假数据

3. 灵动岛 颜色值拆机

4. subtitle 占位符拼接

   ```
   
   
   ```

星期二

1. 灵动岛 小组件添加
2. 灵动岛权限/group
3. domainlist/entry 路径修改

4. podfile 添加灵动岛相关
5. 版本号1.1.5(1)

星期三

灵动岛 更新(颜色值不传,不显示bug)

打包1.1.5(2)







live_activity-default-update请求参数: {"id":"flowerMature_land_1002","time":46,"countdown":{"title":[{"text":"白百合茁壮成长中!"}],"subTitle":[{"text":"距离成熟"},{"text":"{$time}","style":{"color":"#00d59d"}}],"btn":{"text":"前往花园"},"tip":{"text":"{$time}"},"icon":"https://hygncdn.babigame.cn//ios_island/flowerMature_countdown.png"},"completed":{"title":[{"text":"白百合"},{"text":"可收获","style":{"color":"#00d59d"}}],"subTitle":[{"text":"快去收获您的鲜花吧!"}],"tip":{"text":"可收获","style":{"color":"#00d59d"}},"btn":{"text":"前往花园"},"icon":"https://hygncdn.babigame.cn//ios_island/flowerMature_completed.png"},"direction":"left"}







---

> 2026年01月12日 ---2026年01月17日 

星期一

1.广告激励视频:  玩家反馈看广告提示奖励已到账 退出来显示要看完整视频

```
@刘雨欣（客服部-中文客服） @阎晋超（中台发行部-IOS工程师） 看看这个，这个玩家看激励视频提示奖励发放，但是原生返回为观看完成rewarded=false,
包名：cn.lbwdhysj.gf.ios
设备Id: C6415C5D-CBCA-466C-A09A-905DF38B81E7
{tip:"reward_video_topon_play","data":"{\"data\":{\"unitId\":\"b68414cba689c9\",\"csjCodeId\":\"b68414cba689c9\"},\"result\":{\"err\":\"\",\"data\":{\"user_load_extra_data\":{\"refresh\":9,\"placement_id\":\"b68414cba689c9\"},\"scenario_reward_number\":1,\"usd_to_rmb_rate\":\"6.9775\",\"ecpm_level\":14,\"abtest_id\":767006,\"country\":\"CN\",\"scenario_id\":\"f5e54970dc84e6\",\"network_type\":\"Network\",\"adsource_price\":599.185,\"publisher_revenue\":0.599185,\"rmb_to_usd_rate\":\"0.1433\",\"currency\":\"CNY\",\"adsource_id\":\"10156983\",\"segment_id\":0,\"adunit_format\":\"RewardedVideo\",\"network_name\":\"CSJ\",\"adsource_isheaderbidding\":0,\"ad_source_type\":1,\"network_placement_id\":\"969485133\",\"id\":\"bcba921b0789ebec56332bb679097396_10156983_1768184997040\",\"requestId\":\"bcba921b0789ebec56332bb679097396\",\"adsource_index\":7,\"req_id\":\"bcba921b0789ebec56332bb679097396\",\"scenario_reward_name\":\"reward_item\",\"adsource_bid_type\":0,\"s_id\":0,\"adunit_id\":\"b68414cba689c9\",\"placement_type\":1,\"precision\":\"estimated\",\"dismiss_type\":1,\"ext_info\":{\"ad_token\":\"s8d9aMxqM+zOOIVjYYG2CIMo0Tb0EPuTftDNjs3PuY6+77YoXEleNITxiOuPdMXpfHI7upyy/MXRlfE3Gx1aZGT35Vh1a6fsP/3JgYudiBDl0NJA8zypM/TvBk5PoE+qiWy+5vKb5XOqXqAHEsy7ki2PDlVs9uJilS4i9PM54uLORRlLjf8gZ/GiCD3pC9OF\",\"tag_id\":\"06b90d8b5ab5c6f7dc97f2d0a2f297e4\",\"pro_type\":2,\"request_id\":\"6C7DADA1-CCCE-45B4-A03E-97CC828CDBE0u4169\",\"sdk_bidding_type\":0},\"network_firm_id\":15},\"state\":1,\"type\":\"topon_play_close\",\"rewarded\":false}}","lang":"zh","token":"97501c2a90c50f61c3215942bf9b3fef-160-459","mdcl":"c459","mdgid":"160","env":"prod"}
```

2. 广告平台追溯 : 涉及的是一单广告诈骗投诉 期望溯源该账号观看过的广告来源 进一步定位广告平台

```
要查广告平台,需要加白名单,然后获取到广告的adsource_id，network_firm_id,network,去后台可以看到对应广告平台
```

3. fastlane  实现打包   区分海外?

```
# 国内 dev，feature 分支
fastlane ios dev --env cn branch:feature/test update_description:"修复灵动岛倒计时"
fastlane ios adhoc --env cn  update_description:"修复灵动岛倒计时"

# 海外 dev，release 分支
fastlane ios dev --env intl branch:release/intl update_description:"国际版上线内容"

```

星期二

1. 灵动岛打包 1.1.5 (10)
2. 自动打包 本地实现(环境选择)--->上传蒲公英-->飞书通知

3. 默认实现选择国内环境

```
fastlane ios huayuann_build type:adhoc  update_description:"版本号自动+1,测试总时间添加" 
```

星期三

1. iOS 工程整理  每次运行 大量build数据等cocos数据 变更且追踪,若提交代码量太大

```

#///////////////////////////
# Cocos Creator 3D Project
#///////////////////////////

# Cocos 生成但不应提交的目录
library/
temp/
local/
profiles/

# Cocos 构建输出（只精确忽略，不一刀切）
build/ios/node_modules/
build/ios/proj/Pods/
build/ios/proj/archives/
build/ios/proj/build/        # ← 新增：Xcode build / XCBuildData（关键）

# 如果你不打算提交 iOS 工程，才启用下面这一行
# build/

# ======================
# Xcode workspace
# ======================
*.xcworkspace/xcuserdata/
*.xcodeproj/xcuserdata/
*.xcuserstate

#//////////////////////////
# NPM
#//////////////////////////
node_modules/

#//////////////////////////
# VSCode
#//////////////////////////
.vscode/

#//////////////////////////
# WebStorm
#//////////////////////////
.idea/

#//////////////////////////
# macOS
#//////////////////////////
.DS_Store

#//////////////////////////
# Fastlane
#//////////////////////////
build/ios/proj/fastlane/report.xml
build/ios/proj/fastlane/Preview.html
build/ios/proj/fastlane/screenshots/
build/ios/proj/fastlane/test_output/
build/ios/proj/fastlane/.env*
!build/ios/proj/fastlane/.env.example
```

2. 游戏灵动岛打包  修改UI
3. 添加weburl

星期四

1.jenkins

2.查问题

```
355896696	17676086429097246185100	459	10713586  ys0catwxwzak 3051618100299
游戏名称: 我的花园世界 - 花园-正式-混服-公测 [160]
角色显示ID: ys0catwxwzak
角色ID:34702079101868
角色名称:s1868.崔晗雁
角色区服: 正式1868服
客服系统ID:1613850922153943497
17676086429097246185100
```

3.灵动岛bug 快速关闭权限再打开,图片显示默认

星期五

```

```

1. **Homebrew Ruby 3.2** 作为唯一 Ruby。 

2.  用 **gem 安装 bundler / fastlane / cocoapods**。 

3. 项目里用 **Gemfile + bundle exec** 管理版本。 

4. Jenkins 构建脚本 PATH 指向 Homebrew Ruby，保证 CI 用同一套环境。 

   

 根据这个建议我分步骤来 
1.现在我brew list 有2个 ruby ruby@3.2,其中一个应该是4.0的 那么可以 把4.0的删掉,然后只用3.2的 但是ruby@3.2 能不能去掉@3.2 这个? 

2.gem 安装 bundler / fastlane / cocoapods  是不是需要去 gemfile 中指定版本?然后步骤 

3.bundle 安装在什么地方了?怎么管理?上一步Gemfile 写好后是不是 Gemfile.lock 就建好了

4.Jenkins 构建脚本 PATH 指向 Homebrew Ruby

---



2026年01月19日 ~ 2026年01月23日

星期一

1. 海外花园 代码运行,将绝对路径 修改相对路径
   目的打包





星期二

1.plist的路径问题

游戏绝对路径--->相对路径

```
$(SRCROOT)/../../../native/engine/ios/Info.plist
$(SRCROOT)

/Users/lin/Documents/GardenGlobal/native/engine/ios，写法是：
$(SRCROOT)/../../../build/ios/proj

${BUILD_DIR}

${PODS_BUILD_DIR}

SRCROOT        = /Users/lin/Documents/GardenGlobal/native/engine/ios
PROJECT_DIR    = /Users/lin/Documents/GardenGlobal/native/engine/ios
PODS_ROOT      = /Users/lin/Documents/GardenGlobal/build/ios/proj/Pods


echo "SRCROOT--->srcroot是什么??????? = ${SRCROOT}"






```

2.引力引擎 升级5.08-->5.0.18

3.打包 1.1.5 --3

星期三

```
/Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources,
                    "/Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos/editor-support/spine/3.8",
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/SocketRocket,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/ios/include,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos/renderer,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos/platform,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos/renderer/core,
                    "/Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos/editor-support",
                    /Users/lin/Documents/HuaYuann/build/ios/proj/generated,
                    /Users/lin/Documents/HuaYuann/build/ios/proj/generated/cocos,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos/bindings/jswrapper,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/SocketRocket/Internal,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/SocketRocket/Internal/Utilities,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/SocketRocket/Internal/RunLoop,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/SocketRocket/Internal/Security,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/SocketRocket/Internal/Delegate,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/SocketRocket/Internal/IOConsumer,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/SocketRocket/Internal/Proxy,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/external/sources/khronos,
                    /Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos/platform/ios,
```

/Applications/Cocos/Creator/3.8.6/CocosCreator.app/Contents/Resources/resources/3d/engine/native/cocos/platform/ios

$(COCOS_CREATOR_PATH)/resources/3d/engine/native/external/sources
/opt/homebrew/include
/opt/homebrew/include
"$(SRCROOT)/MoDoGame/Frameworks/include"

---

2026年01月26日~ 2026年01月31日

星期一:
海外打包实现

各种分支可以显示 选择

^origin/(debug|master|release(/[^/]+)?)$

星期四

全局后置 钩子处理临时分支

前置准备 → 2. 记录当前原分支 → 3. 暂存原分支未提交修改 → 4. 创建并切换到临时打包分支 → 5. 临时分支执行资源替换 + IPA 打包 → 6. 无论打包成败，切回原分支 → 7. 恢复原分支暂存的修改 → 8. 校验并删除临时打包分支 → 9. 清理 Git 痕迹 / 全局变量，恢复干净环境

星期五
rn资源添加

```
^origin/(iOS_release(/[^/]+)?)$
```

星期六

卡40%

```
渠道: 未知
客服系统ID: 1613850922154124464
IP: 175.19.75.154
语言: zh_cn【中文】
maxLvl: -
手机系统版本: 26.2.1
内存情况: 5687
packageId: cn.lbwdhysj.gf.ios
包版本: v1.1.5
系统语言: zh-Hans-CN
网络类型: wifi
设备id: 9683BF77-AA0D-44F4-BED1-3529362A0351
设备名: iPhone15
设备类型: ios
sdkOpenId: x3rfic9i5e
19142918101140，ys06sa4v6jro，176175123193101567248109，


网络类型: 4g
设备id: A5CD82A3-7695-4E8E-AEF8-7C23E456BEAB
```

---

