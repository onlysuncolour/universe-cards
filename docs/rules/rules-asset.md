> 已经完成


对，这个也应该单独形成一套系统。你需要的不是“临时让 AI 写几个 prompt”，而是：

> **AI 美术素材 Prompt 管理系统 / AI Art Asset Pipeline**

它应该统一管理：

1. 全局美术风格；
2. 各类素材的基础 prompt；
3. 每个具体素材的 prompt；
4. 生成工具、尺寸、seed、版本；
5. 最终选中的图片文件；
6. Godot 导入路径；
7. 后续重生成、改风格、回溯来源。

不要把这些 prompt 散落在聊天记录里，否则后面素材多了会失控。

---

# 一、建议新增目录

在项目中新增：

```text
docs/
  art/
    style_bible.md
    prompt_library/
      00_global_style_prompt.md
      01_global_negative_prompt.md
      10_category_prompts.md
    asset_specs/
      ships/
      ship_modules/
      cards/
      card_frames/
      icons/
      backgrounds/
      ui_panels/
    generation_batches/
    prompt_template.md

data/
  assets/
    asset_manifest.json

assets/
  source/
    ai_generated/
      ships/
      ship_modules/
      cards/
      backgrounds/
  ships/
  ship_modules/
  cards/
  backgrounds/
  icons/
```

含义：

```text
docs/art/style_bible.md
```

管理全局风格。

```text
docs/art/prompt_library/
```

管理可复用 prompt 模板。

```text
docs/art/asset_specs/
```

管理每一个具体素材的 prompt。

```text
data/assets/asset_manifest.json
```

管理素材 ID、状态、文件路径、prompt 文档路径。

```text
assets/source/ai_generated/
```

存 AI 生成的原始大图或候选图。

```text
assets/
```

存 Godot 实际使用的优化后素材。

---

# 二、建议新增 Rule

新增：

```text
.cursor/rules/16_ai_asset_prompt_management.mdc
```

内容如下，可直接复制。

---

## `.cursor/rules/16_ai_asset_prompt_management.mdc`

```md
---
description: AI-generated art asset prompt management and asset pipeline.
alwaysApply: true
---

# AI Asset Prompt Management

## 核心目标

本项目大量美术素材会使用 AI 生成，包括：

- 飞船
- 飞船模块
- 卡牌插画
- 卡牌框
- UI 图标
- 背景
- 敌方单位
- 星图节点
- 科技终端元素

所有 AI 生成素材都必须有可追溯的 prompt、风格版本和文件记录。

禁止只在聊天记录中临时保存 prompt。

## Source of Truth

AI 美术素材管理的核心文件：

```text
docs/art/style_bible.md
docs/art/prompt_library/
docs/art/asset_specs/
data/assets/asset_manifest.json
assets/source/ai_generated/
assets/
```

## Prompt 分层原则

每个具体素材的 prompt 必须由以下层级组合：

```text
全局美术风格 prompt
+ 类别 prompt
+ 具体素材描述
+ 技术约束
+ negative prompt
```

示例：

```text
Global Style Prompt
+ Spaceship Category Prompt
+ "frontier survey corvette, small exploration ship..."
+ "transparent background, 3/4 top-down orthographic view..."
+ Negative Prompt
```

AI 不应每次重新发明风格。

## 风格版本

全局风格必须有版本号，例如：

```text
art_style_v0.1
art_style_v0.2
```

每个素材 prompt 必须记录自己基于哪个风格版本。

如果全局风格大改：

- 不强制立即重做所有旧素材；
- 新素材使用新风格版本；
- 旧素材如果需要统一，再单独创建 regeneration batch。

## 每个素材必须有 asset_id

命名规则：

```text
<type>_<theme>_<name>_<variant>
```

示例：

```text
ship_player_survey_corvette
ship_enemy_raider_drone
module_shield_generator_mk1
card_art_plasma_shot
card_art_emergency_repair
icon_energy
bg_deep_space_nebula_01
```

使用小写和下划线。

## 每个 AI 素材必须有 Prompt Spec

每个具体素材必须有一个文档：

```text
docs/art/asset_specs/<category>/<asset_id>.md
```

例如：

```text
docs/art/asset_specs/ships/ship_player_survey_corvette.md
docs/art/asset_specs/cards/card_art_plasma_shot.md
```

该文档必须记录：

- asset_id
- asset_type
- 用途
- 状态
- 风格版本
- prompt 版本
- 正向 prompt
- negative prompt
- 生成工具
- 尺寸/比例
- seed，如果有
- 参考图，如果有
- 候选图路径
- 选中图路径
- Godot 导入路径
- 修改历史

## 素材状态

素材状态必须使用以下之一：

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

含义：

- `idea`：只有想法。
- `prompt_draft`：prompt 草稿。
- `prompt_approved`：prompt 已确认，可生成图片。
- `generated_candidates`：已有候选图。
- `selected`：用户选中了某张。
- `approved`：用户确认可进入项目。
- `imported`：已导入 Godot。
- `deprecated`：废弃但保留记录。

## AI 生成 prompt 时必须输出

当用户要求生成素材 prompt 时，AI 必须输出：

1. asset_id 建议。
2. asset_type。
3. 使用的风格版本。
4. 适合复制到图片生成工具的英文 prompt。
5. 中文解释。
6. negative prompt。
7. 推荐尺寸和比例。
8. 推荐是否透明背景。
9. 2 到 4 个变体 prompt，如果适合。
10. 建议保存到哪个 Prompt Spec 文件。
11. 如果需要，更新 asset_manifest 的建议项。

## 英文 Prompt 优先

图片生成用的 prompt 优先使用英文。

可以同时提供中文说明，但正式 prompt 应使用英文，因为多数图像模型对英文视觉描述更稳定。

## 版权和风格安全

禁止在 prompt 中直接要求：

- 复制某个商业游戏的具体飞船；
- 复制某个具体电影、动漫、游戏的专有设计；
- 使用在世艺术家的名字作为风格；
- 生成带有真实商标、logo、水印的图；
- 直接复刻参考游戏截图。

允许使用描述性风格，例如：

```text
semi-realistic 2D sci-fi game art
hard-surface spaceship design
cold cyan emissive lights
dark gunmetal panels
deep space atmosphere
```

不要写：

```text
in the style of <specific living artist>
same as <specific commercial game>
```

## 类别要求

### 飞船素材

飞船 prompt 应明确：

- 视角：推荐 3/4 top-down orthographic 或 side view。
- 背景：是否透明。
- 轮廓：小图标下也要可读。
- 材质：金属、装甲板、发光引擎。
- 阵营或用途：探索、掠夺、军用、科研、商船。
- 不要生成文字、logo、水印。

### 飞船模块素材

模块 prompt 应明确：

- 模块功能：护盾、引擎、武器、反应堆、医疗舱等。
- 是否作为 UI 图标。
- 是否透明背景。
- 是否需要与飞船主体风格一致。
- 小尺寸下要清晰。

### 卡牌插画

卡牌插画 prompt 应明确：

- 插画比例，推荐 2:3 或 3:4。
- 不要把卡牌名称、费用、说明文字画进图片。
- 不要直接生成完整卡牌 UI，除非是 card_frame。
- 画面主体要清楚，留出裁切空间。
- 风格应与 UI 和科幻世界观一致。

### 卡牌框

卡牌框和卡牌插画必须分开。

卡牌框 prompt 应明确：

- 是 UI frame，不是插画。
- 是否透明中间区域。
- 是否包含费用槽、标题区、说明区。
- 不包含具体卡牌文字。
- 适合 Godot 叠加 Label 和图标。

### UI 图标

UI 图标 prompt 应明确：

- 简洁；
- 高对比；
- 小尺寸可读；
- 推荐 SVG 风格或单色/双色；
- 不要复杂背景。

### 背景

背景 prompt 应明确：

- 是否用于主菜单、星图、战斗、事件；
- 是否需要留出 UI 可读空间；
- 不要过亮；
- 不要有大面积高频细节干扰文字。

## Card Art 特别规则

卡牌相关素材必须区分：

```text
card_art_xxx       纯插画
card_frame_xxx     卡牌框
card_icon_xxx      卡牌小图标
card_mock_xxx      临时占位
```

禁止把卡牌文字直接烘焙进 card_art 图片。

卡牌文字、费用、数值应由 Godot UI 渲染，方便后续改数值和本地化。

## 更新 asset_manifest

当素材进入 `selected`、`approved` 或 `imported` 状态时，应更新：

```text
data/assets/asset_manifest.json
```

manifest 至少应记录：

```json
{
  "asset_id": "card_art_plasma_shot",
  "asset_type": "card_art",
  "status": "approved",
  "style_version": "art_style_v0.1",
  "prompt_doc": "docs/art/asset_specs/cards/card_art_plasma_shot.md",
  "source_files": [],
  "imported_files": []
}
```

## Godot 导入前

导入 Godot 前必须确认：

- 用户已选择或批准素材；
- 素材来源和 prompt 有记录；
- 文件命名规范；
- 尺寸合理；
- 不包含不需要的文字、水印、logo；
- 需要透明背景的素材确实有透明背景；
- 卡牌插画没有烘焙 UI 文字。

## 用户请求素材时的默认流程

当用户说：

```text
帮我生成一个飞船素材 prompt
```

AI 应该：

1. 判断素材类型。
2. 如果信息不足，做合理假设并列出假设。
3. 生成 asset_id。
4. 读取或遵循 style_bible。
5. 生成 Prompt Spec。
6. 给出可复制的英文 prompt。
7. 给出 negative prompt。
8. 给出推荐尺寸。
9. 不直接导入 Godot，除非用户明确要求。

## 批量生成

批量素材应放在：

```text
docs/art/generation_batches/
```

批量文档应记录：

- 批次目标；
- 涉及 asset_id；
- 是否同一风格版本；
- 每个素材的 prompt spec 路径；
- 当前状态；
- 用户选择结果。
```

---

# 三、Prompt Spec 模板

新增：

```text
docs/art/prompt_template.md
```

内容如下。

```md
# Asset Prompt Spec: <asset_id>

## Basic Info

```yaml
asset_id: <asset_id>
asset_type: <ship | ship_module | card_art | card_frame | icon | background | enemy | ui_panel>
status: idea
style_version: art_style_v0.1
prompt_version: v0.1
created_at: YYYY-MM-DD
updated_at: YYYY-MM-DD
```

## Purpose

用途：

```text
说明这个素材会在哪里使用。
例如：玩家初始探索飞船，在主菜单、战斗界面和舰船管理界面中显示。
```

## Visual Brief

中文视觉描述：

```text
这里写给人看的描述。
```

## Global Style Reference

使用：

```text
docs/art/style_bible.md
docs/art/prompt_library/00_global_style_prompt.md
docs/art/prompt_library/01_global_negative_prompt.md
docs/art/prompt_library/10_category_prompts.md
```

## Positive Prompt

```text
English prompt goes here.
```

## Negative Prompt

```text
Negative prompt goes here.
```

## Recommended Generation Settings

```yaml
tool: generic
aspect_ratio: 1:1
target_size: 1024x1024
transparent_background: true
seed: optional
variants: 4
```

## Prompt Variants

### Variant A

```text
...
```

### Variant B

```text
...
```

### Variant C

```text
...
```

## Candidate Outputs

```text
assets/source/ai_generated/<category>/<asset_id>_candidate_01.png
assets/source/ai_generated/<category>/<asset_id>_candidate_02.png
```

## Selected Output

```text
assets/source/ai_generated/<category>/<asset_id>_selected.png
```

## Godot Import Path

```text
assets/<category>/<asset_id>.png
```

## Approval Checklist

- [ ] 风格符合项目整体方向
- [ ] 没有文字、水印、logo
- [ ] 小尺寸下轮廓清晰
- [ ] 颜色不会和 UI 冲突
- [ ] 如果是卡牌插画，没有烘焙卡牌文字
- [ ] 如果需要透明背景，背景确实透明
- [ ] 文件名符合规范
- [ ] 已更新 asset_manifest

## History

```text
v0.1 - initial prompt draft
```
```

---

# 四、全局风格文档模板

新增：

```text
docs/art/style_bible.md
```

可以先用这个版本。

```md
# Art Style Bible

## Style Version

```text
art_style_v0.1
```

## Project Art Direction

本项目是 2D 科幻宇宙卡牌构筑 Roguelite。

整体视觉关键词：

- 深空
- 舰船终端
- 冷色科技感
- 半透明 UI
- 硬表面飞船
- 暗色金属
- 青蓝色发光
- 低饱和背景
- 高可读性轮廓
- 神秘未知文明

## Global Positive Prompt

```text
semi-realistic 2D sci-fi game art, original space exploration setting, dark deep-space atmosphere, hard-surface industrial design, dark gunmetal materials, subtle worn metal panels, cyan and blue emissive lights, clean readable silhouettes, high contrast focal point, cinematic but functional design, premium indie game visual style, detailed but not noisy, consistent cold color palette, no text, no watermark, no logo
```

## Global Negative Prompt

```text
text, watermark, logo, signature, brand name, copyrighted emblem, blurry, low resolution, messy silhouette, overly busy details, unreadable small shapes, cartoonish, cute mascot style, anime school style, medieval fantasy, modern city, photorealistic humans, distorted perspective, broken geometry, extra random parts, duplicated objects, oversaturated colors, UI text baked into image
```

## Color Direction

主要颜色：

```text
deep navy
near-black blue
dark gunmetal
desaturated steel gray
cyan emissive light
electric blue accent
small orange warning lights
danger red only for alerts
```

避免：

```text
大面积纯白
大面积高饱和红色
过度彩虹色
太卡通的糖果色
```

## Shape Language

飞船和模块：

```text
functional, modular, asymmetric but balanced, readable silhouette, visible engines, armor plates, sensor arrays, weapon hardpoints, maintenance panels
```

UI 和卡牌：

```text
thin glowing borders, semi-transparent panels, beveled corners, holographic terminal feeling, clean layout, readable text area
```

## Perspective Rules

飞船默认：

```text
3/4 top-down orthographic view
```

卡牌插画默认：

```text
vertical illustration, 2:3 aspect ratio
```

UI 图标默认：

```text
simple centered icon, transparent background, readable at 64px
```

## Asset Separation Rules

卡牌相关素材必须拆分：

```text
card_art: only illustration
card_frame: UI frame only
card_icon: small icon
text: rendered in Godot, not baked into image
```

## Change Log

```text
art_style_v0.1 - initial style direction
```
```

---

# 五、类别 Prompt 文档

新增：

```text
docs/art/prompt_library/10_category_prompts.md
```

内容：

```md
# Category Prompt Library

## Spaceship Prompt

用于飞船主体。

```text
3/4 top-down orthographic view of a sci-fi spaceship, isolated game asset, transparent background, hard-surface metal hull, modular armor plates, visible engines, small cyan emissive windows and thrusters, readable silhouette for 2D gameplay, original design, no text, no logo
```

推荐比例：

```text
1:1 或 4:3
```

## Ship Module Prompt

用于飞船模块、装备、舰船部件。

```text
isolated sci-fi spaceship module game asset, transparent background, hard-surface mechanical component, compact functional design, dark gunmetal material, cyan emissive details, readable at small size, no text, no logo
```

推荐比例：

```text
1:1
```

## Card Art Prompt

用于卡牌插画，不包含卡框和文字。

```text
vertical sci-fi card illustration, 2D semi-realistic game art, dramatic focal subject, deep space atmosphere, dark metallic sci-fi palette, cyan blue lighting, clear composition, no card frame, no title, no numbers, no UI text, no watermark
```

推荐比例：

```text
2:3 或 3:4
```

## Card Frame Prompt

用于卡牌框。

```text
sci-fi card frame UI asset, transparent center artwork area, separate title area, cost slot, description area, thin cyan glowing borders, dark metal panel, holographic terminal style, no text, no numbers, no logo
```

推荐比例：

```text
2:3
```

## UI Icon Prompt

用于 UI 图标。

```text
minimal sci-fi UI icon, transparent background, centered symbol, cyan blue line art, high contrast, readable at 32px and 64px, no text, no logo, clean silhouette
```

推荐比例：

```text
1:1
```

## Background Prompt

用于背景。

```text
deep space sci-fi background, dark navy and black color palette, subtle nebula clouds, distant stars, faint cyan light, low contrast background suitable for UI overlay, not too bright, not too busy, no text, no watermark
```

推荐比例：

```text
16:9
```
```

---

# 六、Asset Manifest 示例

新增：

```text
data/assets/asset_manifest.json
```

初始内容：

```json
{
  "version": 1,
  "style_version": "art_style_v0.1",
  "assets": []
}
```

后续添加素材时变成：

```json
{
  "version": 1,
  "style_version": "art_style_v0.1",
  "assets": [
    {
      "asset_id": "ship_player_survey_corvette",
      "asset_type": "ship",
      "status": "prompt_draft",
      "style_version": "art_style_v0.1",
      "prompt_doc": "docs/art/asset_specs/ships/ship_player_survey_corvette.md",
      "source_files": [],
      "imported_files": [],
      "notes": "Player starter ship."
    }
  ]
}
```

---

# 七、示例：让 AI 生成飞船 Prompt

你以后可以这样对 Cursor 说：

```text
请根据 docs/art/style_bible.md 和 prompt_library，
为玩家初始探索飞船生成一个 AI 美术素材 prompt spec。

要求：
1. asset_type = ship
2. asset_id = ship_player_survey_corvette
3. 用途：玩家初始飞船，探索型，小型护卫舰
4. 视角：3/4 top-down orthographic
5. 透明背景
6. 输出 3 个 prompt 变体
7. 不生成图片，只生成 prompt 文档
8. 更新 asset_manifest 建议项
```

AI 应该输出类似：

```text
asset_id: ship_player_survey_corvette
asset_type: ship
style_version: art_style_v0.1
recommended_path: docs/art/asset_specs/ships/ship_player_survey_corvette.md
```

正向 prompt：

```text
semi-realistic 2D sci-fi game art, 3/4 top-down orthographic view of a small frontier survey corvette spaceship, original design, compact exploration vessel, dark gunmetal hull, modular armor plates, visible sensor arrays, reinforced cockpit section, twin rear engines with cyan blue emissive thrusters, subtle worn metal edges, functional industrial hard-surface design, clean readable silhouette for 2D gameplay, isolated asset, transparent background, high quality, detailed but not noisy, no text, no logo, no watermark
```

negative prompt：

```text
text, watermark, logo, signature, brand name, blurry, low resolution, messy silhouette, overly busy details, cartoonish, cute mascot style, anime school style, fantasy medieval elements, modern car, airplane, copied famous spaceship, distorted perspective, broken geometry, oversaturated colors
```

---

# 八、示例：让 AI 生成卡牌插画 Prompt

你可以这样说：

```text
帮我生成一张卡牌插画的 prompt spec。

asset_id: card_art_plasma_shot
用途：攻击牌“等离子射击”的插画
要求：
1. 只生成插画，不要卡框
2. 不要文字、费用、说明
3. 2:3 竖图
4. 科幻战斗感
5. 风格符合 style_bible
```

AI 应该生成：

```text
vertical sci-fi card illustration, semi-realistic 2D game art, a focused burst of cyan plasma energy fired from a compact spaceship weapon, streaking through dark space toward an unseen target, glowing particles and subtle shockwave, deep navy background, dark gunmetal mechanical foreground details, high contrast focal point, cinematic but clean composition, suitable for a card game illustration, no card frame, no title, no numbers, no UI text, no watermark, no logo
```

注意这里强调：

```text
no card frame
no title
no numbers
no UI text
```

这样后续 Godot 可以自己渲染卡牌名称、费用和说明。

---

# 九、建议新增一个 Skill

如果你使用 skills，可以新增：

```text
docs/ai/skills/assets/create_ai_asset_prompt.md
```

内容：

```md
# Skill: Create AI Asset Prompt

当用户要求生成素材 prompt 时，按以下流程执行：

1. 判断 asset_type：
   - ship
   - ship_module
   - card_art
   - card_frame
   - icon
   - background
   - enemy
   - ui_panel

2. 生成或确认 asset_id。

3. 读取并遵守：
   - docs/art/style_bible.md
   - docs/art/prompt_library/00_global_style_prompt.md
   - docs/art/prompt_library/01_global_negative_prompt.md
   - docs/art/prompt_library/10_category_prompts.md

4. 创建 Prompt Spec：
   - docs/art/asset_specs/<category>/<asset_id>.md

5. Prompt 必须包含：
   - 英文正向 prompt
   - negative prompt
   - 中文解释
   - 推荐比例
   - 推荐尺寸
   - 是否透明背景
   - 2 到 4 个变体

6. 如果信息不足：
   - 不要阻塞；
   - 做合理假设；
   - 在文档中写明 assumptions。

7. 不要直接导入 Godot，除非用户明确要求。

8. 如果素材进入 selected / approved / imported 状态，更新：
   - data/assets/asset_manifest.json
```

---

# 十、这套系统的关键价值

这样做之后，你以后可以很自然地让 AI 工作：

```text
帮我生成 10 张初始牌组卡牌的 card_art prompt。
```

AI 会去：

```text
读取 style_bible
读取 card_art category prompt
生成 10 个 asset_id
为每张卡创建 prompt spec
写入 generation batch
给出可复制的英文 prompt
```

你也可以说：

```text
现在所有飞船看起来不够统一，帮我基于 art_style_v0.2 重写这些飞船 prompt。
```

AI 就能根据旧 prompt、旧 asset_id、旧用途批量生成新版 prompt，而不是从零乱猜。

---

# 十一、结论

你这个需求建议直接作为第一阶段基础设施的一部分，而不是等到后面再补。

因为 AI 生成素材一多，如果没有 prompt 管理，后面会非常难统一。

建议现在就新增：

```text
.cursor/rules/16_ai_asset_prompt_management.mdc
docs/art/style_bible.md
docs/art/prompt_library/
docs/art/asset_specs/
data/assets/asset_manifest.json
```

以后所有素材都走这个流程：

```text
素材需求
→ asset_id
→ prompt spec
→ AI 生成候选图
→ 用户选择
→ 记录 selected
→ 导入 Godot
→ 更新 manifest
```

这样飞船、模块、卡牌、背景、UI 图标都能保持统一风格，并且每个素材都能追溯到当初的 prompt。