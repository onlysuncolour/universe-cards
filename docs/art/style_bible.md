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

