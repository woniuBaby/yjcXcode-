RiskPerception风控网易

官网:https://dun.163.com/dashboard#/m/risk-manage/service/?pid=YD00871651425494

文档:https://support.dun.163.com/documents/761315885761396736?docId=765722596493144064

Xcode pod:    pod 'RiskPerceptionModo',:git =>"ssh://git@192.168.13.252:8022/modo_ios_public/riskperceptionmodo.git"#易盾风控

```
官网账号：dev@babigame.cn
官网密码：Modo2025

关键参数如下
iOS productId     YD00871651425494
iOS businessId    29f95b0fa48f9e3cae5bdf3ba087f109
```

> ### 需求

```
①请登录易盾官网（https://dun.163.com/dashboard?v=0116&locale=zh-CN#/m/overview/）,智能风控-服务管理-开通的服务-点击查看产品密钥，获取秘钥信息；
②在服务管理页获取产品编号和业务id（产品编号、businessID），注意不在秘钥凭证中；
③接入详情请见：https://support.dun.163.com/documents/761315885761396736?docId=778684330614894592接入教程和后端接入-智能风控数据服务接口-在线检测接口https://support.dun.163.com/documents/761315885761396736?docId=834511620372037632；
④从官网下载sdk，下载位置https://dun.163.com/dashboard?v=0116&locale=zh-CN#/m/overview/，智能风控-服务管理-sdk版本，建议下载最新版
⑤返回结果中，建议保存taskid字段，以便后续沟通反馈时作为数据查询的标识。
⑥上线前建议将易盾隐私协议合并至app隐私协议中https://support.dun.163.com/documents/761315885761396736?docId=875474991229304832
```

> ### 主要方法

```objc
//易盾SDK 初始化
-(void)initHTP:(NSDictionary *)configDict;
//设置用户信息接口
- (void)setRoleInfo:(NSDictionary *)configDict;
```



> 1. SDK初始化

```objective-c
//OtherThirdSDKDebugVC 调试页面
NSMutableDictionary *dic = [NSMutableDictionary dictionaryWithDictionary:self.infoData];
      Callback *back = [[Callback alloc] init];
      back.onHandler = ^(Msg *err, NSObject *result) {
          MDDebugLogDebug(@"result ---- %@ \n err ----- %@",result.mj_JSONObject,err.mj_JSONObject);
      };
      [[PluginMgr sharedInstance] runPluginWithName:@"secure" adapterName:@"yidun" apiname:@"setRoleInfo" options:dic callback:back];  
```

`具体实现`

```objc
//PluginAdapter_riskperception
/*
 必填) String productId   //易盾HTP分配的productId
 可选) int serverType   //服务器归属地 1 中国大陆（默认） 2 中国台湾 3 海外
 可选) String channel   //需要传递当前应用包体来源渠道可调用该接口（比如：应用宝渠道编号/信息）
 可选) ArrayList<String key,String value> extraData //额外信息给易盾-key -value
 可选) String gameKey (主要跟安全通信的接口相关联，用于游戏类型应用，非游戏类型应用或不需要可以忽略，可以自定义32位长度字符串。主要用于以下接口：数据校验、存档加解密 ）
 可选) String host   //设置私有化地址
 */
-(void)initHTP:(NSDictionary *)configDict;
```

注意:⚠️⚠️⚠️⚠️⚠️ host 如果无效也会报错,config 配置错误

`获取字典数据 并config`

```objc
//可选) ArrayList<String key,String value> extraData //额外信息给易盾-key -value
//ArrayList<String key,String value> extraData //额外信息给易盾-key -value
 
    NSArray *arrayList = dataDict[@"extraData"];
    if ([arrayList isKindOfClass:[NSArray class]] && arrayList.count > 0) {
        for (NSDictionary *item in arrayList) {
            if (![item isKindOfClass:[NSDictionary class]]) continue;
            // 遍历字典所有键值对
            [item enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
               NSString *keyStr = [key isKindOfClass:[NSString class]] ? key : [key description];
               NSString *valueStr = [obj isKindOfClass:[NSString class]] ? obj : [obj description];
                if (keyStr.length > 0 && valueStr.length > 0) {
                    [NTESRiskUniConfiguration setExtraData:keyStr forValue:valueStr];
                }
            }];
        }
    }

```



> 2. 设置用户信息

```objc
//OtherThirdSDKDebugVC 调试页面
 [[PluginMgr sharedInstance] runPluginWithName:@"secure" adapterName:@"yidun" apiname:@"setRoleInfo" options:dic callback:back];
```

`具体实现`

```objc
//PluginAdapter_riskperception
/*
必填 String businessId    当前业务 ID        
必填 String roleId             用户/玩家的角色 ID；非游戏类型应用，roleId 可以与 roleAccount 相同    
可选 String roleName       用户/玩家的角色名称；非游戏类型应用，roleName 可以是当前用户昵称相同 
必填 String roleAccount   用户/玩家的账号,如业务方同时接入易盾反垃圾，则此账号需要与反垃圾接入中的account一致 
可选 String roleServer       用户/玩家的角色的服务器名称 
可选 String serverId           用户/玩家的角色所属服务器的 ID
可选 String gameVersion   游戏版本号
可选 String  assetVersion   游戏类型应用的资源文件版本号
 */
- (void)setRoleInfo:(NSDictionary *)configDict;
```

----

> ## 易盾加固

`加固 :对于游戏引擎中的核心代码逻辑混淆保护，增大攻击者去逆向分析的难度，支持自定义配置。`

文档:https://support.dun.163.com/documents/2017121301?docId=989340186489274368

下载工具: -->里面会有 NHPProtect.jar  需要安装java(https://www.java.com/zh-CN/  下载mac)  配置java环境

```objc
//检查是否安装成功
lin@lindeMac-mini ~ % java -version
java version "25.0.1" 2025-10-21 LTS
Java(TM) SE Runtime Environment (build 25.0.1+8-LTS-27)
Java HotSpot(TM) 64-Bit Server VM (build 25.0.1+8-LTS-27, mixed mode, sharing)
```

## 

注意：加固项由易盾后台进行配置，请联系易盾技术支持人员。

命令行模式（注意，第一次使用需要配置config.ini文件）

1. 命令行模式参数

```objc
java -jar NHPProtect.jar -iOS -nobitcode -yunconfig -input /Users/ios_app.xcarchive

//eg: 分别加固不同类型 (archive / ipa包)
  //1. archive打包 .xcarchive (windows-->organizer -->包)
java -jar NHPProtect.jar -iOS -nobitcode -yunconfig -input /Users/lin/Library/Developer/Xcode/Archives/2025-12-06/HuaYuann\ 2025-12-6\,\ 10.05.xcarchive
  
  //2. ipa包
  java -jar NHPProtect.jar -iOS -nobitcode -yunconfig -input 
 /Users/lin/Desktop/HuaYuann\ 2025-12-09\ 13-35-33/HuaYuann.ipa 
  /*
  注意事项：若加固对象是.ipa包，请在 config 内配置对应工程的符号表路径。
  [SymbolPath]
	path=
  */
```

1. 参数说明如下：

| 参数      | 说明                                                         |
| :-------- | :----------------------------------------------------------- |
| yunconfig | 必填项，表示自动从易盾后台获取加固参数,加固配置已经在后台部署，可以根据实际情况调整 |
| - iOS     | 必填项，标记iOS加固平台                                      |
| nobitcode | 必填项                                                       |
| -input    | 必填项，参数后面跟待加固的文件的绝对路径                     |
| -output   | 默认加固后的文件输出在原文件同路径，-output参数可以指定加固后文件的输出路径和文件名，java -jar NHPProtect.jar -nobitcode -iOS -yunconfig -input /Users/ios_app.xcarchive -output /Users/Projectsxcarchive_encrypted.zip |

### **config.ini文件配置说明**

默认config.ini必须要跟NHPProtect.jar在同一目录下，如果需要指定config文件，可以执行-config 命令参数来处理：

```
java -jar NHPProtect.jar -nobitcode -iOS -yunconfig -config E:\\Desktop\\test\\config.ini -input /path/path/test.xcarchive
```



注意:⚠️⚠️⚠️⚠️⚠️⚠️ 2种加固方式



---

> 1. archive包 加固方式

```objc
//可直接选择路径-->包内容找到对应xcarchive,将绝对路径添加到 java的 -input
ios_app.xcarchive   open ~/Library/Developer/Xcode/Archives/   
```



> 2. ipa 包加固后需要重新签名

加固易盾SDK,ipa打包,无法安装,报错 无法验证APP 完整性,需要重新签名

加固改造，破坏了签名，生成了一个**签名失效的 `.ipa` ** 给你。

脚本需要填写2个参数:

```objective-c
# ==================== 配置区域 ====================
# 请填写以下两个路径
ORIGINAL_IPA="/Users/lin/Desktop/HuaYuann/HuaYuann.ipa"      # 【加固前】的原始ipa路径
ENCRYPTED_IPA="/Users/lin/Desktop/HuaYuann/HuaYuann_encrypted.ipa"    # 【加固后】待重签的ipa路径
# ==================== 配置结束 ====================
```



shell:需要特别注意  原始签名证书:  推断原始证书  可对比(iPhone 还是apple 选择问题),我选的iphone,因为推断

`终端 cd 对应的脚本文件夹(脚本名称resign.sh),    ./resign.sh`

```
#!/bin/bash

# ==================== 配置区域 ====================
# 请填写以下两个路径
ORIGINAL_IPA="/Users/lin/Desktop/HuaYuann/HuaYuann.ipa"      # 【加固前】的原始ipa路径
ENCRYPTED_IPA="/Users/lin/Desktop/HuaYuann/HuaYuann_encrypted.ipa"    # 【加固后】待重签的ipa路径
# ==================== 配置结束 ====================

set -e
echo "🚀 开始智能重签名流程..."
echo "   原始包（用于提取配置）: $(basename "$ORIGINAL_IPA")"
echo "   加固包（待处理）: $(basename "$ENCRYPTED_IPA")"
echo ""

# 1. 基础检查
if [ ! -f "$ORIGINAL_IPA" ]; then echo "❌ 错误：找不到原始ipa文件"; exit 1; fi
if [ ! -f "$ENCRYPTED_IPA" ]; then echo "❌ 错误：找不到加固ipa文件"; exit 1; fi

# 2. 阶段一：从原始包提取配置
echo "🔍 [阶段1] 正在分析原始包，提取签名配置..."
TEMP_EXTRACT_DIR=$(mktemp -d)
unzip -q "$ORIGINAL_IPA" -d "$TEMP_EXTRACT_DIR" 2>/dev/null || { echo "解压原始包失败"; exit 1; }

ORIGINAL_APP=$(find "$TEMP_EXTRACT_DIR" -name "*.app" -type d | head -1)
if [ -z "$ORIGINAL_APP" ]; then echo "在原始包中未找到.app"; exit 1; fi

# 2.1 检查并获取原始描述文件
ORIGINAL_PROFILE="$ORIGINAL_APP/embedded.mobileprovision"
if [ ! -f "$ORIGINAL_PROFILE" ]; then
    echo "❌ 原始包中缺少 'embedded.mobileprovision' 文件，无法获取配置。"
    echo "   请确认这是一个有效的、未经过破坏的原始开发包。"
    rm -rf "$TEMP_EXTRACT_DIR"
    exit 1
fi

# 2.2 提取关键信息
echo "📄 提取描述文件与权限..."
# 提取Entitlements
security cms -D -i "$ORIGINAL_PROFILE" > "$TEMP_EXTRACT_DIR/profile.plist"
/usr/libexec/PlistBuddy -x -c 'Print :Entitlements' "$TEMP_EXTRACT_DIR/profile.plist" > "$TEMP_EXTRACT_DIR/original.entitlements"

# 提取Bundle ID (从描述文件和App自身，以描述文件为准)
EMBEDDED_APP_ID=$(/usr/libexec/PlistBuddy -c 'Print :Entitlements:application-identifier' "$TEMP_EXTRACT_DIR/original.entitlements" 2>/dev/null || echo "")
BUNDLE_ID="${EMBEDDED_APP_ID#*.}" # 去除TeamID前缀，得到纯Bundle ID
if [ -n "$BUNDLE_ID" ]; then
    echo "   Bundle ID 识别为: $BUNDLE_ID"
else
    # 如果从权限文件获取失败，则尝试从Info.plist获取
    BUNDLE_ID=$(/usr/libexec/PlistBuddy -c "Print :CFBundleIdentifier" "$ORIGINAL_APP/Info.plist" 2>/dev/null || echo "")
    echo "   Bundle ID (从Info.plist): $BUNDLE_ID"
fi

# 2.3 推断原始证书
echo "🔑 推断原始签名证书..."
INFERRED_CERT=$(/usr/libexec/PlistBuddy -c 'Print :DeveloperCertificates:0' "$TEMP_EXTRACT_DIR/profile.plist" 2>/dev/null | openssl x509 -inform DER -noout -subject 2>/dev/null | sed -n 's/.*CN=\([^/]*\).*/\1/p')
if [ -n "$INFERRED_CERT" ]; then
    echo "   证书线索: $INFERRED_CERT"
    # 检查钥匙串中是否存在此证书
    if security find-identity -v -p codesigning | grep -q "$INFERRED_CERT"; then
        echo "   ✅ 在钥匙串中找到匹配证书。"
        SIGNING_CERT="$INFERRED_CERT"
    else
        echo "   ⚠️  钥匙串中未找到精确匹配的证书。"
        SIGNING_CERT=""
    fi
else
    echo "   ⚠️  无法从描述文件推断证书。"
    SIGNING_CERT=""
fi

# 3. 阶段二：处理加固包
echo ""
echo "🔧 [阶段2] 开始处理加固包..."
WORK_DIR=$(mktemp -d)
unzip -q "$ENCRYPTED_IPA" -d "$WORK_DIR" 2>/dev/null || { echo "解压加固包失败"; exit 1; }

ENCRYPTED_APP=$(find "$WORK_DIR" -name "*.app" -type d | head -1)
if [ -z "$ENCRYPTED_APP" ]; then echo "在加固包中未找到.app"; exit 1; fi
echo "   找到应用包: $(basename "$ENCRYPTED_APP")"

# 3.1 将原始描述文件复制到加固包中
echo "🔄 注入原始描述文件..."
cp "$ORIGINAL_PROFILE" "$ENCRYPTED_APP/embedded.mobileprovision"

# 3.2 （可选但推荐）强制统一Bundle ID
if [ -n "$BUNDLE_ID" ]; then
    CURRENT_ID=$(/usr/libexec/PlistBuddy -c "Print :CFBundleIdentifier" "$ENCRYPTED_APP/Info.plist" 2>/dev/null || echo "")
    if [ "$CURRENT_ID" != "$BUNDLE_ID" ]; then
        echo "   📝 修正Bundle ID: $CURRENT_ID -> $BUNDLE_ID"
        /usr/libexec/PlistBuddy -c "Set :CFBundleIdentifier $BUNDLE_ID" "$ENCRYPTED_APP/Info.plist" 2>/dev/null && echo "   修改成功。"
    fi
fi

# 3.3 确定签名证书
if [ -z "$SIGNING_CERT" ]; then
    echo ""
    echo "📋 请从以下可用的代码签名证书中选择:"
    security find-identity -v -p codesigning | grep -v "CSSMERR_TP_CERT_REVOKED" | head -10
    echo ""
    read -p "请输入要使用的证书完整名称（引号内的部分）: " USER_CERT
    SIGNING_CERT="$USER_CERT"
fi
echo "   将使用证书: $SIGNING_CERT"

# 3.4 清理旧签名并开始签名
echo "🧹 清理旧签名..."
rm -rf "$ENCRYPTED_APP/_CodeSignature" "$ENCRYPTED_APP/CodeResources" 2>/dev/null || true

echo "⚙️  开始代码签名..."
# 先签嵌套组件（.framework, .appex）
find "$ENCRYPTED_APP" \( -name "*.framework" -o -name "*.appex" \) -type d | while read -r component; do
    echo "   -> 签名组件: $(basename "$component")"
    /usr/bin/codesign --force --sign "$SIGNING_CERT" --entitlements "$TEMP_EXTRACT_DIR/original.entitlements" --timestamp=none "$component" 2>/dev/null || { echo "组件签名失败"; exit 1; }
done

# 最后签主应用
echo "   -> 签名主应用..."
/usr/bin/codesign --force --sign "$SIGNING_CERT" --entitlements "$TEMP_EXTRACT_DIR/original.entitlements" --timestamp=none "$ENCRYPTED_APP" 2>/dev/null || { echo "主应用签名失败"; exit 1; }

echo "✅ 验证签名..."
VERIFY_OUTPUT=$(/usr/bin/codesign -vv "$ENCRYPTED_APP" 2>&1)
if echo "$VERIFY_OUTPUT" | grep -q "valid on disk"; then
    echo "   ✅ 签名验证通过！"
else
    echo "   ❌ 签名验证失败："
    echo "$VERIFY_OUTPUT"
    exit 1
fi

# 4. 打包输出
echo "📦 打包生成最终的ipa文件..."
cd "$WORK_DIR"
OUTPUT_NAME="$(basename "$ENCRYPTED_IPA" .ipa)_resigned.ipa"
OUTPUT_PATH="$(dirname "$ENCRYPTED_IPA")/$OUTPUT_NAME"
zip -qr "$OUTPUT_PATH" Payload/

# 5. 清理与完成
cd - > /dev/null
rm -rf "$TEMP_EXTRACT_DIR" "$WORK_DIR"

echo ""
echo "=========================================="
echo "🎉 智能重签名全部完成！"
echo "➡️  最终可安装的ipa文件："
echo "   $OUTPUT_PATH"
echo "=========================================="

```





---

补充

> 加固

| 作用                 | 说明                                                         | 类比                                               |
| :------------------- | :----------------------------------------------------------- | :------------------------------------------------- |
| **1. 代码混淆**      | 将代码中的类名、方法名等替换成无意义的字符，增加逆向分析和破解的难度。 | 把房子的房间号、门牌号打乱，让窃贼找不到目标。     |
| **2. 加密保护**      | 对核心业务逻辑代码或字符串进行加密，运行时再动态解密，防止静态分析。 | 把保险箱里的重要文件用密码锁起来。                 |
| **3. 反调试/反注入** | 植入检测代码，防止黑客使用调试器动态跟踪或注入恶意代码。     | 安装反侦察设备，一旦有人试图撬锁就会报警。         |
| **4. 完整性校验**    | 检查应用自身是否被篡改。                                     | 在房子里布置激光网格，一旦物品被移动就会触发警报。 |

