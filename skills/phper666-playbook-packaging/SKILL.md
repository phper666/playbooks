---
name: phper666-playbook-packaging
metadata.source: https://github.com/phper666/playbooks
description: Electron 三端打包 playbook。当用户做桌面应用打包 / 跨平台分发 / Electron 壳 / 捆绑运行时 / 自动更新配置 / 多平台 target 时使用，提供可复用通用原则（不绑特定打包工具）。也用于排查更新源配置不一致静默失败、平台 target 缺失无法更新、交叉编译失败等问题。
---

# Electron 三端打包 playbook

## 何时用
- Electron 桌面壳三端（macOS/Windows/Linux）打包
- 排查打包问题（更新源查错静默失败、平台 target 缺失、交叉编译、未签名警告）

## 核心规则（详见 packaging/electron-crossplatform.md）
1. 三端捆绑运行时：per-platform 映射（工具 ${os} 与供应商命名差异）+ 构建脚本参数化平台
2. 更新源配置一致性：更新器与打包器 publish 配置严格一致，写错静默失败难排查
3. 三平台更新 target：mac 必须含 zip、win nsis、linux AppImage
4. Win/Linux 打包需对应平台环境（不能交叉），单平台先行 + 实测
5. 未签名打包会触发平台安全警告，正式分发需签名

## 读文件
- 完整规则：`packaging/electron-crossplatform.md`（本目录相对路径）

## 用法
- 用户问打包/跨平台/electron 分发 → 加载本 skill，读 packaging/electron-crossplatform.md，按通用原则回答
- 工具写法（electron-builder）在 playbook 的「注」里，作为参考非主规则
