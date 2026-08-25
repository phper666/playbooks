---
name: phper666-playbook-release
metadata.source: https://github.com/phper666/playbooks
description: 发版版本策略 playbook。当用户做版本管理 / 发版 / semver 三档 / 版本线维护 / CI-CD 发布 / 旧版本线 backport 时使用，提供跨项目可复用的通用原则（不绑特定 CI 平台：GitHub Actions / GitLab CI / 自建流水线均可套用）。也用于排查版本号语义混乱、并发发布竞态、多端版本漂移、自动更新旧线失效等问题。
---

# 发版版本策略 playbook

## 何时用
- 设计/落地发布流程（patch/minor/major 怎么用、版本线怎么维护、分支模型怎么走）
- 排查发版问题（三档语义混乱、并发发布 tag 冲突、多端版本漂移、自动更新旧线失效）

## 核心规则（详见 release/semver-strategy.md）
1. semver 三档 = 幅度不是通道：patch/minor/major 共用同一 version 字段、都从主分支发，每次只走一步
2. 版本线维护独立于档位：EOL 独立决策；旧线从旧 tag 拉 release/x.y 只发 patch，cherry-pick 回主分支
3. 发版分支自持版本：流水线支持指定目标分支，bump 读所选分支版本文件
4. 并发锁按版本线分组：同线串行异线并行
5. 多端并行同一分支，防版本漂移
6. 自动更新旧线 patch 只服务锁版用户

## 读文件
- 完整规则：`release/semver-strategy.md`（本目录相对路径）

## 用法
- 用户问发版策略/版本管理 → 加载本 skill，读 release/semver-strategy.md，按通用原则回答（不绑具体 CI 平台，按需适配）
- 具体平台写法（GitHub Actions / GitLab CI）在 playbook 的「注」里，作为参考非主规则
