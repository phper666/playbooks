# Playbooks — AI 导航

本仓库是**个人可复用经验库**（跨项目做事方法），给 AI 读/检索用。

## 检索方式

- 用户提到「发版/版本/semver/CI-CD 发布」→ `release/`
- 用户提到「打包/跨平台/electron/分发/签名/codesign/自签名/自更新报错」→ `packaging/`
- 用户提到「架构/模块设计/技术选型」→ `architecture/`
- 用户提到「排障/疑难/踩坑/按钮点不动/重渲染」→ `debugging/`
- 用户提到「工作流/流程方法」→ `workflow/`
- 用户提到「模板/复用模板」→ `templates/`
- 用户提到「工具/好用项目/图生成/架构图/流程图/时序图」→ `tools/`（本地直接用，搭配 skills 但不被 skills 引入）

## 工具使用原则

- `tools/` 登记本地直接用的好用项目/工具（如 archify 图生成）
- 工具是**本地工具**，skills 不直接引入（保持 skills 纯净、松耦合）
- 用工具时：读 `tools/<工具>.md` 登记 → 按本地位置（如 `~/tools/archify/archify/`）调用
- 登记原则：**实测可用才登记**（防「描述好实际空」的工具）

## 使用原则

- playbook 是**通用原则**（跨项目、跨 git 平台），引用时按需适配具体项目
- 具体工具写法（GitHub Actions / GitLab CI / electron-builder 等）在 playbook 里以「注」给出，**不作为主规则**——主规则是可迁移的做事方法
- 新经验加入 → 按领域放对应目录，命名用主题词（semver-strategy.md），不用日期
- playbook 结构：背景 / 决策或坑 / 通用原则 / 适用范围 / 来源

## 与项目 lessons 的关系

- 项目内 `docs/lessons/` = 项目生命周期经验（带日期、带 prd_slug）
- 本仓库 = 跨项目通用经验（按主题、无日期）
- 项目经验有价值且可通用 → 通用化后迁入本仓库；项目原文不改
