# macOS 应用签名与自更新：自签名证书 + CI 流水线

macOS 桌面应用 + Squirrel 类自动更新的签名打通经验。适用于无 Apple 开发者账号（自签名证书）场景下的 CI 签名与自动更新链路。

## 背景

macOS 自动更新安装器（Squirrel 类）以「运行中应用的代码签名」为信任基准执行替换安装；CI 是无状态环境，签名材料只能以 secret 形式临时注入。签名问题特殊在：**每一环断了都不报同一处错**（构建静默成功、装更新时才报错、报错文本不指向真因），极易反复返工。

## 决策或坑

1. **签名静默跳过**——CI 无证书、或身份名解析不到时，打包器只告警不失败，照常发布无签名包。更隐蔽的变体：配置里显式写死身份名会遮蔽环境变量注入的证书（名字对不上即解析失败）。
2. **无签名版本永远无法自更新**——更新器装更新前先验「运行中应用」的签名，无签名直接拒装。先有鸡先有蛋：存量已是无签名版，后续修好签名也救不了存量，只能手动重装。
3. **ad-hoc 签名是半吊子**——能过「运行中已签」检查，但其 designated requirement 钉死当次构建的内容哈希，跨版本签名比对必失败——升级链在第二环断。
4. **自签名证书「导入≠受信」**——p12 导入只落证书+私钥，不带信任；按有效身份过滤的检索结果为 0（报 CSSMERR_TP_NOT_TRUSTED），必须显式加入受信根（信任域）。
5. **旧加密 p12 与新 OpenSSL 不兼容**——传统工具导出的 p12 常用 RC2-40-CBC 旧 PBE 加密，OpenSSL 3.x 默认 provider 拒解。证书已入钥匙串时直接用系统钥匙串工具导出，绕开 p12 内容解析。
6. **钥匙串自动锁 + 身份检索路径**——新建钥匙串默认几分钟自动锁，长构建后半程签名时钥匙串已锁 → 搜身份返回空报 no identity found；且按名字解析身份走的是钥匙串 search list，仅靠命令行参数指定钥匙串不等价。
7. **签名身份切换是单向门**——无签名/ad-hoc/证书 A 的存量用户，无法自更新到证书 B 签的版本（DR 指纹不可移植），全量手动重装。**证书即升级生命线**。

## 通用原则

1. **构建成功 ≠ 签名成功**：CI 发布链必须有产物级签名门禁（「应签未签」= 构建失败），禁止静默跳签路径存在。
2. **自更新成立的完整条件** = 运行中应用有签名 + 新旧版本签名身份兼容（同一证书 = 同一 DR）。签名方案在首次发布前定案，发布后视为不可逆。
3. **证书是升级生命线**：签发证书丢失或更换 = 全量用户手动重装。证书文件 + 密码 + 注入方式必须异机备份并登记。
4. **无证书 CI 签名流水线五步**：解码 secret → 建临时钥匙串（防自动锁 + 入 search list）→ 导入 → 显式受信 → 断言存在有效身份。证书导出用系统钥匙串工具（天然兼容旧加密），不依赖通用加密库解析 p12。
5. **ad-hoc 只适合「本地能跑」，不适合「线上能更」**：要自动更新就用真证书（自签名或付费 Developer ID），不要用无身份签名过渡。
6. **排障按报错分层定位**：NOT_TRUSTED = 受信缺失；no identity found = 钥匙串锁或 search list；静默跳签 = 门禁缺失；could not get code signature for running application = 存量版本无解（手动重装）。

> **注（electron-builder + GitHub Actions 写法）**：
> - 门禁：`mac.forceCodeSigning: true`（应签未签=构建失败）；**不要**显式写 `identity`（会遮蔽 `CSC_LINK` 注入的证书，且显式路径不走门禁）
> - 五步流水线（mac 腿专用 step）：`security create-keychain` + `set-keychain-settings -lut 21600`（防自动锁）+ `list-keychain -d user -s`（入 search list）→ `security import`（`-T /usr/bin/codesign` + `set-key-partition-list`）→ `security find-certificate -p` 导出证书 + `sudo security add-trusted-cert -d -r trustRoot`（显式受信）→ `security find-identity -v -p codesigning` 断言 1 valid
> - 用 `CSC_KEYCHAIN` 指向准备好的钥匙串（打包器跳过自建临时钥匙串——其导入不带信任）；产物级门禁：`codesign --verify --deep --strict`
> - 通用写法：任何「secret 注入签名材料 + 临时信任锚 + 产物验证」的流水线都遵循同一骨架，不绑 electron-builder

## 适用范围

macOS 桌面应用 + Squirrel 类自更新。无 Apple 开发者账号（自签名）场景全量适用；Developer ID 付费场景部分适用（静默跳签门禁、单向门、证书生命线原则通用，无需自建受信）。Windows/Linux 不直接适用。

## 来源

出生：dsh-hull-desktop v0.1.4~v0.1.6 mac 签名/自更新排障（2026-08-29~30，四轮 CI 实测：静默跳签 → NOT_TRUSTED → RC2 拒解 → no identity found）。通用化：2026-08-30 迁入本仓库。
