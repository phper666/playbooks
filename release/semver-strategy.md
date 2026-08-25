# 发版版本策略：semver 三档 + 版本线维护

跨项目可复用的发版版本策略。核心：semver 三档是「幅度」不是「通道」；版本线维护独立于档位。适用于任何 git + 版本号（semver）+ 自动更新的项目。

## 背景

设计发布流程时对「patch/minor/major 三档怎么用」「旧版本线怎么维护」「分支模型怎么走」反复拉扯，最终定稿一套可复用策略。本 playbook 是**通用原则**（不绑特定 CI 平台），平台写法以「注」给出。

## 决策或坑

1. **patch/minor/major 不是三条并行发布线，是同一个 version 字段的三种递增幅度**——每次发布只走一步，共用主分支（main/master），不存在三档写 version 冲突。
2. **自定义语义（贴合单人/小团队）**：patch=小改（bug+小功能随时发）、minor=攒批发布（累计多版本功能发一次，常规节奏非稀有）、major=破坏性重构（页面/架构重构，低频稀有）。有外部 API 消费者时需谨慎自定义。
3. **版本线维护（backport）独立于档位**：「放弃维护旧版本」是独立 EOL 决策，不绑 minor。主分支走远 + 有用户锁旧版 → 从旧 tag 拉 `release/x.y` 只发 patch，修复 cherry-pick 回主分支。
4. **发版分支自行维护版本和 tag**：流水线加 `branch` 输入（默认主分支），维护线发布选 `release/x.y`，bump 读**所选分支**的版本文件，tag 打所选分支 HEAD。
5. **并发锁按版本线分组**：同线串行、异线并行（主分支发 minor 与 release/0.1.x 发 patch 可同时跑）。
6. **多端并行必须同分支**：多端并行各自 checkout 同一 branch，否则 mac 发 0.1.1、win 发 0.1.2 乱套。
7. **自动更新项目旧线 patch 只对锁版用户有效**：自动更新机制通常指向最新版本，主分支已发 0.2.0 时旧线 0.1.1 不会推给自动更新用户。

> **注（GitHub Actions 写法）**：
> - 并发锁：`concurrency.group: release-${{ inputs.branch }}`（同线串行、异线并行）
> - 手动触发：`workflow_dispatch` + `inputs.branch`
> - **注（GitLab CI 写法）**：手动触发用 `rules: if: $CI_PIPELINE_SOURCE == "web"` + `inputs`（CI/CD 变量）；并发控制用 `resource_group`（按分支分组）或 `needs` 串行
> - **注（通用写法）**：任何 CI 都应有「指定目标分支」输入 + 「按分支分组」的并发控制，这是原则不是工具特性

## 通用原则

1. **semver 三档 = 幅度不是通道**：patch/minor/major 共用同一 version 字段、都从主分支发，每次只走一步。自定义语义要文档写清。
2. **版本线维护独立于档位**：EOL 是独立决策不绑 minor；主分支走远 + 用户锁旧版 → 从旧 tag 拉 `release/x.y`，只发 patch，cherry-pick 回主分支。其他分支只做 bug 修复，永不发 minor/major。
3. **发版分支自持版本**：流水线支持指定目标分支（默认主分支），bump 读所选分支版本文件，tag 打所选分支 HEAD——发版分支自行维护版本和 tag。
4. **并发锁按版本线分组**：同线串行异线并行（`concurrency.group` / `resource_group` / 等价机制）。
5. **多端必须同分支**：多端并行 checkout 同一 branch，防止版本漂移。
6. **自动更新项目的旧线 patch 只服务锁版用户**：自动更新指向最新，旧线 patch 不推给自动更新用户。

## 适用范围

任何 git + 版本号（semver）+ 自动更新的桌面应用/服务发布。尤其单主分支起步、将来可能要维护旧版本线的项目。不适用无版本号/无多端发布的项目。

## 来源

出生：dsh-hull-desktop cicd 需求（2026-08-25）。通用化：2026-08-25 迁入本仓库。
