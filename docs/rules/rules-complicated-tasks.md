是的。你现在缺的不是“再多一个普通 checklist”，而是一个**复杂任务门禁流程**。

很多 plan/checklist 没成功，通常是因为：

```text
1. plan 太抽象，没有验收标准；
2. checklist 只在开头出现，做完时没有逐项对照；
3. 没有 Impact Map，所以经常漏掉本地化、数据校验、日志、回滚、文档、素材等边缘项；
4. 没有 Stop Condition，任务越做越大也不停下来重新规划；
5. 没有 Definition of Done，AI 容易“功能大概有了”就宣布完成。
```

所以我建议你的 `00 rule` 做成**所有任务的总控规则**：

```text
任务分级
→ 明确目标和非目标
→ 写可验证的验收标准
→ 做影响范围扫描
→ 分阶段执行
→ 每阶段自检
→ 完成前做 Completion Audit
→ 最终输出验收状态、风险、回滚
```

下面这个可以直接作为新的 `00` rule 使用，或者合并进你已有的 `00`。

建议文件名：

```text
.cursor/rules/00_global_task_execution_protocol.mdc
```

---

# 00_global_task_execution_protocol.mdc

````mdc
---
description: Global task execution protocol, complex task planning, acceptance criteria, impact scan, verification, and definition of done.
alwaysApply: true
---

# 00 Global Task Execution Protocol

## 核心原则

AI 的目标不是“尽快给出看起来完成的答案”，而是交付：

```text
可运行
可验证
可回滚
可继续维护
不遗漏关键配套项
```

对于复杂任务，AI 必须使用结构化流程，不能只凭直觉直接改代码。

本规则是全局总控规则。其他规则负责具体领域，例如 Git、授权、数据校验、本地化、Feature Flag、Replay、性能、构建等。

如果本规则和其他规则冲突，选择更严格、更安全的做法。

---

## 一、任务分级

开始任务前，AI 必须先判断任务规模。

### Simple Task

满足以下大部分条件：

```text
修改 1-3 个文件
局部 bug 修复
局部 UI 文案调整
不改变架构
不改变数据格式
不影响存档
不涉及删除/重命名
不涉及素材授权
```

Simple Task 可以直接处理，但完成后仍需说明：

```text
改了什么
如何测试
如何回滚
```

---

### Complex Task

出现以下任一情况，视为 Complex Task：

```text
修改 4 个以上文件
跨 UI / Gameplay / Data / Asset / Save / Audio / Build 任意两个以上领域
新增一个系统、manager、service、autoload、pipeline
新增一批数据或内容
新增正式 UI 流程
新增战斗、卡牌、敌人、奖励、事件等玩法内容
需要同步文档、测试、数据、素材或本地化
用户说“做一套”“完整实现”“重构”“接入”“系统化”
```

Complex Task 必须先输出任务计划或任务合同，然后再执行。

---

### Critical Task

出现以下任一情况，视为 Critical Task：

```text
删除文件
重命名大量文件
大规模重构
修改存档格式
修改数据 ID 体系
修改核心战斗流程
修改项目启动流程
修改 Godot 版本或插件
引入第三方依赖
修改 export/build/release 配置
涉及法律/授权不明确素材
预计修改超过 10 个文件
可能导致旧系统不可用
```

Critical Task 必须先输出计划，并等待用户确认。

即使用户说“直接做”，以下操作也不能跳过确认：

```text
删除大量文件
破坏旧存档兼容
引入插件
修改 Godot 版本
执行危险 Git 操作
使用授权未知素材作为正式资源
```

---

## 二、复杂任务必须先做 Task Contract

Complex Task 和 Critical Task 在动手前，AI 必须输出 Task Contract。

Task Contract 不需要很长，但必须具体、可验证。

模板：

```text
Task Contract:
- Goal:
- Non-goals:
- Current Understanding:
- Existing Systems To Reuse:
- Acceptance Criteria:
- Impact Map:
- Plan:
- Verification:
- Rollback Strategy:
- Open Questions / Assumptions:
```

---

## 三、Goal 和 Non-goals

复杂任务必须明确：

```text
这次要完成什么
这次明确不做什么
哪些内容留到后续
```

错误示例：

```text
优化战斗系统。
```

正确示例：

```text
Goal:
- 新增一个可在测试场景运行的基础能量战斗回合流程。
- 玩家可以打出卡牌、消耗能量、结束回合。
- 战斗日志能记录核心事件。

Non-goals:
- 本次不做正式美术。
- 本次不做完整敌人 AI。
- 本次不做存档。
- 本次不接入正式星图。
```

---

## 四、Acceptance Criteria 验收标准

Complex Task 必须写可验证的验收标准。

验收标准必须是可以打勾/打叉的，不要写模糊目标。

推荐格式：

```text
Acceptance Criteria:
- [ ] 运行指定 Godot 场景不会报错。
- [ ] 点击“开始战斗”后进入战斗 UI。
- [ ] 玩家初始有 3 点能量。
- [ ] 打出攻击牌后敌人 HP 正确减少。
- [ ] 能量不足时不能打出卡牌，并有 UI/日志反馈。
- [ ] 结束回合后敌人执行一次行动。
- [ ] 战斗关键事件写入 battle log。
- [ ] 新增数据通过基础 ID 检查。
```

不合格示例：

```text
- [ ] 战斗系统更好。
- [ ] UI 更漂亮。
- [ ] 代码更优雅。
```

如果用户需求不明确，AI 应先把需求转成验收标准，再执行。

---

## 五、Impact Map 影响范围扫描

复杂任务最容易漏东西，所以必须做 Impact Map。

AI 必须针对以下领域判断：

```text
Impacted
Not impacted
Unknown
```

模板：

```text
Impact Map:
- Godot scenes/scripts:
- UI / Input / Accessibility:
- Gameplay logic:
- Data / IDs / Validation:
- Localization / Copywriting:
- Assets / Binary files / Git LFS:
- Legal / License / Provenance:
- Save / Migration:
- Feature Flags / Experiments:
- RNG / Replay / Logs:
- Tests / QA:
- Performance:
- Build / Export / Release:
- Docs / AI Memory:
```

如果某项是 `Unknown`，AI 必须：

```text
提出问题
或声明假设
或把它列入风险
```

AI 不得因为用户没提，就自动忽略相关领域。

例如：

新增一张卡牌时，至少要考虑：

```text
Data / IDs
Localization
Asset reference
Validation
Gameplay effect
UI display
Test fixture
```

新增 UI 页面时，至少要考虑：

```text
UI layout
Input/focus
Localization
Responsive sizing
Accessibility
Scene path
Navigation
```

新增 AI 图片时，至少要考虑：

```text
Prompt spec
Asset manifest
License/provenance
Binary storage policy
Godot import path
Git LFS risk
```

---

## 六、先调查现有系统，再新建系统

Complex Task 开始前，AI 必须先检查是否已有相关系统。

在没有确认前，不要重复创建：

```text
Logger
EventBus
FeatureFlags
SaveManager
AssetManifest
LocalizationService
RNGService
BattleSystem
CardDatabase
AudioManager
InputService
```

如果确实要新建系统，必须说明：

```text
为什么不能复用现有系统
新系统边界是什么
如何与现有系统连接
以后如何扩展或删除
```

---

## 七、分阶段执行，而不是一次性大改

复杂任务应拆成阶段。

推荐：

```text
Phase 0: 读取现有结构，确认方案
Phase 1: 添加最小可运行骨架
Phase 2: 接入数据 / UI / 日志
Phase 3: 加测试和验证
Phase 4: 文档、清理、回滚说明
```

每个阶段都应有一个可验证结果。

AI 不应在一个回复里同时做：

```text
大重构
新玩法
新 UI
新素材
新数据格式
新存档格式
```

除非用户明确授权，并且已经接受对应风险。

---

## 八、Scope Drift 范围漂移处理

如果执行过程中发现任务范围变大，AI 必须停止并重新说明。

触发条件：

```text
预计修改文件数增加超过 50%
需要新增原计划外系统
需要删除/重命名文件
发现旧系统与计划冲突
发现需求需要用户选择方案
发现会影响存档、构建、授权或核心数据格式
```

AI 应输出：

```text
Scope Change Detected:
- 原计划是什么
- 新发现的问题是什么
- 影响范围变成什么
- 建议方案
- 是否继续
```

---

## 九、Stop Conditions 必须暂停询问

遇到以下情况，AI 必须暂停并询问用户，不能擅自决定：

```text
需要删除用户文件
需要重命名大量资源
需要修改 Godot 版本
需要引入插件或第三方依赖
需要使用授权未知素材
需要破坏旧存档
需要选择两个以上架构方案
用户需求与现有规则冲突
用户明确要求复制商业 IP、在世艺术家风格或未授权素材
```

---

## 十、Implementation Checklist 执行清单

Complex Task 执行时必须维护清单。

清单可以在聊天中维护；如果任务较长或跨会话，应写入：

```text
docs/ai/tasks/YYYY-MM-DD_task_slug.md
```

任务文件不需要写成长文档，只需要作为轻量 ledger。

推荐模板：

```text
# Task: <name>

Status: planned / in_progress / blocked / done

Goal:
Non-goals:

Acceptance Criteria:
- [ ] ...

Impact Map:
- ...

Plan:
- [ ] Phase 1
- [ ] Phase 2
- [ ] Phase 3

Progress:
- YYYY-MM-DD: ...

Decisions:
- ...

Verification:
- ...

Open Issues:
- ...
```

只有以下情况必须创建任务文件：

```text
任务跨多个会话
预计修改超过 8 个文件
涉及架构
涉及存档
涉及正式数据格式
涉及大量素材
用户明确要求记录
```

否则可以只在回复里维护 checklist。

---

## 十一、不要让 TODO 伪装成完成

AI 可以留下 TODO，但不能把带有核心 TODO 的任务标记为完成。

如果还有 TODO，必须分类：

```text
Blocking TODO:
- 不完成就不能认为任务完成。

Non-blocking TODO:
- 后续优化，不影响本次验收。

Known Risk:
- 当前可运行，但有明确风险。
```

最终输出必须说明：

```text
哪些完成了
哪些没完成
哪些是后续建议
```

---

## 十二、领域遗漏检查表

完成复杂任务前，AI 必须至少快速过一遍以下检查。

### Godot Scene / Script

```text
场景路径是否正确
脚本是否挂载正确
NodePath 是否可能失效
signal 是否连接
autoload 是否需要更新
resource 路径是否存在
class_name 是否冲突
运行入口是否清晰
```

### UI / Input

```text
按钮是否有正常/hover/disabled/focus 状态
是否支持 Esc / Confirm / Cancel
重要操作是否不仅依赖鼠标
长文本是否可能溢出
颜色是否不是唯一信息来源
```

### Gameplay

```text
核心状态是否由逻辑系统管理，而不是 UI 动画
是否有日志
是否有非法操作处理
是否考虑 RNG 可复现
是否需要 replay 支持
```

### Data / IDs

```text
ID 是否唯一
ID 命名是否稳定
引用是否存在
localization key 是否存在
asset_id 是否存在
是否需要更新 registry / manifest
```

### Assets

```text
是否是正式资源还是占位资源
是否记录 prompt/source
是否有授权信息
是否应该进入 Git
是否需要 Git LFS
是否有过大文件
```

### Save

```text
是否改变存档结构
是否需要 save_version
是否兼容旧 dev save
是否需要清空存档说明
```

### Feature Flags

```text
是否是实验功能
是否需要 flag
prototype 是否默认关闭
release 是否允许开启
```

### Logs / Replay

```text
bug 是否可复现
是否记录 seed
是否记录关键 command/event
是否有足够日志定位问题
```

### Tests / Validation

```text
是否有最小测试
是否能运行 data validator
是否有手动测试步骤
是否说明未测试项
```

### Docs / Memory

```text
是否需要更新 project_status
是否需要更新 current_task
是否需要更新 known_issues
是否需要新增 ADR
```

### Build / Release

```text
是否影响导出
是否泄漏 debug/testbed
是否有 blocked_for_release 素材
是否有 prototype flag 进入 release
```

---

## 十三、Verification Ladder 验证阶梯

AI 完成修改后，应尽量按以下顺序验证。

```text
1. 静态检查：文件路径、引用、明显语法错误
2. 数据检查：ID、localization、asset manifest
3. 模块测试：单个系统或测试场景
4. Godot 场景测试：F5/F6 运行指定场景
5. 手动流程测试：用户点击步骤
6. 回归测试：旧功能是否仍然可用
```

AI 不能声称已经运行测试，除非确实通过工具运行过。

如果无法运行，必须写：

```text
未实际运行测试。建议手动测试步骤如下：
...
```

如果只验证了一部分，必须写：

```text
已验证：
未验证：
风险：
```

---

## 十四、Definition of Done 完成定义

Complex Task 只有满足以下条件，才可以说“完成”。

```text
1. Acceptance Criteria 已逐项对照。
2. 修改范围与计划一致，或已说明范围变化。
3. 没有隐藏的 Blocking TODO。
4. 相关数据、文案、素材、日志、测试、文档已处理，或明确说明不适用。
5. 已提供验证方式。
6. 已提供回滚方式。
7. 已列出已知风险。
```

如果没有全部满足，必须说：

```text
本次完成了可运行的阶段性版本，但仍有以下未完成项：
...
```

不得把阶段性结果伪装成完整完成。

---

## 十五、最终输出格式

Simple Task 可以简短。

Complex Task / Critical Task 完成后必须使用以下格式：

```text
Summary:
- 本次完成了什么

Acceptance Criteria Status:
- [x] ...
- [ ] ... 未完成原因

Changed Files:
- path/to/file_a.gd
- path/to/file_b.tscn

Impact Audit:
- UI:
- Gameplay:
- Data:
- Localization:
- Assets:
- Save:
- Feature Flags:
- Logs/Replay:
- Tests:
- Docs:

Verification:
- 已运行：
- 未运行：
- 建议用户手动测试：

Rollback:
- 如何撤销本次修改
- 如果用 Git，建议查看哪些 diff
- 新增文件如何删除
- 是否影响存档/数据

Known Risks:
- ...

Next Steps:
- ...
```

如果某些 Impact Audit 项不相关，可以写：

```text
Not impacted: Save, Build, Audio.
```

但不能完全省略复杂任务的影响审计。

---

## 十六、计划质量要求

AI 输出的 plan 必须具体。

不合格 plan：

```text
1. 修改战斗系统
2. 优化 UI
3. 测试
```

合格 plan：

```text
1. 检查现有 BattleSystem、CardData、BattleTestScene。
2. 新增 EnergyState，并接入打牌消耗逻辑。
3. 更新 Battle UI 显示当前能量。
4. 为能量不足添加日志和按钮禁用反馈。
5. 新增/更新测试场景操作步骤。
6. 更新 current_task 和 known_issues。
```

---

## 十七、复杂任务中的用户确认

对于 Complex Task，AI 应先输出计划。

如果用户已经明确说：

```text
直接做
按你的方案来
不需要问我
```

AI 可以在输出简短计划后继续执行。

但 Critical Task 仍必须等待确认。

---

## 十八、Bug 修复的特殊流程

修 bug 时不要直接猜。

必须尽量明确：

```text
复现步骤
实际结果
预期结果
相关日志
最可能根因
最小修复范围
验证方式
```

如果信息不足，先问最少必要问题。

修复后必须说明：

```text
根因是什么
怎么验证不再发生
是否新增测试/replay
```

---

## 十九、新增系统的特殊流程

新增 manager/service/autoload/system 前，必须回答：

```text
为什么需要新系统
是否已有类似系统
这个系统负责什么
不负责什么
谁调用它
它依赖谁
如何测试
以后如何删除或替换
```

禁止为了方便临时新增一堆全局单例。

---

## 二十、文档不是越多越好

AI 不应为了“显得严谨”创建大量没人维护的文档。

文档规则：

```text
小任务：通常不需要新文档。
复杂任务：需要轻量任务记录或更新现有文档。
架构/数据/存档/素材管线变化：必须更新对应文档。
跨会话任务：必须更新 docs/ai/current_task.md 或 docs/ai/tasks/。
```

文档必须服务于继续开发，而不是堆字数。

---

## 二十一、当用户推翻之前方案

如果用户说：

```text
推翻之前的设计
换方案
这个不要了
重做
```

AI 必须：

```text
1. 找出旧方案涉及文件。
2. 区分可复用基础设施和应废弃逻辑。
3. 说明迁移/删除/归档方案。
4. 检查数据、素材、文档、测试、存档影响。
5. 给出回滚策略。
```

不得直接在旧系统上叠加新系统，导致项目越来越乱。

---

## 二十二、禁止行为

AI 不得：

```text
没有验收标准就开始复杂任务
复杂任务完成后不对照验收标准
不检查影响范围
发现任务变大仍继续硬做
修改无关文件
随意重构
新增重复系统
删除文件不确认
声称测试通过但没有实际运行
把临时代码说成正式方案
忽略本地化、数据校验、授权、回滚、日志等配套项
```

---

## 二十三、最小可用原则

复杂任务优先交付最小可运行闭环。

推荐：

```text
先让一条完整路径跑通
再补扩展
再补美术
再补复杂动效
再补优化
```

不要一开始追求完整大而全，导致每个部分都半成品。

---

## 二十四、如果上下文不足

如果 AI 没有看到项目文件，或无法确认现有结构，必须说：

```text
我需要先查看以下文件/目录：
- ...
```

如果只能基于假设回答，必须写：

```text
Assumptions:
- ...
```

并在最终风险中保留这些假设。

---
````

---

# 我建议你怎么使用这个 00 rule

如果你已有 `00_xxx.mdc`，不要盲目覆盖，可以让 Cursor 做：

```text
请把下面的复杂任务执行协议合并进现有 00 rule。
保留原有项目身份、语言风格、Godot 工作流约束。
如果有冲突，以更严格、更安全的规则为准。
不要修改其他 rule。
完成后输出 Changed Files 和 Rollback。
```

如果你现在的 00 比较散，我建议直接新建/替换为：

```text
.cursor/rules/00_global_task_execution_protocol.mdc
```

---

# 这个规则真正解决的问题

它不是要求 AI “多写计划”，而是强制 AI 在复杂任务里做到：

```text
1. 先定义完成标准；
2. 先扫影响范围；
3. 做到一半发现变复杂，要停下来；
4. 最后必须逐项验收；
5. 没完成的不能假装完成；
6. 每次都要说明怎么测试和怎么回滚。
```

这会比普通 checklist 更有效。普通 checklist 容易写完就忘；这个规则要求：

```text
开头写 Acceptance Criteria
结尾必须回到 Acceptance Criteria Status
```

这样 AI 漏东西的概率会低很多。