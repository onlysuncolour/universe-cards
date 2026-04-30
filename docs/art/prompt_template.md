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

