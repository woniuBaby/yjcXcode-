2026年02月02日~

星期一: 

```
/Users/lin/Documents/HuaYuann/native/engine/ios/domainList.json
```

星期二

```
require_relative '../node_modules/react-native/scripts/react_native_pods'
require_relative '../node_modules/@react-native-community/cli-platform-ios/native_modules'

platform :ios, '12.0'
source 'https://github.com/CocoaPods/Specs.git'

workspace "HuaYuann.xcworkspace"

install! 'cocoapods', :deterministic_uuids => false

# NativeBility 版本/本地切换：export NATIVE_BILITY_PATH=/path/to/NativeBility.xcodeproj 使用本地
NATIVE_BILITY_PROJECT = ENV['NATIVE_BILITY_PATH'] || File.expand_path('../../../../modo-native-ability-ios/NativeBility/NativeBility.xcodeproj', __dir__)

# RN 静态 + 个别第三方动态，全局先设静态
use_frameworks! :linkage => :static

# ********************************* 公用能力库（只声明一次，App 与 NativeBility 共用）*********************************
def shared_pods
  pod 'MJExtension', '3.4.1'
  pod 'AFNetworking', '4.0.1'
  pod 'SSZipArchive'
  pod 'libpag', '4.3.33'
  pod 'Masonry', '~> 1.0.2'
  pod 'FMDB', '2.7.5'
  pod 'SVProgressHUD', :git => "https://github.com/SVProgressHUD/SVProgressHUD.git"
  pod 'CocoaLumberjack', '3.8.0'
  pod 'ZXingObjC', '~> 3.6.4'
  pod 'ActivitySDK', :git => "ssh://git@192.168.13.252:8022/modo_ios/activitysdk.git"
  pod 'MDRNSource', :git => 'ssh://git@192.168.13.252:8022/modo_ios/mdrnsource.git', :branch => (ENV['RN_POD_BRANCH'] || 'main')
  pod 'AnyThinkiOS', '6.4.92'
  pod 'AnyThinkBaiduSDKAdapter', '6.4.92.2'
  pod 'AnyThinkKuaiShouSDKAdapter', '6.4.92.2'
  pod 'AnyThinkSigmobSDKAdapter', '6.4.92.2'
  pod 'AnyThinkTTSDKAdapter', '6.4.92.1'
  pod 'AnyThinkGDTSDKAdapter', '6.4.92.2'
  pod 'WechatOpenSDK', '2.0.5'
  pod 'TencentOpenAPIMD', :git => 'ssh://git@192.168.13.252:8022/cjx/TencentOpenAPIMD.git'
  pod 'GravityEngineSDK'
  pod 'ATAuthSDKMD', :git => 'ssh://git@192.168.13.252:8022/cjx/atauthsdkmd.git'
  pod 'TapTapShareSDK', '~> 4.6.3'
  pod 'XiaoHongShuOpenSDK', '~> 1.2.18'
  pod 'Weibo_SDK', :git => "https://github.com/sinaweibosdk/weibo_ios_sdk.git"
  pod 'BDASignalSDK'
  pod 'RiskPerceptionModo', :git => "ssh://git@192.168.13.252:8022/modo_ios_public/riskperceptionmodo.git"
  pod 'TencentGDTActionSDKMoDo', :git => "ssh://git@192.168.13.252:8022/modo_ios_public/TencentGDTActionSDKMoDo.git"
end

def app_only_pods
  pod 'TXLiteAVSDK_TRTC', '11.4.14571'
end

abstract_target 'SharedPods' do
  shared_pods

  target 'HuaYuann' do
    config = use_native_modules!
    use_react_native!(:path => config["reactNativePath"], :hermes_enabled => true)
    app_only_pods
  end

  target 'NativeBility' do
    project NATIVE_BILITY_PROJECT
    config = use_native_modules!
    use_react_native!(:path => config["reactNativePath"], :hermes_enabled => true)
  end
end

# 强制必须为 dynamic 的 pod（如 ActivitySDK）
pre_install do |installer|
  installer.pod_targets.each do |pod|
    if pod.name == 'ActivitySDK'
      puts "👉 [pre_install] 强制将 #{pod.name} 设置为动态 framework"
      def pod.build_type
        Pod::BuildType.dynamic_framework
      end
    end
  end
end

# 灵动岛相关
target 'HuaYuanLAExtension' do
  pod  'ActivityUI',:git => "ssh://git@192.168.13.252:8022/modo_ios/activityui.git"
#  pod  'ActivityUI', :path => '/Users/lin/Desktop/gameProject/LiveActivity/ActivityUI' #本地
end

# Disable signing for pods
post_install do |installer|
  installer.generated_projects.each do |project|
    project.targets.each do |target|
      puts "Target Name: #{target.name}"
        target.build_configurations.each do |config|
          config.build_settings['CODE_SIGN_IDENTITY'] = ''
         end
    end
  end
  
  installer.pods_project.targets.each do |t|
    t.build_configurations.each do |config|
      if config.name == 'Release'
        config.build_settings['DEBUG_INFORMATION_FORMAT'] = 'dwarf-with-dsym'
        config.build_settings['GCC_GENERATE_DEBUGGING_SYMBOLS'] = 'YES'
      end
    end
  end
  
#  installer.pods_project.targets.each do |target|
#    puts "Target Name: #{target.name}"
#    if target.name == 'Pod-NativeBility'
#      target.build_configurations.each do |config|
#        # 添加header search path
#        config.build_settings['HEADER_SEARCH_PATHS'] ||= ['${PODS_ROOT}/../NativeBilityDemo/egret-libs/include']
#        # 添加library search path
#        config.build_settings['LIBRARY_SEARCH_PATHS'] ||= ['${PODS_ROOT}/../NativeBilityDemo/egret-libs']
#      end
#    end
#  end
  
end




```



星期三

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

