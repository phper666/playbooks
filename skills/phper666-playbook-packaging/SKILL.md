---
name: phper666-playbook-packaging
metadata.source: https://github.com/phper666/playbooks
description: Electron 三端打包 playbook。当用户做桌面应用打包 / 跨平台分发 / Electron 壳 / 捆绑运行时 / 自动更新配置 / 多平台 target 时使用，提供可复用通用原则（不绑特定打包工具）。也用于排查更新源配置不一致静默失败、平台 target 缺失无法更新、交叉编译失败、macOS 签名（自签名证书 / codesign / 钥匙串 / CSSMERR_TP_NOT_TRUSTED / no identity found）与自更新链路（Squirrel / could not get code signature for running application / 差分下载失败进度条假回退 / 更新缓存失配）等问题。
---

# Electron 三端打包 playbook

## 何时用
- 新项目从零接入自更新链路（接入顺序见 runbook）
- Electron 桌面壳三端（macOS/Windows/Linux）打包
- 排查打包问题（更新源查错静默失败、平台 target 缺失、交叉编译、未签名警告、产物名 404）
- macOS 签名与自更新（自签名证书 CI 流水线 / codesign 报错分层排障 / 无签名版本无法自更新 / 差分假回退进度条重来）

## 核心规则（详见 packaging/electron-crossplatform.md、packaging/macos-code-signing.md）
1. 三端捆绑运行时：per-platform 映射（工具 ${os} 与供应商命名差异）+ 构建脚本参数化平台
2. 更新源配置一致性：更新器与打包器 publish 配置严格一致，写错静默失败难排查
3. 三平台更新 target：mac 必须含 zip、win nsis、linux AppImage
4. Win/Linux 打包需对应平台环境（不能交叉），单平台先行 + 实测
5. 未签名打包会触发平台安全警告，正式分发需签名
6. 签名（macOS，详见 macos-code-signing.md）：构建成功≠签名成功，必须有产物级签名门禁；自签名证书 CI 五步（临时钥匙串防自动锁+入 search list → 导入 → 显式受信 → 断言有效身份）；证书即升级生命线，签名身份切换是单向门；差分基缓存对账（下载记版本，启动对账不一致清基）+ 更新器日志必须接入应用日志

## 读文件
- 新项目接入（runbook，从这里开始）：`packaging/auto-update-onboarding.md`（本目录相对路径）
- 完整规则：`packaging/electron-crossplatform.md`（本目录相对路径）
- 签名专题：`packaging/macos-code-signing.md`（本目录相对路径）

## 用法
- 用户问打包/跨平台/electron 分发 → 加载本 skill，读 packaging/electron-crossplatform.md，按通用原则回答
- 用户问 macOS 签名/自签名/codesign/自更新报错 → 读 packaging/macos-code-signing.md，按报错分层定位（NOT_TRUSTED=受信缺失 / no identity found=锁或 search list / 静默跳签=门禁缺失 / running application=存量无解）
- 工具写法（electron-builder、security 命令）在 playbook 的「注」里，作为参考非主规则
