# Playbooks

个人可复用经验仓库——跨项目的「做事方法」沉淀（发版策略、打包、架构、排障、工作流等）。从项目 lessons 沉淀后通用化，供任何项目/任何 git 平台复用。

> 这些 playbook 是**通用原则**，不绑特定工具（GitHub Actions / GitLab CI / 自建流水线均可套用）；具体工具写法以「注」的形式给出，不作为主规则。

## 目录

| 目录 | 内容 |
|:---|:---|
| [release/](release/) | 发版 / 版本管理 / CI-CD 发布 |
| [packaging/](packaging/) | 打包 / 跨平台分发 |
| [architecture/](architecture/) | 架构决策 / 模块设计 |
| [debugging/](debugging/) | 排障经验 / 疑难问题 |
| [workflow/](workflow/) | 工作流 / 流程方法 |
| [templates/](templates/) | 可复用模板 |

## Playbook 索引

### release/
- [semver-strategy.md](release/semver-strategy.md) — semver 三档版本策略（幅度不是通道 + 版本线维护独立于档位）
- [version-line-maintenance.md](release/version-line-maintenance.md) — 旧版本线维护（backport / EOL 决策独立）

### packaging/
- [electron-crossplatform.md](packaging/electron-crossplatform.md) — Electron 三端打包（捆绑运行时 + 更新源一致性 + 平台 target）

## 如何贡献

- 项目踩坑经验通用化后 → 按领域放对应目录
- 每个 playbook：背景 / 决策或坑 / 通用原则 / 适用范围 / 来源
- 命名用主题词（semver-strategy.md），不用日期——面向检索不面向历史
- 来源字段保留原始项目路径，便于追溯
