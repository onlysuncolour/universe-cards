有。你前面已经补了：

```text
03_project_memory_git_safety.mdc
04_legal_license_provenance.mdc
19_feature_flags_experiment_modes.mdc
25_localization_copywriting.mdc
31_data_validation_id_registry.mdc
34_bug_reproduction_replay.mdc
```

还建议补这 9 个：

```text
05_godot_version_dependency_policy.mdc
17_audio_asset_pipeline.mdc
18_binary_asset_storage_lfs.mdc
20_save_versioning_dev_save.mdc
26_accessibility_input.mdc
28_animation_vfx_guidelines.mdc
32_build_export_release.mdc
33_performance_budget.mdc
36_lore_content_pipeline.mdc
```

其中最关键的新增遗漏是：

```text
18_binary_asset_storage_lfs.mdc
20_save_versioning_dev_save.mdc
05_godot_version_dependency_policy.mdc
```

下面是可直接复制使用的 rule。

---

# 05_godot_version_dependency_policy.mdc

````mdc
---
description: Godot version lock, upgrade policy, plugin policy, and third-party dependency safety.
alwaysApply: true
---

# Godot Version and Dependency Policy

## 核心目标

本项目必须锁定 Godot 版本，避免 AI 使用错误版本 API，或随意引入插件导致项目不可维护。

AI 必须优先假设项目是一个长期维护的 Godot 游戏工程，而不是临时 Demo。

---

## Source of Truth

Godot 版本和依赖信息应记录在：

```text
docs/setup/godot_version.md
docs/legal/third_party_dependencies.md
project.godot
addons/
```

如果这些文件不存在，AI 在合适时机应建议创建。

---

## Godot 版本规则

项目必须明确记录：

```text
Godot 版本号
是否使用 .NET 版本
导出模板版本
主要开发平台
最后确认日期
```

示例：

```text
Godot Version: 4.3 stable
Variant: Standard, not .NET
Export Templates: 4.3 stable
```

AI 不得擅自建议升级 Godot 主版本。

升级 Godot 前必须说明：

```text
为什么升级
升级到哪个版本
可能破坏哪些 API
是否影响导出模板
是否需要备份
如何回滚
```

---

## API 使用规则

AI 写 Godot 代码时必须遵守当前项目 Godot 版本。

如果不确定 API 是否存在，应：

```text
说明不确定点
使用更稳妥的写法
避免依赖过新的 API
```

不要假设用户使用最新 Godot nightly 版本。

---

## 插件规则

默认策略：

```text
能不用插件就不用插件。
```

引入插件前必须说明：

```text
插件名称
用途
来源
许可证
版本
维护状态
是否必须
替代方案
移除成本
```

插件必须记录到：

```text
docs/legal/third_party_dependencies.md
```

未经用户确认，AI 不得擅自添加插件。

---

## addons 修改规则

如果项目中使用了 `addons/`，AI 不应随意修改第三方插件源码。

如果必须修改，应：

```text
说明修改原因
记录修改文件
说明后续插件升级风险
建议创建 patch 记录
```

推荐记录：

```text
docs/patches/
```

---

## 依赖删除规则

删除插件或依赖前必须检查：

```text
project.godot 是否引用
场景是否引用
脚本是否引用
资源是否引用
导出设置是否引用
```

完成后必须说明如何测试项目是否仍可运行。

---

## AI 完成相关修改后的输出

涉及 Godot 版本、插件、依赖时，AI 必须输出：

```text
Version Impact:
- 是否依赖特定 Godot 版本
- 是否需要用户确认本地版本

Dependency Impact:
- 是否新增依赖
- 是否删除依赖
- 是否涉及许可证

Test:
- 如何在 Godot 中验证
- 预期结果

Rollback:
- 如何撤销
```
````

---

# 17_audio_asset_pipeline.mdc

````mdc
---
description: AI audio generation, SFX/music/ambience prompt management, import paths, licensing, and Godot audio usage.
globs:
  - "docs/audio/**"
  - "assets/audio/**"
  - "data/audio/**"
  - "data/assets/**"
  - "src/audio/**"
  - "scenes/audio/**"
alwaysApply: false
---

# Audio Asset Pipeline

## 核心目标

本项目的音效、音乐、环境音可以由 AI 或第三方工具生成，但必须和美术素材一样可追溯、可管理、可导入、可替换。

音频不能只保存在聊天记录里。

---

## 推荐目录

```text
docs/audio/
  audio_style_bible.md
  prompt_library/
  asset_specs/
  generation_batches/

assets/audio/
  sfx/
  music/
  ambience/
  ui/

assets/source/audio/
  ai_generated/
  third_party/
  raw_exports/

data/audio/
  audio_manifest.json
```

---

## 音频类型

使用以下类型：

```text
sfx_ui
sfx_card
sfx_combat
sfx_ship
sfx_alert
music_menu
music_battle
music_map
ambience_space
ambience_ship
```

---

## 命名规则

音频 ID 使用小写下划线：

```text
sfx_ui_button_confirm
sfx_card_play_attack
sfx_combat_shield_hit
sfx_ship_engine_low_loop
music_battle_deep_space_01
ambience_ship_bridge_loop
```

---

## 每个音频素材必须记录

每个正式音频素材应有 spec：

```text
docs/audio/asset_specs/<category>/<audio_id>.md
```

记录：

```text
audio_id
audio_type
用途
状态
生成工具
模型名称/版本，如果有
prompt
negative prompt，如果有
生成日期
是否循环
推荐响度
推荐 bus
源文件路径
Godot 导入路径
许可证/商用状态
```

---

## 状态

音频状态使用：

```text
idea
prompt_draft
prompt_approved
generated_candidates
selected
approved
imported
deprecated
```

---

## AI 音频 Prompt 要求

生成音频 prompt 时必须说明：

```text
音频类型
情绪
长度
是否 loop
是否需要无缝循环
是否有强攻击瞬态
是否适合 UI
是否需要科幻质感
是否避免旋律
是否避免人声
```

示例：

```text
Short futuristic UI confirmation sound, 0.3 seconds, clean digital click with soft cyan holographic tone, no voice, no melody, no noise, suitable for repeated button presses.
```

---

## 技术建议

推荐格式：

```text
SFX: wav 或 ogg
Music: ogg
Loop ambience: ogg
```

推荐规范：

```text
SFX 长度尽量短
UI 音效不要刺耳
音乐必须记录是否可循环
Loop 音频必须测试首尾是否断裂
避免过大音量
避免低频轰鸣影响长期游玩
```

---

## Godot Audio Bus

音频应按用途分配 bus：

```text
Master
Music
SFX
UI
Ambience
```

不要所有声音都直接塞到 Master。

---

## 版权规则

禁止使用来源不明音频。

AI 生成音频也必须记录：

```text
工具
模型
授权状态
是否允许商用
```

不得生成明显模仿具体商业音乐、电影配乐、游戏音效的素材。

---

## AI 完成音频任务后的输出

必须输出：

```text
Audio:
- audio_id
- audio_type
- 推荐路径
- 是否 loop
- 推荐 bus

Prompt:
- 英文 prompt
- 中文解释

Import:
- Godot 导入路径
- 推荐格式

Legal:
- 授权状态
- 是否可商用
```
````

---

# 18_binary_asset_storage_lfs.mdc

````mdc
---
description: Binary asset storage policy, AI-generated candidates, Git LFS, raw files, imported files, and repository size control.
globs:
  - "assets/**"
  - "docs/art/**"
  - "docs/audio/**"
  - "data/assets/**"
  - ".gitignore"
  - ".gitattributes"
alwaysApply: false
---

# Binary Asset Storage and Git LFS

## 核心目标

本项目会产生大量 AI 图片、音频、候选图、源文件。必须避免 Git 仓库迅速膨胀。

AI 不应默认把所有 AI 生成候选图都加入 Git。

---

## 资产分层

推荐分为：

```text
assets/source/
  ai_generated/
  raw/
  third_party/

assets/
  art/
  audio/
  ui/
  cards/
  ships/
```

含义：

```text
assets/source/        原始文件、候选图、大图、工程源文件
assets/               Godot 实际使用的 approved/imported 素材
```

---

## Git 提交原则

默认提交：

```text
Godot 实际使用的 approved/imported 素材
素材 manifest
prompt spec
授权文档
必要的小型占位图
```

默认不提交：

```text
所有候选图
失败生成图
超大原图
PSD/Blend/源工程文件
临时导出文件
重复版本
```

如果需要提交大文件，必须先确认是否使用 Git LFS。

---

## 大文件阈值

建议规则：

```text
单文件 > 10MB：提交前必须提醒用户
单文件 > 50MB：不应普通 Git 提交，建议 Git LFS 或外部存储
大量候选图：默认不提交
```

---

## Git LFS 建议

如果用户决定使用 Git LFS，可管理：

```text
*.png
*.jpg
*.jpeg
*.webp
*.psd
*.wav
*.ogg
*.mp3
*.blend
*.kra
*.clip
```

但 AI 不得擅自执行 Git LFS 初始化或修改远程仓库设置。

需要用户确认后才可建议：

```text
git lfs install
git lfs track "*.png"
git lfs track "*.wav"
```

---

## .gitignore 建议

可以考虑忽略：

```text
assets/source/ai_generated/candidates/
assets/source/**/temp/
assets/source/**/rejected/
exports/
builds/
*.tmp
```

但忽略规则必须结合项目实际，不得误忽略 Godot 正式素材。

---

## AI 生成候选图规则

候选图可以记录路径，但不一定纳入版本库。

Prompt Spec 中应记录：

```text
candidate_01
candidate_02
selected
approved
imported
```

正式进入项目的流程：

```text
候选图
→ 用户选择
→ selected
→ 清理/裁切/压缩
→ approved
→ 导入 assets/
→ 更新 asset_manifest
```

---

## 文件命名

二进制素材文件名必须稳定：

```text
<asset_id>.png
<asset_id>_candidate_01.png
<asset_id>_selected.png
<asset_id>_source.png
```

不要使用：

```text
final.png
new.png
test.png
image123.png
```

---

## Release 规则

发布包不应包含：

```text
AI 候选图
废弃图
未授权素材
源工程文件
超大原始图
未使用音频
```

Release 前应检查：

```text
素材是否 approved/imported
文件大小是否合理
是否存在水印/logo
是否有许可证记录
```

---

## AI 完成素材相关任务后的输出

必须输出：

```text
Binary Asset Policy:
- 哪些文件建议提交
- 哪些文件建议不提交
- 是否需要 Git LFS
- 是否需要更新 .gitignore
- 是否需要更新 asset_manifest

Risk:
- 仓库体积风险
- 授权风险
- Release 包体风险
```
````

---

# 20_save_versioning_dev_save.mdc

````mdc
---
description: Save file versioning, dev save reset, migration policy, save compatibility, and safe persistence rules.
globs:
  - "src/save/**"
  - "src/core/save/**"
  - "data/save/**"
  - "docs/save/**"
  - "tests/save/**"
  - "project.godot"
alwaysApply: false
---

# Save Versioning and Dev Save Policy

## 核心目标

开发期玩法会频繁变化，存档一定会坏。必须提前设计存档版本和清理策略，避免旧存档导致莫名报错。

---

## Source of Truth

存档规则记录在：

```text
docs/save/save_policy.md
docs/save/save_format.md
src/core/save/
```

如果不存在，AI 在实现存档前应建议创建。

---

## Save 文件必须带版本

所有正式 save 数据必须包含：

```text
save_version
game_version
created_at
updated_at
data_versions
feature_flags
```

示例：

```json
{
  "save_version": 1,
  "game_version": "0.1.0-dev",
  "created_at": "YYYY-MM-DDTHH:MM:SS",
  "data_versions": {
    "cards": 1,
    "enemies": 1
  },
  "feature_flags": {},
  "payload": {}
}
```

---

## 开发期策略

开发期允许不兼容旧存档，但必须：

```text
明确说明旧存档不兼容
提供清空 dev save 的方法
避免游戏启动直接崩溃
给出清晰日志
```

推荐提供：

```text
清空开发存档按钮
命令式 reset_dev_save
检测版本不兼容后移动到 backup
```

---

## 正式期策略

正式版本之后，修改存档格式必须考虑：

```text
是否需要 migration
是否兼容旧版本
是否保留备份
失败时如何恢复
```

不得静默丢弃玩家正式存档。

---

## 存档内容规则

存档中应保存稳定 ID 和数据，不应保存 UI 节点引用。

允许：

```text
card_id
card_instance_id
ship_id
module_id
run_seed
battle_seed
player_state
deck_list
unlocked_items
```

禁止：

```text
NodePath 作为核心数据
Control 节点状态作为核心数据
临时 UI index 作为长期数据
直接保存不可控 Resource 引用
```

---

## Feature Flags 和 Save

如果某个实验功能会影响存档结构，save 中必须记录：

```text
active feature flags
combat model
run model
data version
```

Prototype 功能默认不应写入正式存档。

---

## 旧存档处理

当检测到不兼容存档：

```text
输出清晰 warning
不要直接崩溃
提供删除/备份路径
开发期可以提示用户清空存档
```

推荐备份：

```text
user://save_backups/
```

---

## ID 重命名影响

如果重命名：

```text
card_id
enemy_id
status_id
module_id
asset_id
```

必须考虑：

```text
旧存档是否引用
是否需要 deprecated 映射
是否需要 migration
```

---

## AI 修改存档相关代码后必须输出

```text
Save Impact:
- 是否改变 save_version
- 是否兼容旧存档
- 是否需要 migration
- 是否需要清空 dev save

Test:
- 新存档测试
- 读旧存档测试
- 删除/清空存档测试

Rollback:
- 如何恢复旧存档格式
```
````

---

# 26_accessibility_input.mdc

````mdc
---
description: Accessibility, readable UI, keyboard/controller input, focus navigation, motion reduction, and input mapping.
globs:
  - "src/ui/**"
  - "scenes/ui/**"
  - "themes/**"
  - "docs/ui/**"
  - "project.godot"
  - "data/settings/**"
alwaysApply: false
---

# Accessibility and Input

## 核心目标

游戏 UI 必须优先保证可读、可操作、可测试。科幻风格不能牺牲文字清晰度和交互反馈。

---

## 文本可读性

UI 文本必须注意：

```text
字体足够大
对比度足够高
中文清晰
不要长时间使用发光文字
不要把正文放在复杂背景上
```

卡牌描述、按钮、提示文本尤其需要检查小分辨率。

---

## 颜色规则

重要信息不能只靠颜色区分。

例如：

```text
危险状态不能只用红色
增益/减益需要图标或文字辅助
按钮禁用状态需要透明度/图标/文本变化
```

避免红绿作为唯一差异。

---

## 输入规则

不要在代码中到处硬编码键盘按键。

推荐使用 Godot InputMap：

```text
ui_confirm
ui_cancel
ui_left
ui_right
ui_up
ui_down
game_end_turn
game_open_deck
game_open_map
game_pause
```

---

## 鼠标和键盘

主要 UI 操作应支持：

```text
鼠标点击
键盘确认/取消
键盘焦点移动
Esc 返回或关闭弹窗
Enter/Space 确认
```

至少主菜单、设置、战斗关键按钮应可键盘操作。

---

## Focus 规则

所有重要按钮必须有清晰 focus 状态。

弹窗打开时：

```text
焦点应进入弹窗
Esc 可关闭，如果允许
关闭后焦点回到合理位置
```

---

## 手柄预留

即使初期不支持手柄，也不要写死只能鼠标操作。

如果后续支持手柄，UI 不应需要完全重写。

---

## 动效可访问性

强动效应可降低或关闭：

```text
屏幕震动
强闪白
快速缩放
高频闪烁
```

设置项可以预留：

```text
reduce_motion
screen_shake_intensity
flash_intensity
```

---

## 音频可访问性

重要提示不要只靠音效。

例如：

```text
敌人意图变化
回合结束
资源不足
错误操作
```

应有视觉提示或文字提示。

---

## AI 修改 UI/Input 后必须输出

```text
Accessibility:
- 文本是否清晰
- 是否只靠颜色表达信息
- 是否有 hover/focus/disabled 状态

Input:
- 是否使用 InputMap
- 鼠标操作是否可用
- 键盘操作是否可用
- Esc/确认/取消是否合理

Risk:
- 小分辨率风险
- 长文本风险
- 强动效风险
```
````

---

# 28_animation_vfx_guidelines.mdc

````mdc
---
description: Animation, VFX, feedback timing, screen shake, reduce motion, and separation between visual effects and game logic.
globs:
  - "src/vfx/**"
  - "src/animation/**"
  - "src/ui/**"
  - "scenes/vfx/**"
  - "scenes/ui/**"
  - "assets/vfx/**"
  - "docs/vfx/**"
alwaysApply: false
---

# Animation and VFX Guidelines

## 核心目标

动画和 VFX 用于表现反馈，不应承载核心游戏逻辑。

战斗逻辑应产生 BattleEvent，VFX 只消费事件并播放表现。

---

## 逻辑和表现分离

正确流程：

```text
玩家操作
→ BattleCommand
→ BattleSystem 更新状态
→ 产生 BattleEvent
→ UI/VFX 播放表现
```

禁止：

```text
动画播放完才真正造成伤害
粒子碰撞决定战斗结果
UI tween 内修改核心血量
用动画节点作为战斗状态源
```

可以等待动画结束再进入下一段表现，但核心状态必须已经由逻辑系统决定。

---

## 推荐动画时长

建议：

```text
按钮 hover: 0.08 - 0.15s
按钮点击: 0.05 - 0.12s
卡牌 hover: 0.10 - 0.20s
卡牌打出: 0.20 - 0.45s
伤害数字: 0.30 - 0.70s
护盾闪光: 0.15 - 0.35s
敌人受击: 0.10 - 0.25s
界面切换: 0.20 - 0.50s
```

不要让常见操作被长动画拖慢。

---

## 可跳过原则

频繁播放的动画应能：

```text
加速
跳过
快速结算
在 reduce_motion 下简化
```

卡牌游戏中，重复操作不能因为动画过长变得疲劳。

---

## 屏幕震动规则

屏幕震动只能用于重要反馈：

```text
大伤害
飞船爆炸
Boss 攻击
系统警报
```

默认强度要克制。

必须避免：

```text
每次小伤害都震
持续震动
高频震动
无法关闭
```

---

## 闪光规则

避免高频闪烁和大面积纯白闪光。

危险警告应优先使用：

```text
边框闪烁
图标提示
轻微色彩变化
声音辅助
```

---

## VFX 命名

推荐：

```text
vfx_card_play_attack
vfx_card_play_skill
vfx_damage_hit_small
vfx_damage_hit_large
vfx_shield_gain
vfx_ship_explosion_small
vfx_warning_alert
```

---

## VFX 资源路径

推荐：

```text
assets/vfx/
scenes/vfx/
src/vfx/
docs/vfx/
```

---

## Reduce Motion

如果项目设置中存在：

```text
reduce_motion
screen_shake_intensity
flash_intensity
```

VFX 必须遵守。

没有设置时，新增强动效应提示用户是否需要设置项。

---

## AI 修改动画/VFX 后必须输出

```text
VFX:
- 表现了什么事件
- 是否依赖 BattleEvent
- 是否修改核心逻辑

Timing:
- 动画时长
- 是否可跳过
- 是否影响操作节奏

Accessibility:
- 是否有强闪光
- 是否有屏幕震动
- 是否支持 reduce_motion

Test:
- 如何触发
- 预期表现
```
````

---

# 32_build_export_release.mdc

````mdc
---
description: Build, export, release checklist, versioning, debug/release separation, export artifacts, and release safety.
globs:
  - "export_presets.cfg"
  - "project.godot"
  - "docs/release/**"
  - "builds/**"
  - "exports/**"
  - "data/dev/**"
  - "data/assets/**"
alwaysApply: false
---

# Build, Export, and Release

## 核心目标

发布包必须可追溯、可复现、可检查。Debug 功能、临时素材、未授权资源、prototype 功能不能进入正式版本。

---

## 推荐目录

```text
docs/release/
  build_checklist.md
  version_history.md
  release_notes/
  smoke_test_checklist.md

builds/
exports/
```

`builds/` 和 `exports/` 通常不应提交到 Git，除非用户明确决定。

---

## 版本号

每次准备发布应记录：

```text
game_version
build_number
release_date
target_platform
Godot version
export template version
```

示例：

```text
0.1.0-dev
0.1.0-alpha.1
0.1.0-demo
```

---

## Debug 和 Release 区分

Release 中不应默认启用：

```text
verbose log
testbed screen
debug buttons
prototype feature flags
未授权占位素材
作弊快捷键
调试 overlay
```

如果保留 debug 功能，必须明确隐藏或关闭。

---

## 发布前检查

Release 前必须检查：

```text
项目能启动
主菜单可进入
战斗可开始
关键 UI 无报错
data validator 通过
缺失 localization key 检查
asset_manifest 检查
没有 blocked_for_release 素材
没有来源不明字体/音频/图片
没有大规模 warning
```

---

## Feature Flag 检查

发布前必须确认：

```text
prototype flags 默认关闭
release_allowed=false 的功能没有开启
当前 combat/run/ui 模式明确
```

---

## 素材检查

发布包不应包含：

```text
AI 候选图
废弃素材
raw PSD/Blend
临时占位图
未授权音频
未授权字体
```

---

## Export Preset 修改规则

修改 `export_presets.cfg` 前必须说明：

```text
目标平台
为什么修改
是否影响已有导出
是否需要导出模板
```

不要随意破坏已有 export preset。

---

## Smoke Test

每次 Release Candidate 至少手动测试：

```text
启动游戏
进入主菜单
开始新游戏
进入一场战斗
打出一张牌
结束回合
打开设置
退出到菜单
关闭游戏
```

如果有存档：

```text
保存
读档
清空 dev save
```

---

## AI 完成构建/发布相关修改后必须输出

```text
Build Impact:
- 是否修改导出配置
- 是否修改版本号
- 是否影响 release

Checklist:
- 已检查项
- 未检查项
- 需要用户手动测试项

Risk:
- Debug 功能是否可能泄漏
- 未授权素材风险
- Prototype flag 风险

Rollback:
- 如何恢复旧配置
```
````

---

# 33_performance_budget.mdc

````mdc
---
description: Performance budget, texture sizes, UI node count, logging cost, loading strategy, memory use, and profiling discipline.
globs:
  - "assets/**"
  - "scenes/**"
  - "src/**"
  - "docs/performance/**"
  - "project.godot"
alwaysApply: false
---

# Performance Budget

## 核心目标

即使是 2D 卡牌游戏，也要控制性能和内存，尤其是：

```text
AI 大图
大量 UI 节点
粒子和 shader
verbose log
重复创建节点
运行时加载资源
```

---

## 基准目标

默认目标：

```text
桌面端 60 FPS
UI 操作无明显卡顿
战斗动画不卡顿
切换界面不长时间冻结
```

具体目标可记录在：

```text
docs/performance/performance_budget.md
```

---

## 图片尺寸建议

建议：

```text
卡牌插画 source: 1024x1536 或更高
卡牌游戏内使用: 512x768 或按 UI 需求压缩
飞船 source: 1024x1024
飞船游戏内使用: 512x512 或更小
UI icon: 64x64 / 128x128 / 256x256
背景: 1920x1080 或 2560x1440，避免无必要 4K
```

不要在运行时直接使用超大 AI 原图。

---

## Texture 导入规则

导入前应考虑：

```text
是否需要透明
是否需要 mipmap
是否需要压缩
是否是 UI 图
是否会频繁显示
```

卡牌、UI、图标应优先保证清晰；背景和大图应控制内存。

---

## UI 性能规则

避免：

```text
每帧重建整个手牌
每帧重新计算全部布局
大量隐藏节点不释放
无限创建弹窗
每次 hover 都加载资源
```

推荐：

```text
复用卡牌 UI 节点
只在数据变化时刷新
大列表分页或虚拟化
关闭界面时释放不需要的节点
```

---

## _process 使用规则

不要随意给大量节点添加 `_process(delta)`。

如果只是响应事件，应使用：

```text
signal
callback
timer
state change
```

---

## 日志性能

Verbose log 不应在 Release 默认开启。

高频逻辑中不要每帧输出大量日志。

战斗日志应可开关，并区分：

```text
INFO
DEBUG
WARNING
ERROR
```

---

## 加载策略

大资源加载应考虑：

```text
预加载
延迟加载
缓存
释放
加载界面
```

不要在关键点击瞬间同步加载大量图片或音频。

---

## Profiling 原则

优化前优先确认瓶颈。

AI 不应为了“可能更快”进行大规模复杂优化。

性能修改应说明：

```text
瓶颈假设
修改点
如何验证
可能副作用
```

---

## AI 完成性能相关修改后必须输出

```text
Performance:
- 解决了什么性能风险
- 是否减少内存/节点/加载/日志

Test:
- 如何观察 FPS 或卡顿
- 哪个场景测试
- 预期表现

Risk:
- 是否影响画质
- 是否影响 UI 行为
- 是否影响加载流程
```
````

---

# 36_lore_content_pipeline.mdc

````mdc
---
description: Lore bible, naming rules, factions, terminology, event writing, world consistency, and content pipeline.
globs:
  - "docs/content/**"
  - "data/gameplay/**"
  - "data/localization/**"
  - "docs/art/**"
  - "docs/audio/**"
alwaysApply: false
---

# Lore and Content Pipeline

## 核心目标

本项目需要统一的世界观、命名规则和内容语气，避免 AI 每次新增卡牌、敌人、派系、事件时风格混乱。

---

## Source of Truth

内容设定集中在：

```text
docs/content/lore_bible.md
docs/content/factions.md
docs/content/glossary.md
docs/content/naming_rules.md
docs/content/technology_terms.md
docs/content/event_writing_guide.md
```

如果不存在，AI 在新增大量内容前应建议创建。

---

## 世界观一致性

新增内容必须检查是否符合：

```text
项目科幻基调
已有派系
已有科技设定
已有术语
卡牌命名风格
敌人命名风格
事件文本语气
```

不要随意引入和项目风格冲突的元素，例如：

```text
中世纪魔法
现代都市黑帮
无解释的奇幻神明
直接借用知名科幻 IP 名词
```

除非用户明确要求改变世界观方向。

---

## 命名规则

正式 ID 使用英文小写下划线。

玩家可见名称使用 localization。

示例：

```text
card_plasma_shot
enemy_raider_drone
faction_outer_ring_union
event_abandoned_relay
module_shield_generator_mk1
```

玩家可见名称：

```text
等离子射击
掠袭无人机
外环联合体
废弃中继站
Mk1 护盾发生器
```

---

## 术语统一

术语必须记录在：

```text
docs/content/glossary.md
```

例如：

```text
护盾
舰体
能量
热量
模块
过热
干扰
校准
裂隙
遗物
航道
```

不要混用多个词表达同一机制。

---

## 派系规则

新增派系时必须记录：

```text
faction_id
中文名
英文名
视觉关键词
科技风格
行为模式
常见敌人
常见卡牌/模块关联
禁用元素
```

派系应影响：

```text
敌人设计
飞船设计
卡牌名称
事件文本
美术 prompt
音频风格
```

---

## 卡牌内容规则

新增卡牌时应同时考虑：

```text
card_id
name_key
desc_key
机制效果
美术 asset_id
是否符合术语
是否符合派系或科技来源
```

不要只新增玩法效果而没有命名和文案来源。

---

## 事件文本规则

事件文本应：

```text
短而有画面感
选项清晰
后果可理解
语气克制
避免过长段落
避免网络梗
避免直接解释所有设定
```

事件逻辑和文本必须分离。

---

## 美术 Prompt 和 Lore

生成美术 prompt 时，应参考：

```text
派系视觉关键词
科技术语
飞船功能
卡牌来源
星域环境
```

不要让同一派系的飞船、模块、卡牌完全没有视觉联系。

---

## 版权安全

禁止直接使用：

```text
知名科幻 IP 名词
商业游戏派系名
电影飞船名
真实品牌标志
```

应使用原创描述和原创命名。

---

## AI 新增内容后必须输出

```text
Content:
- 新增了什么
- 属于哪个派系/系统
- 是否新增术语

Localization:
- name_key
- desc_key
- event text key

Lore:
- 是否需要更新 lore_bible
- 是否需要更新 glossary
- 是否需要更新 factions

Risk:
- 是否和已有设定冲突
- 是否有版权/IP 风险
```
````

---

# 建议最终规则清单

如果你把上面都加上，规则体系会比较完整：

```text
03_project_memory_git_safety.mdc
04_legal_license_provenance.mdc
05_godot_version_dependency_policy.mdc
16_ai_asset_prompt_management.mdc
17_audio_asset_pipeline.mdc
18_binary_asset_storage_lfs.mdc
19_feature_flags_experiment_modes.mdc
20_save_versioning_dev_save.mdc
25_localization_copywriting.mdc
26_accessibility_input.mdc
28_animation_vfx_guidelines.mdc
31_data_validation_id_registry.mdc
32_build_export_release.mdc
33_performance_budget.mdc
34_bug_reproduction_replay.mdc
36_lore_content_pipeline.mdc
```

你可以直接让 Cursor 执行：

```text
请创建以下 .cursor/rules 文件，并写入我提供的内容：
05_godot_version_dependency_policy.mdc
17_audio_asset_pipeline.mdc
18_binary_asset_storage_lfs.mdc
20_save_versioning_dev_save.mdc
26_accessibility_input.mdc
28_animation_vfx_guidelines.mdc
32_build_export_release.mdc
33_performance_budget.mdc
36_lore_content_pipeline.mdc

如果相关 docs/data/assets 目录不存在，只创建必要空目录，不要实现复杂系统。
完成后输出 Changed Files、How to Test、Rollback。
```