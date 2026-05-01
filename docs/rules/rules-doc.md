是的，这部分和 `50复杂任务流程` 很像，但我建议**不要全部塞进 50**。

更好的结构是：

```text
50_global_task_execution_protocol.mdc
负责：任务怎么执行、怎么验收、怎么防漏。

51_product_documentation_system.mdc
负责：产品文档怎么沉淀、改动应该写到哪里、文档先行、开发过程记录、文档和代码同步。
```

然后在 `50 rule` 里加一个很短的门禁：

```text
复杂任务开始前，必须先应用 51_product_documentation_system：
- 判断本次任务影响哪些产品文档；
- 必要时先更新/创建对应文档；
- 再开发；
- 开发完成后回写结果、问题、验证、后续。
```

下面给你一个可直接使用的 rule。

建议文件名：

```text
.cursor/rules/51_product_documentation_system.mdc
```

---

# 51_product_documentation_system.mdc

````mdc
---
description: Product documentation source of truth, doc-first workflow, idea capture, doc routing, feature specs, changelog, and doc/code reconciliation.
alwaysApply: true
---

# 51 Product Documentation System

## 核心目标

本规则用于保证项目不是靠聊天记录和临时记忆推进，而是逐步沉淀成一套可维护的产品文档。

AI 必须帮助用户把：

- 零散 idea；
- 新增功能；
- 玩法改动；
- UI 流程；
- 数据结构；
- 美术 / 音频方向；
- 技术决策；
- Bug 修复；
- 开发过程；
- 开发结果；
- 遗留问题；

落实到合适的文档中。

原则：

```text
先明确要做什么，再开发。
开发中记录关键变化。
开发后回写结果、问题、验证和后续。
不要让重要产品信息只存在于聊天记录里。
```

---

## 与 50 rule 的关系

`50_global_task_execution_protocol.mdc` 负责：

```text
任务分级
计划
验收标准
影响范围
执行流程
测试
回滚
```

本规则负责：

```text
文档归档位置
文档先行
产品知识沉淀
开发过程记录
文档与代码同步
```

对于 Complex Task / Critical Task：

```text
必须先应用本规则，再开始开发。
```

对于 Simple Task：

```text
如果只是很小的内部修复，可以不新增产品文档；
但如果改变了玩家可见行为、玩法数据、UI 文案、素材、流程或架构，仍需要更新相关文档或 changelog。
```

---

## 文档不是越多越好

本项目需要的是：

```text
可维护文档
可追踪文档
能指导开发的文档
能帮助下次 AI 接手的文档
```

不是为了形式创建大量空文档。

AI 必须遵守：

```text
小任务：少写文档，但要说明是否影响产品文档。
复杂任务：必须有轻量任务记录和相关产品文档更新。
架构 / 数据 / 存档 / 素材管线 / 核心玩法变化：必须更新对应文档。
跨会话任务：必须更新 docs/ai/current_task.md 或 docs/ai/tasks/。
```

---

## 推荐文档结构

如果项目尚无产品文档，优先创建最小集合：

```text
docs/product/document_index.md
docs/product/product_brief.md
docs/product/idea_inbox.md
docs/product/roadmap.md
docs/product/change_log.md
docs/product/features/

docs/design/
docs/technical/
docs/ai/tasks/
```

不要一次性创建几十个空目录。

随着项目发展，可扩展为：

```text
docs/product/
  document_index.md
  product_brief.md
  product_state.md
  idea_inbox.md
  roadmap.md
  change_log.md
  features/

docs/design/
  game_loop.md
  combat_design.md
  card_design.md
  economy_balance.md
  ui_flows.md
  progression.md

docs/content/
  lore_bible.md
  glossary.md
  factions.md
  naming_rules.md
  event_writing_guide.md

docs/technical/
  architecture.md
  data_schemas.md
  save_format.md
  logging_replay.md

docs/ai/
  project_status.md
  current_task.md
  next_steps.md
  known_issues.md
  tasks/

docs/decisions/
  ADR_5051_example.md

docs/qa/
  known_repro_cases.md
  test_checklists.md

docs/release/
  build_checklist.md
  release_notes/
```

如果项目已有等价文档，AI 应复用已有文档，不要重复创建新体系。

---

## 文档分层

AI 必须区分不同文档的职责。

### Product Docs

回答：

```text
这个游戏是什么？
为什么要做？
玩家体验是什么？
当前要做哪些功能？
哪些 idea 被接受、搁置或废弃？
```

常见文件：

```text
docs/product/product_brief.md
docs/product/roadmap.md
docs/product/features/*.md
docs/product/idea_inbox.md
docs/product/change_log.md
```

---

### Design Docs

回答：

```text
玩法规则是什么？
战斗怎么运作？
卡牌怎么设计？
UI 流程是什么？
数值和平衡原则是什么？
```

常见文件：

```text
docs/design/combat_design.md
docs/design/card_design.md
docs/design/ui_flows.md
docs/design/economy_balance.md
```

---

### Technical Docs

回答：

```text
系统怎么实现？
数据结构是什么？
存档格式是什么？
日志 / replay 怎么工作？
模块边界是什么？
```

常见文件：

```text
docs/technical/architecture.md
docs/technical/data_schemas.md
docs/technical/save_format.md
docs/technical/logging_replay.md
```

---

### AI Memory

回答：

```text
现在做到哪了？
当前任务是什么？
下次接手应该做什么？
有什么已知问题？
```

常见文件：

```text
docs/ai/project_status.md
docs/ai/current_task.md
docs/ai/known_issues.md
docs/ai/tasks/
```

---

### Decisions / ADR

回答：

```text
为什么选择这个方案？
为什么不用别的方案？
这个决定将来如何回滚或迁移？
```

常见文件：

```text
docs/decisions/ADR_*.md
```

---

## 文档 Source of Truth

以下文档建议作为核心入口：

```text
docs/product/document_index.md
```

它应该记录项目中重要文档的位置和用途。

推荐结构：

```text
# Document Index

Last Updated: YYYY-MM-DD

## Core Product Docs

- Product Brief: docs/product/product_brief.md
- Roadmap: docs/product/roadmap.md
- Idea Inbox: docs/product/idea_inbox.md
- Change Log: docs/product/change_log.md

## Feature Specs

- feature_xxx: docs/product/features/feature_xxx.md

## Design Docs

- Combat Design: docs/design/combat_design.md
- Card Design: docs/design/card_design.md

## Technical Docs

- Architecture: docs/technical/architecture.md
- Data Schemas: docs/technical/data_schemas.md

## AI Memory

- Current Task: docs/ai/current_task.md
- Known Issues: docs/ai/known_issues.md
```

每次创建新的重要文档，都应更新 `document_index.md`。

---

## 文档状态

产品文档和功能规格应标记状态。

推荐状态：

```text
idea
draft
proposed
planned
in_progress
implemented_unverified
implemented_verified
deprecated
rejected
```

含义：

```text
idea：只是想法，未承诺开发。
draft：正在整理。
proposed：方案已成形，但未排期。
planned：准备做。
in_progress：正在做。
implemented_unverified：代码已实现，但未完整验证。
implemented_verified：已实现并通过验证。
deprecated：已废弃。
rejected：明确不做。
```

AI 不得把 `idea` 当成已确定需求，也不得把 `implemented_unverified` 说成已验证完成。

---

## 文档先行原则

对于 Complex Task / Critical Task，AI 必须先做 Documentation Plan。

模板：

```text
Documentation Plan:
- 本次任务类型：
- 需要先读取的文档：
- 需要先更新/创建的文档：
- 开发中需要记录的位置：
- 开发后需要回写的位置：
- 不需要更新的文档及原因：
```

如果相关文档不存在，AI 应优先创建最小可用文档，而不是创建庞大完整文档。

---

## Doc Routing：自动判断应该更新哪个文档

AI 收到任务后，必须判断它属于哪些类型，并路由到对应文档。

### 零散 idea

更新：

```text
docs/product/idea_inbox.md
```

如果 idea 已经比较明确，可以同时创建：

```text
docs/product/features/feature_<id>.md
```

---

### 新功能 / 新系统

更新：

```text
docs/product/features/feature_<id>.md
docs/product/roadmap.md
docs/product/change_log.md
docs/ai/tasks/YYYY-MM-DD_<task_slug>.md
```

如果涉及架构：

```text
docs/technical/architecture.md
docs/decisions/ADR_*.md
```

---

### 玩法改动

更新：

```text
docs/design/combat_design.md
docs/design/card_design.md
docs/product/features/feature_<id>.md
docs/product/change_log.md
```

如果涉及数据：

```text
docs/technical/data_schemas.md
data/ids/id_registry.json
```

---

### 新卡牌 / 敌人 / 状态 / 事件

更新：

```text
docs/design/card_design.md
docs/content/glossary.md
docs/product/change_log.md
```

同时检查：

```text
data/gameplay/**
data/localization/**
data/assets/asset_manifest.json
data/ids/id_registry.json
```

---

### UI / UX 流程

更新：

```text
docs/design/ui_flows.md
docs/product/features/feature_<id>.md
docs/product/change_log.md
```

同时检查：

```text
data/localization/**
docs/content/glossary.md
```

---

### 美术素材 / Prompt / 二进制资源

更新：

```text
docs/art/**
data/assets/asset_manifest.json
docs/legal/asset_licenses.md
docs/product/change_log.md
```

如果是大量二进制：

```text
.gitignore
.gitattributes
docs/technical/asset_pipeline.md
```

---

### 音频素材

更新：

```text
docs/audio/**
data/audio/audio_manifest.json
docs/legal/asset_licenses.md
docs/product/change_log.md
```

---

### 存档 / 数据格式

更新：

```text
docs/technical/save_format.md
docs/technical/data_schemas.md
docs/decisions/ADR_*.md
docs/product/change_log.md
docs/ai/known_issues.md
```

---

### Bug 修复

更新：

```text
docs/ai/known_issues.md
docs/qa/known_repro_cases.md
docs/product/change_log.md
```

如果有 replay：

```text
tests/replays/
data/replays/
```

如果只是内部小 bug 且不影响玩家可见行为，可以只更新：

```text
docs/ai/known_issues.md
```

---

### 架构调整

更新：

```text
docs/technical/architecture.md
docs/decisions/ADR_*.md
docs/product/change_log.md
docs/ai/project_status.md
```

---

### Release / Export

更新：

```text
docs/release/build_checklist.md
docs/release/release_notes/
docs/product/change_log.md
```

---

## Idea Inbox 规则

用户经常会无序提出 idea。AI 不应该强迫用户立刻写完整 PRD。

对于零散想法，AI 应先轻量记录到：

```text
docs/product/idea_inbox.md
```

推荐格式：

```text
## IDEA-YYYYMMDD-551: <标题>

Status: idea
Tags: combat / ui / content / asset / tech / monetization / unknown
Created: YYYY-MM-DD
Source: user

Raw Idea:
- 用户原始想法

Interpretation:
- AI 对这个想法的理解

Potential Value:
- 它可能改善什么

Risks / Unknowns:
- 不确定点
- 可能冲突

Possible Next Step:
- 保留
- 继续澄清
- 转为 feature spec
- 拒绝
```

如果用户只是头脑风暴，AI 应记录为 `idea`，不要擅自开发。

---

## 从 Idea 晋升为 Feature Spec

当一个 idea 准备进入开发，应创建或更新：

```text
docs/product/features/feature_<id>.md
```

`feature_id` 命名规则：

```text
feature_combat_energy
feature_card_upgrade
feature_star_map
feature_deck_builder
feature_ship_modules
feature_event_system
```

不要使用：

```text
new_feature
thing
test
idea1
```

---

## Feature Spec 模板

每个正式功能建议使用以下模板：

```text
# Feature: <feature_id> - <名称>

Status: draft / proposed / planned / in_progress / implemented_unverified / implemented_verified / deprecated
Last Updated: YYYY-MM-DD

## Summary

这个功能是什么。

## Player Value

它给玩家带来什么体验或价值。

## Goals

- 本次要实现什么

## Non-goals

- 本次明确不做什么

## Current Design

当前设计方案。

## UX / Flow

玩家如何看到、进入、操作、退出。

## Gameplay Rules

如果涉及玩法，写清规则。

## Data Requirements

涉及哪些数据、ID、本地化、素材。

## Technical Notes

实现边界、相关系统、可能影响。

## Acceptance Criteria

- [ ] 可验证标准 1
- [ ] 可验证标准 2

## Implementation Plan

- [ ] Phase 1
- [ ] Phase 2

## Development Log

- YYYY-MM-DD: 做了什么，遇到什么问题。

## Verification

- 已验证：
- 未验证：
- 手动测试步骤：

## Known Issues

- 当前已知限制或风险。

## Related Files

- path/to/file.gd
- path/to/scene.tscn

## Related Docs

- docs/design/xxx.md
- docs/technical/xxx.md
```

---

## Task Ledger：复杂任务过程记录

Complex Task / Critical Task 应创建任务记录：

```text
docs/ai/tasks/YYYY-MM-DD_<task_slug>.md
```

除非已经有对应任务文档。

模板：

```text
# Task: <任务名>

Status: planned / in_progress / blocked / done
Created: YYYY-MM-DD
Last Updated: YYYY-MM-DD

## Goal

## Non-goals

## Related Docs

- docs/product/features/feature_xxx.md

## Acceptance Criteria

- [ ] ...

## Impact Map

- UI:
- Gameplay:
- Data:
- Localization:
- Assets:
- Save:
- Feature Flags:
- Tests:
- Docs:

## Plan

- [ ] Phase 1
- [ ] Phase 2

## Development Log

- YYYY-MM-DD:
  - 做了什么
  - 改了哪些关键文件
  - 发现了什么问题

## Decisions

- 决策和理由

## Verification

- 已运行：
- 未运行：
- 建议手动测试：

## Result

- 完成情况
- 未完成项
- 后续建议

## Rollback

- 如何撤销本次任务
```

---

## Change Log 规则

所有玩家可见变化、产品方向变化、玩法变化、UI 流程变化、数据结构变化、重要 bug 修复，都应记录到：

```text
docs/product/change_log.md
```

推荐格式：

```text
## YYYY-MM-DD

### Added

- 新增了什么。

### Changed

- 修改了什么。

### Fixed

- 修复了什么。

### Deprecated

- 废弃了什么。

### Docs

- 更新了哪些文档。

### Notes

- 风险、未验证项、后续事项。
```

小型内部代码整理如果不影响产品，可以不写入产品 changelog，但复杂任务仍应写入 task ledger。

---

## 文档先于开发的最低要求

在开始 Complex Task 前，AI 至少要完成：

```text
1. 判断是否已有相关 feature spec / design doc。
2. 如果有，读取并基于它开发。
3. 如果没有，创建最小 feature spec 或 task ledger。
4. 写清 Goal、Non-goals、Acceptance Criteria。
5. 写清本次会更新哪些文档。
```

不得在需求未明确、验收标准未写清的情况下直接大规模开发。

---

## 开发后的文档回写

开发完成后，AI 必须回写相关文档。

至少包括：

```text
实际完成了什么
哪些验收标准已完成
哪些没有完成
实际改动文件
验证结果
已知问题
后续建议
是否需要更新 roadmap
是否需要更新 known_issues
```

如果实际实现和开发前文档不一致，必须更新文档，而不是让文档过期。

---

## Doc / Code Reconciliation

开发结束前，AI 必须检查：

```text
文档是否仍然描述当前代码
代码是否实现了文档承诺
是否有文档写了但没做的功能
是否有代码做了但文档没记录的功能
是否有旧设计应该标记 deprecated
```

如果发现不一致，必须输出：

```text
Doc Drift Detected:
- 哪些文档过期
- 哪些代码没有文档
- 建议如何修正
```

并尽量直接修正。

---

## No Orphan Change 原则

重要改动不能成为孤儿。

以下改动必须至少关联一个文档：

```text
新增功能
修改玩法
新增 UI 流程
新增数据类型
新增素材管线
新增存档字段
新增正式内容
架构调整
Feature Flag
Bug 修复并影响玩家体验
Release 配置变化
```

关联方式可以是：

```text
feature spec
task ledger
change log
ADR
known issues
QA repro case
asset spec
```

---

## 文档冲突处理

如果文档之间冲突，AI 不得擅自选择一个继续。

必须说明：

```text
Doc Conflict:
- 冲突文档 A
- 冲突文档 B
- 冲突内容
- 推荐以哪个为准
- 是否需要用户确认
```

如果代码和文档冲突，应说明：

```text
Code / Doc Mismatch:
- 文档说什么
- 代码实际是什么
- 推荐修代码还是修文档
```

---

## Roadmap 规则

`docs/product/roadmap.md` 用于记录阶段目标，不应塞入所有细节。

推荐结构：

```text
# Roadmap

## Now

当前正在做。

## Next

接下来准备做。

## Later

未来可能做。

## Parking Lot

暂时搁置。

## Done

已完成的重要节点。
```

当一个功能从 idea 进入 planned / in_progress / done，应同步更新 roadmap。

---

## Product Brief 规则

`docs/product/product_brief.md` 记录项目长期方向，不应频繁重写。

应包含：

```text
游戏一句话描述
目标体验
核心循环
核心卖点
目标平台
视觉/音频方向
设计原则
明确不做的内容
```

如果用户推翻游戏方向，必须更新 product brief，并说明哪些旧设计废弃。

---

## ADR 触发条件

以下情况应创建 ADR：

```text
选择核心架构方案
改变数据格式
改变存档格式
引入插件
改变核心战斗模型
改变主要 UI 架构
决定废弃某个大系统
选择素材管线或 AI 生成流程
```

ADR 推荐路径：

```text
docs/decisions/ADR_YYYYMMDD_<title>.md
```

---

## 文档命名规则

文档文件名使用：

```text
小写英文
下划线
稳定名称
```

推荐：

```text
feature_combat_energy.md
combat_design.md
card_design.md
data_schemas.md
save_format.md
```

不推荐：

```text
新功能.md
final.md
设计2.md
临时.md
```

---

## AI 回答中的文档输出要求

涉及产品文档的任务，AI 必须输出：

```text
Documentation:
- 读取了哪些文档
- 新增了哪些文档
- 更新了哪些文档
- 哪些文档应该更新但本次未更新，原因是什么

Doc Routing:
- 本次改动属于哪些类型
- 对应写入了哪些文档
```

复杂任务最终输出必须包含：

```text
Docs Updated:
- docs/product/features/feature_xxx.md
- docs/product/change_log.md
- docs/ai/tasks/YYYY-MM-DD_xxx.md
```

如果没有更新文档，必须说明：

```text
Docs Not Updated:
- 原因：
- 是否产生文档债：
```

---

## 文档债 Doc Debt

如果因为时间、信息不足或用户要求，暂时没有更新应更新的文档，必须记录为文档债。

推荐写入：

```text
docs/ai/known_issues.md
```

格式：

```text
## Doc Debt: <标题>

Created: YYYY-MM-DD
Related Task:
Missing Docs:
Reason:
Risk:
Suggested Fix:
```

AI 不得让文档债悄悄消失。

---

## 简单任务的轻量流程

Simple Task 可以不创建 task ledger，但仍应判断：

```text
是否改变玩家可见行为
是否改变 UI 文案
是否改变玩法数据
是否改变素材
是否改变存档
是否改变架构
```

如果都没有，可以只在最终回复中写：

```text
Documentation:
- Not impacted. No product docs updated.
```

如果有影响，则至少更新：

```text
docs/product/change_log.md
```

或相关 feature spec。

---

## Bug 修复文档流程

修 bug 时，应记录：

```text
复现步骤
根因
修复方式
验证方式
是否新增 replay/test
```

优先更新：

```text
docs/ai/known_issues.md
docs/qa/known_repro_cases.md
docs/product/change_log.md
```

如果 bug 属于某个功能，也要更新对应：

```text
docs/product/features/feature_<id>.md
```

---

## 用户推翻设计时

当用户说：

```text
这个不要了
推翻之前方案
换方向
重做
```

AI 必须：

```text
1. 找出相关 feature spec / design doc / ADR / task ledger。
2. 标记旧方案 deprecated 或 rejected。
3. 更新 product_brief / roadmap，如果影响方向。
4. 区分可复用基础设施和废弃逻辑。
5. 记录迁移或清理计划。
6. 不要在旧文档里继续追加新方案导致混乱。
```

---

## 防止文档臃肿

AI 不应把每次聊天都复制进文档。

文档应记录：

```text
决策
需求
验收标准
实现结果
风险
后续行动
```

不要记录：

```text
无意义寒暄
重复说明
过长推理过程
无关备选方案
已经废弃且无价值的临时细节
```

---

## 文档质量标准

合格文档应该：

```text
能让下一个 AI 或开发者知道当前状态
能指导下一步开发
能看出哪些已完成、哪些没完成
能追踪为什么这样做
能找到相关文件
```

不合格文档：

```text
只有一句“优化战斗系统”
没有日期
没有状态
没有验收标准
没有相关文件
没有结果记录
和当前代码不一致
```

---

## AI 禁止行为

AI 不得：

```text
复杂任务不写文档直接开发
只在聊天里说计划，不写入长期文档
新增产品功能但不更新 feature spec / changelog
改玩法但不更新 design doc
改架构但不更新 technical doc / ADR
修重要 bug 但不记录 known issue / repro
发现文档过期却不说明
创建大量空文档
重复创建已有文档
把未验证功能标记为 verified
把 idea 当成已确认需求
```

---

## 复杂任务最终输出格式补充

Complex Task / Critical Task 完成后，除了 50 rule 要求的输出外，还必须增加：

```text
Documentation:
- 读取：
- 新增：
- 更新：
- 未更新但应关注：

Doc Status:
- feature_xxx: in_progress / implemented_unverified / implemented_verified
- roadmap 是否更新：
- changelog 是否更新：

Doc / Code Reconciliation:
- 文档与代码是否一致：
- 是否有 Doc Drift：
- 是否有 Doc Debt：
```

---

## 最小可用文档优先

如果项目完全没有文档，第一次不要创建完整体系。

优先创建：

```text
docs/product/document_index.md
docs/product/product_brief.md
docs/product/idea_inbox.md
docs/product/change_log.md
docs/ai/current_task.md
docs/ai/known_issues.md
```

等真正有功能开发时，再逐步创建：

```text
docs/product/features/
docs/design/
docs/technical/
docs/decisions/
```

---
````

---

# 建议给 50 rule 增加这一小段

你不用把上面全部塞进 `50`，只要在 `50_global_task_execution_protocol.mdc` 里加这段即可：

```md
## Product Documentation Gate

在执行 Complex Task / Critical Task 前，AI 必须应用 `51_product_documentation_system.mdc`。

开始开发前必须输出：

```text
Documentation Plan:
- 需要读取的文档
- 需要创建或更新的文档
- 本次开发过程记录位置
- 开发完成后回写位置
```

如果任务涉及以下任一内容：

```text
新功能
玩法变化
UI 流程变化
数据结构变化
素材管线
存档
架构
重要 bug
release 配置
```

则必须先更新或创建最小相关文档，再开发。

开发完成后必须回写：

```text
完成结果
验收状态
实际改动文件
验证结果
已知问题
后续建议
```

复杂任务最终输出必须包含：

```text
Documentation:
- 读取了哪些文档
- 新增了哪些文档
- 更新了哪些文档
- 是否存在 Doc Drift / Doc Debt
```
```

---

# 推荐你让 Cursor 执行

```text
请创建 .cursor/rules/51_product_documentation_system.mdc，并写入我提供的规则内容。

然后修改现有 50 rule：
- 增加 Product Documentation Gate 小节；
- 不要删除已有内容；
- 如果有冲突，以更严格、更安全的规则为准。

如果 docs/product 目录不存在，只创建最小目录：
docs/product/
docs/product/features/
docs/ai/tasks/

不要创建大量空文档。
完成后输出 Changed Files、How to Test、Rollback。
```

这个规则会把你的流程从：

```text
想到什么做什么
做完以后靠聊天记忆
下次 AI 不知道前因后果
```

变成：

```text
idea 进入 inbox
任务前生成 spec / task ledger
开发中记录过程
开发后回写结果
产品 changelog 持续更新
文档和代码保持同步
```