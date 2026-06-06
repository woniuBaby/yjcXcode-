# iOS 开发证书与描述文件 — 协作说明

> 适用：同事 A 本地开发 / 真机调试 / Xcode Archive / HBuilderX 自定义基座  
> 同事 B 持有 Apple Developer 团队权限

---

## 0. 一句话速查

| 组件 | 主要作用 | 你可以把它想象成… |
|------|----------|-------------------|
| **开发证书** | 证明开发者身份，允许在真机上安装、运行和测试 App | 开发者的**个人身份证** |
| **描述文件** | 把证书、App ID、设备、权限绑定在一起 | **数字旅行团套餐**（导游 + 团 + 景点） |
| **P12 文件** | 安全传输「证书 + 私钥」，可在另一台 Mac 上使用同一开发身份 | 身份证的**安全复印件**（含签名权，需妥善保管） |

---

## 1. 先搞懂：到底需要哪些东西？

iOS 真机安装、调试、Archive，签名需要 **两样核心文件**：

| 文件 | 扩展名 | 作用 | 能否单独使用 |
|------|--------|------|--------------|
| **开发证书（含私钥）** | `.p12` 或 `.cer` + 本机私钥 | 证明「谁有权给 App 签名」 | ❌ 单独不够 |
| **描述文件** | `.mobileprovision` | 绑定 App ID、证书、设备、权限 | ❌ 单独不够 |

两者 **必须匹配**，且同事 A 的手机 **UDID 必须在描述文件里**，才能真机调试。

---

## 2. 两种协作方式对比

### 方式一：CSR 流程（标准、推荐）

**特点：** 私钥始终留在 A 的 Mac 上，更安全。

```
同事 A                          同事 B（有开发者权限）
   │                                    │
   ├─ 1. 钥匙串生成 CSR (.certSigningRequest)
   │                                    │
   └─ 2. 把 CSR 发给 B ────────────────► 3. Apple 后台上传 CSR，创建 Development 证书
                                        4. 下载 .cer，发回给 A
   │
   ├─ 5. 双击 .cer 导入钥匙串（自动与 A 本机私钥配对）
   │
   └─ 6. 导入/下载 Development 描述文件 (.mobileprovision)
        → 可在 Xcode 真机调试
```

**B 发给 A 的文件：**

- `.cer`（开发证书公钥部分）
- `.mobileprovision`（描述文件，且含 A 设备 UDID）

**A 本机还需要有：** CSR 时生成的 **私钥**（已在钥匙串里，不用单独传）

---

### 方式二：B 直接发「拿来就能用」的包（快捷、适合共用证书）

**特点：** 不依赖 A 是否生成过 CSR；A 换电脑也能用；但私钥由 B 导出，安全风险更高。

```
同事 B（有开发者权限）                同事 A
   │                                    │
   ├─ 1. 在 Apple 后台创建/维护 Development 证书
   ├─ 2. 注册 A 的手机 UDID
   ├─ 3. 生成 Development 描述文件
   ├─ 4. 从钥匙串导出 .p12（设密码）
   │
   └─ 5. 打包发给 A ──────────────────► 6. 导入 .p12 + .mobileprovision
                                         7. 输入 p12 密码
                                         → 可直接签名、Archive、真机调试
```

**B 发给 A 的文件（最小可用包）：**

| 文件 | 说明 |
|------|------|
| `xxx.p12` | 开发证书 + 私钥（B 从钥匙串导出） |
| `xxx.mobileprovision` | Development 描述文件 |
| p12 密码 | 单独告知，不要和文件放同一消息 |

**可选补充：** Team ID、Bundle ID、证书过期时间、描述文件名称

---

### 两种方式怎么选？

| 场景 | 推荐方式 |
|------|----------|
| A 有固定 Mac，长期开发 | **方式一 CSR**（私钥不离开 A） |
| A 换电脑 / 多台 Mac | **方式二 p12** |
| 团队共用一张开发证书 | **方式二 p12** |
| HBuilderX 云打包、CI 机器 | **方式二 p12**（上传到打包平台） |
| 重视安全、人员流动大 | **方式一**，或每人各自证书 + 同一描述文件 |

---

## 3. 专业名词详解

### 3.1 Apple Developer / Team（开发者账号 / 团队）

- **Apple Developer Program**：苹果开发者计划，年费账号，用于上架 App Store、真机调试等。
- **Team**：一个开发者账号下的团队实体，有唯一 **Team ID**（如 `7BBKRAB2HT`）。
- **Account Holder / Admin**：能在后台创建证书、描述文件、添加设备的角色。
- **Developer 成员**：可被邀请加入 Team，用自己的 Apple ID 生成个人开发证书。

---

### 3.2 Bundle ID（包名 / App 标识符）

- 格式如：`cn.lbwdhysj.gf.ios`
- 每个 App 在 Apple 生态中的唯一标识。
- **描述文件里的 Bundle ID 必须与 Xcode / HBuilderX 工程里的一致**，否则签名失败。

---

### 3.3 UDID（设备唯一标识）

- 每台 iPhone/iPad 的唯一 ID。
- **Development 描述文件** 必须包含要安装 App 的设备 UDID。
- 获取方式：Xcode 连接设备 → Window → Devices and Simulators；或 Finder 连接 iPhone 查看。

**流程：** A 把 UDID 发给 B → B 在 Developer 后台 **Devices** 注册 → **重新生成** 描述文件 → 再发给 A。

---

### 3.4 CSR — Certificate Signing Request（证书签名请求）

- 扩展名：`.certSigningRequest`
- **生成位置：** Mac「钥匙串访问」→ 证书助理 → 从证书颁发机构请求证书。
- **内容：** 公钥 + 身份信息（邮箱、名称）；**不含私钥**。
- **作用：** 提交给 Apple，用于签发 `.cer` 开发证书。
- **重要：** 生成 CSR 时，**私钥会保存在本机钥匙串**，不会随 CSR 文件发出。

---

### 3.5 .cer — Certificate（证书，仅公钥部分）

- Apple Developer 后台下载的 **开发证书** 或 **发布证书**。
- **只含公钥**，不含私钥。
- **用法：**
  - 若 CSR 在本机生成 → 双击 `.cer` 导入，会自动与钥匙串里的私钥配对 ✅
  - 若 CSR 不在本机 → 单独 `.cer` **无法用于签名** ❌

**常见类型：**

- **Apple Development / iOS Development**：开发、调试、Archive（Development 导出）
- **Apple Distribution / iOS Distribution**：App Store 上架、Ad Hoc 分发

---

### 3.6 .p12 — PKCS#12 证书包（证书 + 私钥）

- 从「钥匙串访问」导出的 **证书与私钥打包文件**。
- **包含：** 公钥证书 + 私钥（可含证书链）。
- **导出时必须设置密码**，导入时需输入该密码。
- **用途：**
  - 发给同事在其他 Mac 上使用
  - 上传到 HBuilderX 云打包、Jenkins 等 CI
  - 备份证书（需妥善保管）

**安全级别：** ⚠️ 等同于签名私钥，泄露后他人可冒充你的身份签名 App。

**导入：** 双击 `.p12` → 选「登录」钥匙串 → 输入密码。

---

### 3.7 私钥 / 公钥（Private Key / Public Key）

- **私钥：** 签名用，必须保密，通常存在 Mac 钥匙串；`.p12` 里包含私钥。
- **公钥：** 放在 `.cer` 里，可公开；Apple 用 CSR 里的公钥签发证书。
- **配对关系：** 只有「该 CSR 对应的私钥」+「Apple 签发的 .cer」才能正常签名。

---

### 3.8 .mobileprovision — Provisioning Profile（描述文件 / 配置文件）

- Apple 签发的 **配置文件**，告诉 Xcode：这个 App 用什么证书、哪些设备能装、有哪些 Capability（推送、Associated Domains 等）。

**类型：**

| 类型 | 用途 |
|------|------|
| **iOS App Development** | 开发调试、Development Archive |
| **iOS App Store** | 上传 App Store |
| **Ad Hoc** | 指定设备安装（不经过 App Store） |
| **Enterprise** | 企业内部分发（需 Enterprise 账号） |

- **导入：** 双击文件，或放到 `~/Library/MobileDevice/Provisioning Profiles/`。
- **Xcode 也会自动同步**（登录同一 Team 的 Apple ID 时）。

**常见报错：**

- 设备未在描述文件中 → 需 B 加 UDID 并重新生成
- 证书与描述文件不匹配 → 需用描述文件里指定的证书
- 描述文件过期 → 重新下载

---

### 3.9 钥匙串访问（Keychain Access）

- macOS 管理证书、私钥的系统工具。
- **查看证书是否完整：** 展开证书左侧小三角，若能看到 **专用密钥**（私钥），说明证书可用。
- **导出 p12：** 选中证书（含私钥）→ 右键 → 导出 → 选 `.p12` → 设密码。

---

### 3.10 签名 / Code Signing（代码签名）

- Apple 要求所有在真机运行的 App 都必须签名。
- Xcode **Signing & Capabilities** 里配置：
  - **Team**
  - **Bundle Identifier**
  - **Signing Certificate**（Development / Distribution）
  - **Provisioning Profile**（自动或手动）

---

### 3.11 Archive / IPA

- **Archive：** Xcode 菜单 Product → Archive，生成 `.xcarchive`。
- **导出 IPA：** 从 Archive 导出，选择 **Development**（调试基座）或 **App Store**（上架）。
- **自定义基座：** HBuilderX 使用 Development 方式导出的 `HBuilder.ipa` 作为 `iOS_debug.ipa`。

---

## 4. 操作清单

### 4.1 方式一：A 发 CSR，B 回 .cer（推荐）

**同事 A：**

1. 钥匙串访问 → 证书助理 → 从证书颁发机构请求证书 → 存盘得到 `.certSigningRequest`
2. 把 CSR + 自己手机 **UDID** 发给 B
3. 收到 B 的 `.cer` 后双击导入钥匙串
4. 确认证书下有「专用密钥」
5. 收到/下载 `.mobileprovision` 并双击导入
6. Xcode 选择 Team，Bundle ID 与描述文件一致 → 真机运行

**同事 B：**

1. developer.apple.com → Certificates → `+` → Apple Development
2. 上传 A 的 CSR → Download `.cer` → 发给 A
3. Devices → 注册 A 的 UDID
4. Profiles → iOS App Development → 选 App ID + 该证书 + A 的设备 → Download `.mobileprovision` → 发给 A

---

### 4.2 方式二：B 直接发可用包（p12 + 描述文件）

**同事 B：**

1. 确保 Development 证书在钥匙串中且含私钥
2. Devices 注册 A 的 UDID
3. 创建/更新 Development 描述文件（含该证书 + A 设备 + 正确 App ID）
4. 钥匙串导出 `.p12`，设置密码
5. 下载 `.mobileprovision`
6. 将 **p12 + mobileprovision + 密码（单独发）** 发给 A

**同事 A：**

1. 双击 `.p12` 导入，输入密码
2. 双击 `.mobileprovision` 导入
3. Xcode → Signing：Bundle ID 与描述文件一致
4. 真机连接 → Run / Archive

---

## 5. 常见问题

| 现象 | 原因 | 处理 |
|------|------|------|
| 有 .cer 仍无法签名 | 本机没有对应私钥 | 用方式二发 p12，或 A 重新走 CSR 流程 |
| 安装失败 / 设备未注册 | UDID 不在描述文件 | B 加设备后 **重新生成** 描述文件 |
| Provisioning profile doesn't match | Bundle ID 不一致 | 对齐工程与描述文件的 Bundle ID |
| 证书已过期 | 开发证书有效期约 1 年 | B 重新创建证书并更新描述文件 |
| p12 导入后仍无专用密钥 | p12 损坏或密码错误 | 重新导出 p12 |

---

## 6. 安全建议

1. **不要** 把 `.p12` 和密码放在同一封邮件/同一条消息里。
2. **不要** 上传到公开网盘；离职人员及时 **Revoke** 其证书。
3. 能走 **CSR 流程** 尽量走 CSR，减少私钥流转。
4. 团队长期协作：每人各自 Development 证书 + 共用 Development 描述文件（含所有设备）。

---

## 7. 一句话对照

| 说法 | 实际含义 |
|------|----------|
| 「B 直接发个 A 就能用的」 | 通常是 **`.p12` + `.mobileprovision`**，且 A 的 UDID 已在描述文件里 |
| 「A 先申请 CA 请求证书」 | 即生成本机 **CSR**，私钥留 A，B 只回 **`.cer`** |
| 「加密码再返回」 | B 导出 **`.p12`** 时设置的导出密码，A 导入时要输入 |

---

*文档版本：2026-06-06*
