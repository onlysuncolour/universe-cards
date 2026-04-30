> 已经完成

下面直接生成这 6 个 `.cursor/rules` 文件内容。你可以让 Cursor 新建这些文件并复制进去。

```text
.cursor/rules/
  03_project_memory_git_safety.mdc
  04_legal_license_provenance.mdc
  19_feature_flags_experiment_modes.mdc
  25_localization_copywriting.mdc
  31_data_validation_id_registry.mdc
  34_bug_reproduction_replay.mdc
```

其中建议：

```text
03 / 04 / 31：alwaysApply: true
19 / 25 / 34：按相关文件触发
```

---

# 03_project_memory_git_safety.mdc

````mdc
---
description: Project memory, AI handoff, Git safety, rollback, and change management rules.
alwaysApply: true
---

# Project Memory and Git Safety

## 核心目标

本规则用于保证 AI 协作开发时：

- 项目状态不会丢失；
- AI 不会反复重新发明已有系统；
- 大改之前有计划；
- 删除、重命名、重构可回滚；
- 每次修改都有清晰交接记录；
- 用户作为 Godot 新手也能知道如何验证和回退。

AI 必须把项目当成一个长期维护的游戏工程，而不是一次性脚本任务。

---

## 项目记忆文件

以下文件是 AI 交接和项目状态的 Source of Truth：

```text
docs/ai/project_status.md
docs/ai/current_task.md
docs/ai/next_steps.md
docs/ai/known_issues.md
docs/ai/architecture_overview.md
docs/decisions/
```

如果这些文件不存在，AI 在合适时机可以建议创建，但不要为了小任务强制创建大量文档。

---

## 何时必须更新项目记忆

当发生以下情况时，AI 必须更新或建议更新相关文档：

### 必须更新 `docs/ai/project_status.md`

- 新增一个可运行模块；
- 完成一个阶段性目标；
- 改变项目启动流程；
- 改变当前默认玩法或 UI 流程；
- 新增重要目录结构；
- 废弃一个旧系统。

### 必须更新 `docs/ai/current_task.md`

- 用户正在进行一个多步任务；
- AI 完成了任务的一部分但还有后续；
- 当前任务需要跨会话继续。

### 必须更新 `docs/ai/known_issues.md`

- 发现未解决 bug；
- 有临时代码、风险或 TODO；
- 用户暂时接受一个已知限制；
- 修复了之前记录的问题。

### 必须更新 `docs/ai/architecture_overview.md`

- 新增核心架构模块；
- 修改 UI / Gameplay / Data / Save / Asset 管线边界；
- 新增跨模块通信方式；
- 修改状态管理方式。

### 必须新增 `docs/decisions/ADR_xxxx_title.md`

当做出以下决策时：

- 选择一种架构方案；
- 引入第三方插件或工具；
- 改变数据格式；
- 改变存档格式；
- 改变战斗核心流程；
- 改变素材管线；
- 做出之后可能影响很多文件的设计选择。

ADR 文件应至少包含：

```text
标题
日期
背景
决策
备选方案
影响
后续行动
```

---

## 修改前的安全检查

在进行代码或数据修改前，AI 应先判断修改规模：

```text
Small：1-3 个文件，局部修复或小功能。
Medium：4-10 个文件，涉及一个模块。
Large：超过 10 个文件，或涉及架构、数据格式、存档、玩法核心、UI 主流程。
```

### Small 修改

可以直接进行，但完成后必须说明：

```text
修改了什么
如何测试
如何回滚
```

### Medium 修改

开始前应简要说明：

```text
影响范围
计划修改文件
风险点
测试方式
```

### Large 修改

开始前必须先输出计划并等待用户确认，除非用户已经明确授权大改。

计划必须包含：

```text
目标
涉及系统
预计修改文件
可能破坏的内容
回滚方式
测试方式
是否需要先提交 Git
```

---

## Git 安全规则

AI 不得擅自执行危险 Git 操作。

禁止在未得到用户明确许可时执行：

```text
git reset --hard
git clean -fd
git checkout .
git rebase
git push --force
删除大量文件
覆盖用户未提交内容
```

大改前，AI 应提醒用户：

```text
建议先提交当前可运行版本，或至少创建一个 Git checkpoint。
```

如果用户没有使用 Git，AI 应提醒：

```text
建议在重大改动前复制项目文件夹作为备份。
```

---

## 删除和重命名规则

删除文件前必须确认：

```text
为什么删除
是否有引用
是否需要迁移
是否需要保留归档
是否影响 Godot 场景引用
```

重命名文件、节点、资源路径前必须说明：

```text
旧路径
新路径
引用位置
需要同步修改的文件
测试方式
```

AI 不应为了“看起来更整洁”而随意重命名大量文件。

---

## 回滚要求

每次完成任务后，AI 必须输出回滚信息。

格式：

```text
Rollback:
- 如果要撤销本次修改，恢复以下文件：
  - path/to/file_a.gd
  - path/to/file_b.tscn
- 如果使用 Git，可回退本次提交或查看 diff。
- 如果生成了新文件，可删除：
  - path/to/new_file.md
```

如果本次修改涉及数据格式或存档格式，必须额外说明：

```text
是否兼容旧数据
是否需要清空 dev save
是否需要更新 validator
```

---

## 测试诚实原则

AI 不能声称已经运行了测试，除非确实通过工具执行了测试。

如果没有实际运行，必须说：

```text
未实际运行测试。建议手动测试步骤如下：
...
```

如果测试部分通过，必须明确：

```text
已验证：
未验证：
风险：
```

---

## 不允许的行为

AI 不得：

- 在未说明影响的情况下大范围重构；
- 为了修一个 bug 改动无关系统；
- 删除旧代码但不说明替代方案；
- 把临时代码伪装成正式系统；
- 在多个地方重复实现同一逻辑；
- 让用户不知道下一步该如何测试；
- 完成后不提供修改摘要；
- 修改项目规则但不说明原因。

---

## 每次任务完成后的固定输出

除非用户明确只要求简短回答，否则 AI 完成修改后必须输出：

```text
Summary:
- 本次做了什么

Changed Files:
- 文件列表

How to Test:
- Godot 中如何运行
- 如果是 F5/F6，说明运行哪个场景
- 预期结果
- 预期日志

Rollback:
- 如何撤销本次修改

Known Risks:
- 临时代码
- 未测试点
- 后续建议
```

---

## 用户推翻设计时的处理

当用户说：

```text
推翻之前的系统
不要用这个玩法了
这个 UI 方向不要了
换一种方案
```

AI 应该：

1. 找出旧系统涉及文件；
2. 区分“可复用基础设施”和“应废弃设计代码”；
3. 说明哪些保留、哪些删除、哪些归档；
4. 更新项目记忆；
5. 更新测试和文档；
6. 不要为了兼容旧设计保留无用复杂度。

推翻玩法或 UI 方向是允许的，但不能破坏：

```text
日志
测试入口
数据入口
RNG 可复现
UI / 逻辑边界
素材 prompt 记录
项目记忆
```
````

---

# 04_legal_license_provenance.mdc

````mdc
---
description: Legal, license, provenance, AI-generated asset, third-party dependency, font, audio, and prompt safety rules.
alwaysApply: true
---

# Legal, License, and Provenance

## 核心目标

本规则用于确保项目中的：

- AI 生成图片；
- AI 生成音频；
- 字体；
- 第三方素材；
- 插件；
- 代码依赖；
- 参考图；
- prompt；

都有清晰来源、授权状态和使用限制。

AI 不提供法律意见，但必须帮助用户记录风险，避免明显侵权和来源不明素材进入项目。

---

## Source of Truth

法律、授权和来源记录应集中在：

```text
docs/legal/asset_licenses.md
docs/legal/ai_generation_policy.md
docs/legal/third_party_dependencies.md
data/assets/asset_manifest.json
docs/art/asset_specs/
docs/audio/asset_specs/
```

如果这些文件不存在，AI 在添加相关素材或依赖时应建议创建。

---

## 禁止行为

AI 不得建议或生成以下内容：

```text
直接复制某个商业游戏、电影、动漫、小说、漫画、桌游、卡牌游戏的具体设计
直接要求“做得和某某游戏一样”
直接使用在世艺术家的名字作为风格关键词
直接使用商业 IP 的角色、飞船、logo、阵营标志
生成真实品牌 logo、商标、水印
生成仿冒 UI、仿冒卡牌框、仿冒知名游戏图标
使用来源不明且不允许商用的图片、字体、音效、音乐
移除水印或规避授权限制
```

当用户提出风险较高的要求时，AI 应改写为安全的描述性 prompt。

错误示例：

```text
做一个像《某知名科幻 IP》里的战舰
```

正确改写：

```text
做一个原创的硬表面科幻战舰，深色金属装甲，模块化结构，青蓝色引擎光，3/4 俯视角，轮廓清晰。
```

---

## AI 生成素材必须记录的信息

每一个 AI 生成素材都必须尽量记录：

```text
asset_id
asset_type
用途
生成工具
模型名称
模型版本，如果可得
生成日期
prompt 文档路径
positive prompt
negative prompt
seed，如果有
参考图来源，如果有
候选图路径
选中图路径
Godot 导入路径
授权状态
是否允许商用
备注
```

推荐写入：

```text
docs/art/asset_specs/<category>/<asset_id>.md
data/assets/asset_manifest.json
```

音频素材写入：

```text
docs/audio/asset_specs/<category>/<asset_id>.md
```

---

## 第三方素材规则

任何第三方素材进入项目前，必须记录：

```text
名称
来源 URL
作者
许可证
是否允许商用
是否允许修改
是否需要署名
下载日期
本地路径
备注
```

如果许可证不明确：

```text
不得作为正式素材使用。
只能作为临时占位素材。
必须标记为 placeholder 或 unlicensed_reference。
```

未经确认的素材不得进入：

```text
assets/
export/
release/
```

可以临时放入：

```text
assets/temp/
assets/placeholders/
```

但必须有清晰标记。

---

## 字体规则

新增字体前必须确认：

```text
字体名称
来源
许可证
是否允许商用
是否允许嵌入游戏
是否需要署名
支持字符范围，尤其是中文
```

禁止使用来源不明字体。

如果只是开发期占位字体，必须标记：

```text
placeholder_font
not_for_release
```

---

## 音频规则

新增音乐、音效、环境音前必须记录：

```text
audio_id
来源或生成工具
许可证
是否允许商用
是否可循环
推荐音量
是否需要署名
原始文件路径
Godot 导入路径
```

AI 生成音频也要记录 prompt 和工具。

禁止直接使用来源不明的音频。

---

## 插件和第三方代码规则

引入 Godot 插件、第三方库、脚本前必须说明：

```text
用途
来源
许可证
版本
维护状态
是否有替代方案
是否必须引入
移除成本
```

AI 不得因为方便就随意引入插件。

默认策略：

```text
能不用插件就不用插件。
必须引入时先说明风险和收益。
```

---

## Prompt 安全规则

AI 生成图片或音频 prompt 时，应使用描述性风格词，而不是侵权性引用。

允许：

```text
semi-realistic 2D sci-fi game art
hard-surface spaceship design
dark gunmetal hull
cyan emissive light
holographic terminal UI
deep-space atmosphere
```

禁止：

```text
in the style of <living artist>
same as <specific commercial game>
copy <specific IP ship>
using <real brand logo>
```

如果用户要求“像某某作品”，AI 应提取抽象设计目标：

```text
暗色科幻
硬表面结构
可读性强
冷色发光
模块化舰体
终端式 UI
```

然后生成原创描述。

---

## Release 前检查

发布前必须确认：

```text
没有来源不明素材
没有未授权字体
没有未授权音效或音乐
没有水印
没有真实商标
没有商业 IP 标志
没有直接仿制知名游戏 UI
asset_manifest 状态清晰
third_party_dependencies 文档完整
```

如果发现风险素材，应标记：

```text
blocked_for_release
```

或替换为合法素材。

---

## AI 回答要求

当用户要求添加素材、字体、音频、插件时，AI 必须输出：

```text
Provenance:
- 来源
- 授权状态
- 是否可商用
- 是否需要记录到 manifest

Risk:
- 是否有版权风险
- 是否是占位资源
- 是否可用于正式发布
```

如果信息不足，AI 必须明确写：

```text
授权状态未知，不能作为正式素材使用。
```
````

---

# 19_feature_flags_experiment_modes.mdc

````mdc
---
description: Feature flags, development config, experiment toggles, prototype/candidate/stable gameplay and UI modes.
globs:
  - "data/dev/**"
  - "src/core/feature_flags/**"
  - "src/dev/**"
  - "src/prototypes/**"
  - "docs/experiments/**"
  - "tests/**"
alwaysApply: false
---

# Feature Flags and Experiment Modes

## 核心目标

本规则用于管理开发期实验开关，避免：

- Prototype 功能悄悄变成正式功能；
- 旧实验残留在主流程；
- 多套玩法互相污染；
- UI、战斗、数据、素材实验无法回滚；
- 用户不知道当前运行的是哪个实验版本。

所有实验功能必须可识别、可开关、可删除。

---

## 推荐目录

```text
data/dev/dev_config.json
data/dev/feature_flags.json
src/core/feature_flags/
src/dev/
src/prototypes/
docs/experiments/
```

---

## Feature Flag Source of Truth

Feature flags 应集中管理，不应散落在代码各处。

推荐文件：

```text
data/dev/feature_flags.json
```

推荐结构：

```json
{
  "version": 1,
  "flags": {
    "combat.energy_v1": {
      "enabled": true,
      "mode": "prototype",
      "owner": "gameplay",
      "description": "Prototype energy-based combat model.",
      "created_at": "YYYY-MM-DD",
      "expires_at": "YYYY-MM-DD",
      "release_allowed": false
    },
    "ui.new_card_layout": {
      "enabled": false,
      "mode": "prototype",
      "owner": "ui",
      "description": "Experimental card layout.",
      "created_at": "YYYY-MM-DD",
      "expires_at": "YYYY-MM-DD",
      "release_allowed": false
    }
  }
}
```

---

## 模式定义

每个实验必须属于以下模式之一：

```text
prototype
candidate
stable
deprecated
```

### prototype

用于快速验证。

允许：

```text
临时数据
临时 UI
硬编码测试值
testbed 专用逻辑
```

禁止：

```text
写入正式存档
混入正式数据池
默认进入 release
污染稳定系统
```

### candidate

准备进入主流程的候选方案。

必须：

```text
数据化
有日志
有测试入口
有文档
可回滚
```

### stable

当前正式方案。

必须：

```text
默认开启
有测试保护
有当前设计文档
修改时说明迁移影响
```

### deprecated

废弃方案。

必须：

```text
默认关闭
不得进入正式流程
可归档或删除
```

---

## Feature Flag 命名规则

使用小写、点分层：

```text
combat.energy_v1
combat.heat_v1
ui.new_card_layout
run.star_map_v2
asset.use_temp_card_art
debug.verbose_battle_log
```

不要使用模糊名称：

```text
new_system
test
flag1
thing_enabled
```

---

## 中央访问原则

代码不应到处直接读取 JSON。

推荐统一入口：

```text
FeatureFlags.is_enabled("combat.energy_v1")
FeatureFlags.get_mode("combat.energy_v1")
FeatureFlags.require("debug.verbose_battle_log", false)
```

如果项目还没有 FeatureFlags 服务，Prototype 阶段可以临时读取，但进入 Candidate 前必须整理成统一入口。

---

## 默认值规则

实验功能默认应安全关闭。

除非该功能已经是 stable，否则：

```text
release_allowed: false
```

Release build 中不得默认开启 prototype 功能。

如果找不到某个 flag，默认值必须安全：

```text
false
```

并输出清晰 warning。

---

## 日志规则

游戏启动时应记录当前启用的重要 flags：

```text
feature_flags_loaded
active_feature_flags
```

战斗、星图、UI 大实验也应记录：

```text
combat_model = combat.energy_v1
run_model = run.star_map_v2
ui_card_layout = ui.new_card_layout
```

这样 bug 和 replay 才能复现。

---

## 实验文档

每个重要实验应有文档：

```text
docs/experiments/exp_001_energy_combat.md
docs/experiments/exp_002_heat_combat.md
```

实验文档应包含：

```text
实验目标
假设
启用 flag
涉及文件
测试方式
结果
是否进入 Candidate
是否废弃
```

---

## 不允许的行为

AI 不得：

- 用 feature flag 掩盖糟糕架构；
- 同时维护太多旧玩法导致主流程混乱；
- 让 prototype 默认进入 release；
- 添加 flag 但没有说明用途；
- 添加 flag 后不提供删除条件；
- 将数据格式差异隐藏在多个 if 里。

如果一个实验被确认废弃，应删除或归档，而不是永久保留。

---

## 修改 Feature Flag 时必须输出

AI 修改 feature flag 时必须说明：

```text
Flag:
- 名称
- 模式
- 默认是否开启
- release 是否允许
- 影响范围

Test:
- 如何开启
- 如何关闭
- 如何验证
- 回滚方式
```

---

## 测试要求

对于 Candidate 或 Stable 功能，至少应测试：

```text
flag 开启时能运行
flag 关闭时旧流程不坏
日志能显示当前 flag 状态
release 配置不会启用 prototype
```

对于 Prototype，可以只提供手动 testbed 测试，但必须隔离。
````

---

# 25_localization_copywriting.mdc

````mdc
---
description: Localization keys, copywriting style, card text, UI text, terminology, and text-in-art separation rules.
globs:
  - "data/localization/**"
  - "docs/content/**"
  - "data/gameplay/**"
  - "src/ui/**"
  - "scenes/ui/**"
  - "docs/art/**"
alwaysApply: false
---

# Localization and Copywriting

## 核心目标

本规则用于保证：

- UI 文本集中管理；
- 卡牌、状态、事件文案风格统一；
- 后续支持多语言不会大返工；
- 文本不会烘焙进图片；
- 长文本不会破坏 UI；
- AI 不会到处硬编码中文或英文。

即使项目初期只做中文，也应尽量使用 localization key。

---

## 推荐目录

```text
data/localization/
  zh_cn.json
  en_us.json

docs/content/
  text_style_guide.md
  glossary.md
  naming_rules.md
  lore_bible.md
```

---

## 文本 Source of Truth

正式面向玩家的文本应来自 localization 文件。

包括：

```text
主菜单文本
按钮文本
设置文本
卡牌名称
卡牌描述
状态名称
状态描述
敌人名称
事件标题
事件正文
事件选项
奖励说明
错误提示
教程文本
```

不应在代码里到处硬编码玩家可见文本。

---

## 允许硬编码的情况

以下文本可以临时硬编码：

```text
debug log
testbed 按钮
开发期占位文本
内部错误标记
临时 prototype 文案
```

但如果进入 Candidate 或 Stable，必须迁移到 localization key。

---

## Localization Key 命名规则

使用小写、点分层：

```text
ui.main_menu.start
ui.main_menu.settings
ui.common.confirm
ui.common.cancel

card.plasma_shot.name
card.plasma_shot.desc

status.overheat.name
status.overheat.desc

enemy.raider_drone.name
enemy.raider_drone.intent.attack

event.abandoned_station.title
event.abandoned_station.body
event.abandoned_station.choice.search
event.abandoned_station.choice.leave
```

不要使用：

```text
text1
button_ok_2
new_card_name
asdf
```

---

## Localization 文件格式

推荐 JSON：

```json
{
  "ui.common.confirm": "确认",
  "ui.common.cancel": "取消",
  "card.plasma_shot.name": "等离子射击",
  "card.plasma_shot.desc": "造成 {damage} 点伤害。"
}
```

动态数值使用占位符：

```text
{damage}
{block}
{amount}
{turns}
{card_name}
```

不要把数值直接写死在文案里，如果该数值来自玩法数据。

---

## 卡牌文案规则

卡牌描述应：

```text
短句优先
效果顺序清晰
术语统一
数值使用占位符
避免过长
避免文学化过度
```

推荐句式：

```text
造成 {damage} 点伤害。
获得 {block} 点护盾。
抽 {count} 张牌。
施加 {amount} 层过热。
本回合内，每当你打出攻击牌，获得 {block} 点护盾。
```

不推荐：

```text
你操纵飞船发射出一道威力强大的能量光束，它将敌人撕裂并带来毁灭性的打击。
```

卡牌可以有 flavor text，但应与规则文本分离。

推荐：

```text
card.xxx.desc
card.xxx.flavor
```

---

## 术语统一

所有游戏术语必须统一。

例如，如果决定使用：

```text
护盾
能量
热量
模块
舰体
过热
干扰
校准
```

就不要在不同地方混用：

```text
护甲 / 格挡 / 护盾
能源 / 能量 / 行动力
飞船 / 舰船 / 船体
```

术语应记录在：

```text
docs/content/glossary.md
```

---

## 世界观文案风格

科幻 Roguelite 卡牌游戏建议整体语气：

```text
冷静
克制
技术感
探索未知
轻微压迫感
避免网络梗
避免过度中二
避免现代口语过多
```

除非明确是喜剧事件，不要让事件和系统文本风格突兀。

---

## 文本和图片分离

卡牌插画、飞船图、背景图中不得烘焙正式文本。

尤其禁止：

```text
卡牌名称画在 card_art 中
卡牌费用画在 card_art 中
卡牌描述画在 card_art 中
UI 按钮文字直接画进背景图
```

正式文字应由 Godot UI 渲染，便于：

```text
改数值
本地化
适配字体
适配分辨率
修正文案
```

---

## UI 溢出检查

新增或修改文本后，必须考虑：

```text
中文是否过长
英文是否更长
按钮是否能容纳
卡牌描述是否溢出
小分辨率是否可读
字体是否支持字符
```

如果 UI 没有自适应能力，应在任务输出中说明风险。

---

## 多语言策略

初期可以只完整维护：

```text
zh_cn.json
```

但新增 key 时建议同时在：

```text
en_us.json
```

加入英文草稿或 TODO。

例如：

```json
{
  "card.plasma_shot.name": "Plasma Shot",
  "card.plasma_shot.desc": "Deal {damage} damage."
}
```

如果暂不翻译，必须标记：

```text
TODO_TRANSLATE
```

---

## AI 修改文本时必须输出

当 AI 新增或修改玩家可见文本时，必须说明：

```text
Localization Keys:
- 新增 key
- 修改 key
- 删除 key

UI Risk:
- 是否可能溢出
- 是否需要检查卡牌/按钮/面板尺寸

Terminology:
- 是否新增术语
- 是否需要更新 glossary
```

---

## 不允许的行为

AI 不得：

- 在正式 UI 代码中硬编码大量中文；
- 在图片 prompt 中要求生成卡牌文字；
- 同一个概念使用多个名称；
- 修改卡牌数值但忘记同步描述占位符；
- 删除 localization key 但不检查引用；
- 编写过长卡牌描述导致 UI 不可读。
````

---

# 31_data_validation_id_registry.mdc

````mdc
---
description: Data validation, unique IDs, cross-reference checks, schema sanity, registry, and content integrity rules.
alwaysApply: true
---

# Data Validation and ID Registry

## 核心目标

本规则用于保证项目中所有数据：

- ID 唯一；
- 引用有效；
- 缺字段能被发现；
- 卡牌、敌人、状态、奖励、素材、本地化不会互相引用错误；
- AI 添加数据后不会留下隐性 bug；
- 后期平衡和内容扩展可维护。

数据错误必须尽早暴露，而不是运行到一半才崩溃。

---

## 推荐目录

```text
data/ids/id_registry.json
tools/validation/validate_project_data.gd
tests/validation/
data/gameplay/
data/assets/asset_manifest.json
data/localization/
```

如果项目还没有 validator，AI 在新增较多数据时应建议创建最小校验工具。

---

## ID 命名规则

所有正式 ID 使用：

```text
小写英文
下划线
稳定前缀
不可包含空格
不可包含中文
不可包含特殊符号
```

推荐：

```text
card_plasma_shot
card_emergency_repair
enemy_raider_drone
status_overheat
module_shield_generator_mk1
reward_add_card_common
event_abandoned_station
asset_card_art_plasma_shot
ship_player_survey_corvette
```

不推荐：

```text
Plasma Shot
等离子射击
card-1
new_card
test
xxx
```

---

## ID Registry

推荐维护：

```text
data/ids/id_registry.json
```

示例：

```json
{
  "version": 1,
  "ids": {
    "cards": [
      "card_plasma_shot",
      "card_emergency_repair"
    ],
    "enemies": [
      "enemy_raider_drone"
    ],
    "statuses": [
      "status_overheat"
    ],
    "assets": [
      "card_art_plasma_shot"
    ],
    "events": []
  },
  "deprecated": {
    "card_old_laser": {
      "replaced_by": "card_plasma_shot",
      "deprecated_at": "YYYY-MM-DD",
      "reason": "Renamed during card naming cleanup."
    }
  }
}
```

如果暂时不维护全局 registry，至少每类数据必须在加载时检查重复 ID。

---

## 必须校验的内容

Validator 应逐步支持以下检查。

### 通用检查

```text
JSON / cfg / resource 格式可解析
必填字段存在
字段类型正确
ID 唯一
ID 命名合法
没有重复文件定义同一 ID
```

### 卡牌数据

检查：

```text
card_id 唯一
name_key 存在
desc_key 存在
asset_id 或 card_art_id 存在
effects 格式合法
cost 格式合法，如果当前系统使用 cost
target 类型合法
引用的 status_id 存在
引用的 card_id 存在
```

### 敌人数据

检查：

```text
enemy_id 唯一
name_key 存在
hp 合法
intent / pattern 格式合法
引用的 action/effect/status 存在
asset_id 存在，如果需要
```

### 状态数据

检查：

```text
status_id 唯一
name_key 存在
desc_key 存在
hook 类型合法
icon asset 存在
```

### 奖励数据

检查：

```text
reward_id 唯一
奖励引用的 card_id / module_id 存在
稀有度合法
权重合法
```

### 事件数据

检查：

```text
event_id 唯一
title_key 存在
body_key 存在
choice_id 唯一
choice text key 存在
事件结果引用的数据存在
```

### 素材数据

检查：

```text
asset_id 唯一
prompt_doc 存在
source_files 记录合理
imported_files 存在，如果 status 是 imported
status 合法
style_version 存在
```

### 本地化

检查：

```text
代码或数据引用的 localization key 存在
缺失 key 输出 error 或 warning
未使用 key 可输出 warning
占位符一致
```

---

## 严重程度

校验结果分为：

```text
ERROR
WARNING
INFO
```

### ERROR

会导致游戏错误或内容不可用。

例如：

```text
重复 ID
引用不存在的 card_id
必填字段缺失
JSON 无法解析
导入素材路径不存在
```

### WARNING

不一定阻止运行，但需要关注。

例如：

```text
未使用的 localization key
缺少英文翻译
prototype 数据未进入正式 registry
数值异常大
asset 状态仍是 prompt_draft
```

### INFO

提示信息。

例如：

```text
加载了多少张卡
加载了多少个敌人
当前 style_version
```

---

## Prototype 数据规则

Prototype 数据可以不进入正式数据池，但必须隔离。

推荐目录：

```text
data/prototypes/
docs/experiments/
```

Prototype 数据如果绕过完整校验，必须标记：

```text
prototype_only
not_for_release
```

进入 Candidate 前必须：

```text
加入正式数据目录
通过 validator
补齐 localization key
补齐 asset 引用
补齐测试 fixture
```

---

## 数据修改时 AI 必须做的事

当 AI 新增、删除、重命名数据 ID 时，必须：

```text
更新引用
更新 id_registry，如果存在
更新 localization key
更新 asset_manifest，如果涉及素材
更新测试 fixture，如果存在
说明是否破坏旧存档或 replay
```

重命名 ID 时必须说明：

```text
old_id
new_id
影响文件
是否加入 deprecated 映射
```

---

## 不允许的行为

AI 不得：

- 创建重复 ID；
- 使用中文或空格作为正式 ID；
- 让 UI 直接猜测数据路径；
- 添加卡牌但不添加 name_key / desc_key；
- 添加素材引用但不更新 asset_manifest；
- 删除 ID 但不检查引用；
- 静默忽略缺失数据；
- 把校验错误降级成无提示 fallback。

---

## Validator 运行建议

如果项目已有 Godot headless 验证脚本，应使用类似方式运行：

```text
godot --headless --script tools/validation/validate_project_data.gd
```

具体命令以项目 Godot 版本和脚本为准。

如果无法运行，AI 必须提供手动检查步骤。

---

## AI 完成数据相关任务后的输出

数据相关任务完成后，AI 必须输出：

```text
Data Changes:
- 新增 ID
- 修改 ID
- 删除 ID
- 重命名 ID

Validation:
- 是否运行 validator
- 如果未运行，如何手动验证
- 已知 warning

References:
- 影响的卡牌/敌人/状态/奖励/事件/素材/本地化

Rollback:
- 如何恢复旧数据
```
````

---

# 34_bug_reproduction_replay.mdc

````mdc
---
description: Bug reporting, deterministic replay, battle/run reproduction packages, regression fixtures, and debugging workflow.
globs:
  - "docs/qa/**"
  - "logs/**"
  - "tests/**"
  - "src/gameplay/**"
  - "src/core/rng/**"
  - "src/core/logging/**"
  - "data/replays/**"
alwaysApply: false
---

# Bug Reproduction and Replay

## 核心目标

本规则用于确保 bug 可以被复现、分析和修复，尤其是：

- 战斗 bug；
- 随机数 bug；
- 卡牌效果 bug；
- 敌人行动 bug；
- 奖励生成 bug；
- 星图事件 bug；
- 存档 / 读档 bug；
- UI 与逻辑不同步 bug。

对于卡牌 Roguelite，单靠“我点了一下就错了”很难定位问题。必须尽早建立 seed、command、event、state 的复现记录。

---

## 推荐目录

```text
docs/qa/bug_report_template.md
docs/qa/known_repro_cases.md
data/replays/
tests/replays/
logs/replays/
```

开发期也可以把临时 replay 存在：

```text
user://replays/dev/
```

但重要复现用例应整理到项目目录中。

---

## Bug 报告模板

当用户报告 bug 时，AI 应尽量收集：

```text
1. 运行的是哪个场景？
2. 是 F5 运行项目，还是 F6 运行当前场景？
3. 操作步骤是什么？
4. 预期结果是什么？
5. 实际结果是什么？
6. Godot Output 完整报错是什么？
7. 是否开启 verbose log？
8. 当前 run_seed / battle_seed 是什么？
9. 当前启用了哪些 feature flags？
10. 是否能稳定复现？
11. 截图或录屏，如果有。
12. replay 文件，如果有。
```

如果信息不足，AI 应先提出最小必要问题，不要盲目大改。

---

## Replay 数据应记录什么

战斗或 run 相关 replay 应尽量记录：

```text
replay_version
game_version
godot_version
created_at
feature_flags
data_versions
run_seed
battle_seed
initial_state
player_commands
rng_calls 或 rng stream 标识
battle_events
final_state
error_log
checksum，如果有
```

示例结构：

```json
{
  "replay_version": 1,
  "game_version": "dev",
  "created_at": "YYYY-MM-DDTHH:MM:SS",
  "feature_flags": {
    "combat.energy_v1": true
  },
  "data_versions": {
    "cards": 1,
    "enemies": 1
  },
  "run_seed": 123456,
  "battle_seed": 987654,
  "initial_state": {},
  "commands": [
    {
      "turn": 1,
      "type": "play_card",
      "card_instance_id": "c001",
      "target_id": "enemy_001"
    },
    {
      "turn": 1,
      "type": "end_turn"
    }
  ],
  "events": [],
  "final_state": {},
  "error": null
}
```

---

## Determinism 规则

Replay 必须基于核心逻辑，而不是 UI 表现。

正确：

```text
记录 BattleCommand
记录 BattleEvent
记录 seed
记录初始状态
```

错误：

```text
记录鼠标坐标
依赖动画播放完成时间
依赖 UI 节点顺序
依赖当前帧率
```

UI 可以用于展示 replay，但 replay 不应依赖 UI 才能运行。

---

## RNG 要求

所有影响玩法的随机必须来自统一 RNG。

Replay 必须记录：

```text
run_seed
battle_seed
随机流名称，如果有
当前启用的随机相关 feature flags
```

不允许在战斗、奖励、敌人行动中直接调用不可复现随机。

---

## 日志要求

出现 bug 时，日志应尽量包含：

```text
bug_context
scene_name
active_screen
run_seed
battle_seed
active_flags
last_command
last_event
current_phase
player_hp
enemy_hp
hand_ids
draw_pile_ids
discard_pile_ids
error_stack
```

Verbose log 可以更详细，但 release 默认不应开启。

---

## 修 bug 时的流程

AI 修复可复现 bug 时，应遵循：

```text
1. 明确复现步骤。
2. 如果可能，创建 replay 或测试 fixture。
3. 定位是数据问题、逻辑问题、UI 问题、RNG 问题还是存档问题。
4. 最小范围修复。
5. 添加回归测试或 replay case。
6. 说明如何验证 bug 不再出现。
```

如果无法创建自动测试，也应提供手动复现步骤。

---

## Regression Replay

重要 bug 修复后，应保存一个回归用例。

推荐路径：

```text
tests/replays/bug_YYYYMMDD_short_name.json
```

文档记录：

```text
bug 描述
原始复现步骤
修复文件
预期结果
```

这样以后 AI 改战斗系统时，可以重新跑这些 replay，避免老 bug 复发。

---

## Replay 版本

Replay 格式必须带版本：

```text
replay_version
```

如果玩法和数据格式大改导致旧 replay 不能运行，应：

```text
标记 obsolete
说明原因
必要时保留原始文件
不强行兼容所有开发期 replay
```

开发期 replay 可以失效，但必须知道为什么失效。

---

## 不允许的行为

AI 不得：

- 只根据猜测修 bug，不收集基础信息；
- 删除报错代码但不修根因；
- 为了让报错消失而吞掉异常；
- 静默忽略非法 command；
- 修改随机逻辑但不记录 seed 影响；
- 修复战斗 bug 但不提供复现步骤；
- 把 UI 动画当作战斗逻辑的一部分；
- 说“已测试”但没有实际运行或给出测试步骤。

---

## 用户只贴报错时

如果用户只贴了 Godot 报错，AI 应：

```text
1. 解释报错可能含义。
2. 找到最可能相关文件。
3. 请求最少必要补充信息。
4. 如果足够明确，给出最小修复方案。
5. 修复后要求用户重新运行同一场景。
```

不要立刻大范围重构。

---

## AI 完成 bug 修复后的输出

修 bug 后必须输出：

```text
Bug:
- 问题是什么
- 根因是什么

Fix:
- 修改了什么
- 为什么这样修

Reproduction:
- 原始复现步骤
- 修复后验证步骤

Replay / Test:
- 是否新增 replay
- 是否新增测试
- 如果没有，为什么

Rollback:
- 如何撤销本次修复

Risk:
- 可能影响哪些系统
```
````

---

这 6 个文件加上之后，你的项目规则会更完整，特别是补上了：

```text
AI 交接
Git 安全
授权来源
实验开关
本地化文案
数据校验
Bug 复现
```

下一步建议你让 Cursor 执行：

```text
请创建上述 6 个 .cursor/rules 文件。
如果相关 docs/data 目录不存在，只创建必要空目录，不要实现复杂系统。
完成后输出文件列表。
```