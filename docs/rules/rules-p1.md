> 已经完成


下面这批可以直接复制到：

```text
.cursor/rules/
```

我按你的新要求强化了四件事：

1. **你没有 Godot/游戏开发经验**：AI 必须用“新手可执行”的方式给测试步骤。
2. **每个模块都要可单独测试**：每个服务、UI 组件、页面都要有 testbed / mock / 手动测试说明。
3. **全局日志系统**：禁止随便 `print()`，统一走 `DebugService`，支持 verbose log、channel log、测试记录。
4. **UI 先网页设计，再还原 Godot**：默认流程是 HTML/CSS 原型 → 你审核 → Godot UI 实现。

> 如果后续你觉得 Cursor 上下文太长，可以把部分 UI 专项 rule 的 `alwaysApply: true` 改成 `alwaysApply: false`，但第一阶段建议先强约束。

---

# 目录

```text
.cursor/rules/
  00_ai_workflow_beginner.mdc
  01_project_context_phase1.mdc
  02_task_scope_change_control.mdc

  10_godot_project_structure.mdc
  11_gdscript_style.mdc
  12_scene_architecture.mdc
  14_autoload_services.mdc
  15_assets_import_pipeline.mdc
  18_logging_debug_tools.mdc

  20_ui_design_system.mdc
  21_ui_layout_responsive.mdc
  22_ui_component_library.mdc
  23_ui_screen_architecture.mdc
  24_ui_web_first_design_and_restoration.mdc
  27_ui_visual_qa.mdc

  30_modular_testing_manual_qa.mdc
  40_gameplay_architecture_later.mdc
```

---

# `.cursor/rules/00_ai_workflow_beginner.mdc`

```md
---
description: Global AI workflow for beginner Godot development.
alwaysApply: true
---

# AI Workflow / Beginner Mode

本项目使用 Cursor / AI 进行代码开发，用户负责人工测试。用户没有游戏开发经验，也没有 Godot 使用经验。因此 AI 必须以“新手可执行”为标准输出方案、代码和测试步骤。

## 基本沟通规则

- 默认使用中文回答。
- 不要假设用户懂 Godot、GDScript、场景树、Autoload、导出设置等概念。
- 如果必须使用 Godot 概念，先用一句话解释。
- 不要只说“运行测试”或“打开场景看看”，必须给出具体路径和点击步骤。
- 每次任务都要小步实现，不要一次性重写大面积系统。
- 不做与当前任务无关的重构、美化、插件接入或目录迁移。
- 如果需要修改 `project.godot`、Autoload、输入映射、导出设置、主题资源、全局配置，必须在完成摘要里明确说明。

## 每次开始任务前，AI 必须做

1. 复述本次目标。
2. 判断本次任务属于：
   - 工程结构
   - UI 设计
   - Web 原型
   - Godot UI 还原
   - 通用组件
   - 日志/调试
   - 测试
   - 玩法占位
3. 列出预计会新增或修改的文件。
4. 给出简短实现计划。
5. 判断是否需要：
   - mock data
   - testbed 场景
   - 手动测试文档
   - 自动测试脚本
   - 新增日志 channel

## 实现时必须遵守

- 优先实现最小可运行版本。
- 新增功能必须有独立测试入口：
  - UI 组件：需要 testbed 场景。
  - 页面 Screen：需要 mock data 和手动测试步骤。
  - Service：需要可单独调用的测试脚本或调试入口。
- UI 第一阶段必须允许使用 mock data，不要提前接真实玩法。
- 不允许为了让代码跑通而删除已有功能。
- 不允许静默吞掉错误。错误应通过 `DebugService` 或 Godot 的错误机制记录。
- 不允许直接复制参考游戏的具体文本、剧情、卡牌、UI 资源或专有名词。

## 每次完成任务后，AI 必须输出

1. 修改文件列表。
2. 实现摘要。
3. 新增或变更的测试入口。
4. Godot 新手人工测试步骤，必须具体到：
   - 打开哪个场景；
   - 按 F5 还是 F6；
   - 应该看到什么；
   - 应该点击什么；
   - Godot Output 面板应该关注什么日志。
5. 如果有自动测试，给出运行命令或运行方式。
6. 已知风险和后续建议。

## Godot 新手测试步骤模板

每次完成后，尽量使用下面格式：

```text
人工测试步骤：
1. 打开 Godot。
2. 点击 Import / 导入项目，选择本项目的 project.godot。
3. 在 FileSystem 面板中找到：res://scenes/xxx/xxx.tscn。
4. 双击打开该场景。
5. 点击右上角 “Run Current Scene” 或按 F6。
6. 预期看到：……
7. 点击：……
8. 预期日志：Godot 底部 Output 面板出现 [ui] / [screen] / [test] 等日志。
9. 如果报错，请复制 Godot Output 面板中的完整错误信息给 AI。
```

## 报错修复流程

当用户反馈 Godot 报错时，AI 必须：

1. 根据报错文件和行号定位问题。
2. 先解释可能原因。
3. 做最小修复。
4. 不顺手大重构。
5. 如果是可复现问题，添加或更新测试入口。
6. 再给出重新测试步骤。
```

---

# `.cursor/rules/01_project_context_phase1.mdc`

```md
---
description: Project context and phase-1 priorities.
alwaysApply: true
---

# Project Context / Phase 1

## 项目定位

本项目是一个 2D 科幻宇宙背景的卡牌构筑 Roguelite 游戏。

参考方向：

- 星际探索
- 宇宙迷航
- 未知文明
- 舰船生存
- 资源压力
- 道德选择
- 卡牌构筑
- 回合制战斗

可以参考类似作品的体验结构，但不得直接复制其剧情、卡牌、角色、美术、UI 或专有名词。

## 技术栈

- 引擎：Godot 4.x
- 语言：GDScript
- 平台：PC 优先
- 画面：2D
- 开发方式：Cursor / AI 编码，人工测试

## 第一阶段优先级

当前阶段是：

```text
基础工程 + UI 设计系统 + Web UI 原型 + Godot UI 还原 + 测试/日志基础
```

第一阶段不以真实玩法为核心。

第一阶段应该重点完成：

- Godot 项目结构
- AppRoot / 启动场景
- ScreenRouter / 页面切换
- DebugService / 全局日志
- SettingsService / 基础设置占位
- UI 设计 Token
- 通用 UI 组件
- Web UI 原型流程
- Godot UI 还原流程
- 主菜单页面
- 设置页面
- 星图 mock 页面
- 战斗 mock 页面
- 奖励 mock 页面
- 组件 testbed
- 页面 manual QA 文档

## 第一阶段禁止事项

除非用户明确要求，不要在第一阶段实现复杂玩法：

- 不实现完整卡牌战斗系统。
- 不实现复杂敌人 AI。
- 不实现完整遗物系统。
- 不实现完整剧情事件系统。
- 不实现正式存档结构。
- 不把临时战斗逻辑硬编码进 UI。

允许做 mock：

- mock 手牌
- mock 敌人
- mock 星图节点
- mock 奖励
- mock 资源栏
- mock 舰船状态

## UI 风格方向

整体视觉方向：

- 深空背景
- 舰船终端
- 冷色科技感
- 半透明面板
- 蓝/青色光效
- 低饱和深色底
- 重要风险用橙色/红色
- 中文可读性优先

AI 不应每次随意发明新的 UI 风格。新增 UI 必须服从设计系统和 token。

## Web-first UI 原则

视觉类 UI 默认流程：

```text
HTML/CSS 静态原型
→ 用户浏览器审核
→ 用户确认
→ Godot Control 场景实现
→ Godot 视觉 QA
```

除非用户明确说“直接做 Godot”，否则 AI 应优先生成网页形式的 UI 设计稿。
```

---

# `.cursor/rules/02_task_scope_change_control.mdc`

```md
---
description: Scope control and safe change rules.
alwaysApply: true
---

# Task Scope and Change Control

## 核心原则

每次任务只解决当前目标，不做无关扩展。

AI 必须避免：

- 一次性重写多个系统。
- 为了一个按钮改完整 UI 框架。
- 为了一个页面接入完整玩法。
- 为了一个报错删除功能。
- 未经说明修改项目设置。
- 未经说明新增插件或第三方依赖。

## 修改边界

如果任务是 UI：

- 只修改相关 UI 文件、组件、mock data、testbed。
- 不实现真实玩法。
- 不修改存档结构。
- 不修改战斗核心。

如果任务是工程结构：

- 可以创建目录、AppRoot、基础服务。
- 必须说明新增目录用途。
- 必须提供最小运行测试。

如果任务是 bug 修复：

- 先定位。
- 再做最小修改。
- 不做顺手重构。
- 输出复测步骤。

## 需要用户确认的情况

以下情况必须先询问用户，不能直接做：

- 接入新插件。
- 改 Godot 主版本。
- 大规模重命名文件。
- 删除已有文件。
- 修改存档格式。
- 修改全局输入映射。
- 修改导出平台配置。
- 引入 npm、React、Vue、Vite 等 Web 构建工具。
- 引入付费或来源不明的素材。

## Godot 文件修改规则

- `.gd` 可以直接修改。
- `.tscn` 可以修改，但要尽量小心，避免无关大面积变更。
- 不要手动改 `.import` 文件，除非明确知道原因。
- 不要修改 Godot 自动生成的 UID/metadata，除非必要。
- 新增资源时优先使用文本可审查格式，例如 `.gd`、`.tscn`、`.tres`、`.json`、`.md`、`.svg`。

## 完成定义

一个任务只有同时满足以下条件才算完成：

1. 功能可运行。
2. 有明确测试方式。
3. 有必要日志。
4. 没有 Godot 启动时报错。
5. 没有破坏现有页面。
6. 输出了人工测试步骤。
```

---

# `.cursor/rules/10_godot_project_structure.mdc`

```md
---
description: Godot project folder and file organization.
alwaysApply: true
---

# Godot Project Structure

## 推荐目录结构

项目根目录建议使用：

```text
.cursor/
  rules/

src/
  app/
  core/
  services/
  debug/
  ui/
    components/
    screens/
    theme/
  data/
  mock/

scenes/
  app/
  components/
  screens/
  testbeds/
  mock/

assets/
  fonts/
  ui/
  icons/
  backgrounds/
  audio/
  placeholders/
  source/

data/
  ui_mock/
  localization/

web_ui/
  prototypes/
  shared/
  assets/

docs/
  ui/
    specs/
    references/
    conversion_notes/
  test_plans/
  ai/
  log_examples/

tests/
  unit/
  integration/
  runners/
```

在 Godot 中这些路径对应：

```text
res://src/
res://scenes/
res://assets/
res://data/
res://docs/
res://tests/
```

`web_ui/` 主要用于浏览器设计稿，不是 Godot 运行时目录。

## 目录职责

### src/

放 GDScript 源码。

- `src/app/`：启动、应用生命周期。
- `src/services/`：全局服务，例如 ScreenRouter、SettingsService。
- `src/debug/`：日志、调试面板、测试辅助。
- `src/ui/components/`：可复用 UI 组件脚本。
- `src/ui/screens/`：页面 Screen 脚本。
- `src/ui/theme/`：主题、样式 token 脚本或工具。
- `src/mock/`：第一阶段 mock 数据适配器。

### scenes/

放 Godot 场景。

- `scenes/app/`：AppRoot、启动场景。
- `scenes/components/`：UI 组件场景。
- `scenes/screens/`：完整页面场景。
- `scenes/testbeds/`：单模块测试场景。
- `scenes/mock/`：mock 展示场景。

### assets/

放游戏素材。

- 字体、图标、背景、音频、placeholder。
- 不放来源不明或版权不清的资源。
- AI 生成的临时素材必须标记为 placeholder。

### data/

放运行数据和 mock 数据。

- `data/ui_mock/`：UI 第一阶段 mock 数据。
- `data/localization/`：本地化文本。

### web_ui/

放网页 UI 原型。

- 每个页面一个目录。
- 使用静态 HTML/CSS/少量 JS。
- 不默认引入构建工具。
- 用户可以直接双击 `index.html` 查看。

### docs/

放文档。

- UI 规格。
- 测试计划。
- UI 还原说明。
- 日志示例。
- AI 开发说明。

### tests/

放自动测试或轻量测试 runner。

默认不引入测试插件。若需要插件，必须用户确认。

## 文件命名

- GDScript：`snake_case.gd`
- 场景文件：`snake_case.tscn`
- JSON 数据：`snake_case.json`
- 文档：`snake_case.md`
- 类名：`PascalCase`
- ID：`lower_snake_case`

示例：

```text
src/ui/screens/main_menu_screen.gd
scenes/screens/main_menu_screen.tscn
docs/ui/specs/main_menu_screen.md
data/ui_mock/main_menu_mock.json
scenes/testbeds/main_menu_screen_testbed.tscn
```

## 新增模块要求

每新增一个模块，必须尽量同时提供：

1. 代码文件。
2. 场景文件，如果是 UI。
3. mock data，如果需要数据。
4. testbed 场景或测试脚本。
5. 手动测试文档或测试步骤。
6. 必要日志。
```

---

# `.cursor/rules/11_gdscript_style.mdc`

```md
---
description: GDScript coding style and maintainability rules.
alwaysApply: true
---

# GDScript Style

## 基础规范

- 使用 Godot 4.x GDScript。
- 尽量添加类型标注。
- 文件名使用 `snake_case.gd`。
- 类名使用 `PascalCase`。
- 变量和函数使用 `snake_case`。
- 常量使用 `UPPER_SNAKE_CASE`。
- signal 使用过去式或事件式命名，例如 `pressed`, `screen_changed`, `value_updated`。

示例：

```gdscript
class_name MainMenuScreen
extends Control

signal start_game_requested
signal settings_requested

var is_ready: bool = false

func setup(data: Dictionary) -> void:
    pass
```

## 类型标注

优先写：

```gdscript
var energy: int = 3
var title: String = ""
var config: Dictionary = {}

func set_title(value: String) -> void:
    title = value
```

避免无类型大字典到处传。确实需要 Dictionary 时，字段名要清晰。

## 职责边界

- 一个脚本只负责一类事情。
- UI 脚本只负责显示、输入和 signal。
- Service 负责全局功能。
- Mock adapter 负责 mock 数据转换。
- 不要把真实玩法逻辑写进 UI。
- 不要把大量 UI 细节写进 Service。

## 节点访问

允许使用：

```gdscript
@onready var start_button: Button = %StartButton
```

但必须保证节点名清晰。

如果节点可能缺失，应进行防御：

```gdscript
if start_button == null:
    DebugService.error("ui", "StartButton missing", {"screen": "main_menu"})
    return
```

## 日志规则

- 不要直接使用 `print()` 输出调试日志。
- 统一使用 `DebugService`。
- 如果 DebugService 尚未实现，可以临时使用 Godot 的 `push_warning()` / `push_error()`，但之后要迁移到 DebugService。
- 测试数据、状态变化、按钮点击、页面切换应记录 channel log。

## 错误处理

- 不要静默失败。
- 对用户可继续操作的问题，用 warning。
- 对无法继续的问题，用 error。
- 不要用异常式写法掩盖 Godot 报错。
- 缺失素材时使用 placeholder，不要崩溃。

## `_process` 使用限制

- 不要在 `_process()` 中堆复杂逻辑。
- UI 动画优先使用 Tween 或 AnimationPlayer。
- 轮询逻辑必须有明确理由。
- 如果只是响应点击、hover、状态变化，应使用 signal。

## 可测试性

每个非纯 UI 脚本应尽量：

- 可独立实例化。
- 少依赖场景树。
- 少依赖 Autoload。
- 对 Autoload 调用集中封装。
- 提供清晰输入输出。
- 允许 mock data 驱动。

## 注释

- 不要写无意义注释。
- 对新手不直观的 Godot 机制可以写简短注释。
- 对临时实现必须标记：

```gdscript
# TODO: Phase 2 replace mock data with real gameplay data.
```
```

---

# `.cursor/rules/12_scene_architecture.mdc`

```md
---
description: Godot scene architecture, screen routing and testbeds.
alwaysApply: true
---

# Scene Architecture

## 启动结构

推荐主启动场景：

```text
scenes/app/app_root.tscn
```

推荐场景树：

```text
AppRoot
  ├── BackgroundLayer
  ├── ScreenLayer
  ├── PopupLayer
  ├── TransitionLayer
  └── DebugLayer
```

职责：

- `BackgroundLayer`：全局背景、深空背景、装饰。
- `ScreenLayer`：当前页面。
- `PopupLayer`：弹窗、确认框。
- `TransitionLayer`：页面切换过渡。
- `DebugLayer`：FPS、日志开关、调试信息。

## Screen 页面

每个完整界面都是一个 Screen。

示例：

```text
scenes/screens/main_menu_screen.tscn
src/ui/screens/main_menu_screen.gd
```

Screen 根节点推荐继承：

```gdscript
extends Control
```

Screen 推荐支持这些方法：

```gdscript
func enter_screen(params: Dictionary = {}) -> void:
    pass

func exit_screen() -> void:
    pass

func refresh_view(data: Dictionary = {}) -> void:
    pass
```

如果当前阶段不需要完整生命周期，也应保持结构上可扩展。

## 页面切换

页面切换统一由 `ScreenRouter` 管理。

禁止：

- Screen A 直接 `get_node()` 找 Screen B。
- Screen A 直接实例化另一个完整页面。
- 子组件直接修改全局页面状态。

允许：

- Screen 发出 signal。
- AppRoot 或 ScreenRouter 接收 signal 后切换页面。
- 使用 mock 参数进入页面。

## Popup

弹窗统一走 PopupLayer 或 PopupManager。

禁止每个页面自己随意创建全屏弹窗层。

## UI 组件

UI 组件应是独立场景。

示例：

```text
scenes/components/sci_fi_button.tscn
src/ui/components/sci_fi_button.gd
scenes/testbeds/sci_fi_button_testbed.tscn
```

组件不应该知道自己属于哪个 Screen。

## Testbed 场景

每个重要模块都应有独立测试入口。

### UI 组件 testbed

用于测试：

- normal
- hover
- pressed
- disabled
- selected
- 不同文本长度
- 不同图标
- 不同尺寸

### Screen testbed

用于测试：

- mock 数据加载
- 页面布局
- 按钮点击
- 返回按钮
- 弹窗
- 日志输出

testbed 命名：

```text
scenes/testbeds/<module_name>_testbed.tscn
```

## 场景设计原则

- 根节点命名清楚。
- 关键节点使用唯一名称，便于 `%NodeName` 引用。
- 布局优先使用 Container。
- 不要用大量绝对坐标搭 UI。
- 装饰性元素可以绝对定位。
- 关键交互元素必须可响应分辨率变化。
```

---

# `.cursor/rules/14_autoload_services.mdc`

```md
---
description: Global Autoload service rules.
alwaysApply: true
---

# Autoload Services

## 原则

Autoload 是全局服务，不是万能垃圾桶。

允许放：

- 页面路由
- 日志
- 设置
- 音频
- 本地化
- 资源注册
- 简单应用状态

不允许放：

- 大量 UI 细节
- 复杂玩法逻辑
- 单个页面专属状态
- 临时测试变量堆积

## 第一阶段推荐服务

按需创建，不必一次全部实现：

```text
AppState
ScreenRouter
DebugService
SettingsService
AudioService
LocalizationService
AssetRegistry
SaveService
```

### AppState

保存第一阶段应用级 mock 状态，例如：

- 当前 screen id
- 当前 mock run id
- 是否处于 debug 模式

不保存复杂玩法状态。

### ScreenRouter

负责：

- 注册 screen id 到场景路径。
- 切换页面。
- 记录页面切换日志。
- 处理返回逻辑。

### DebugService

负责：

- 统一日志。
- verbose 开关。
- log channel 开关。
- 测试 session。
- 内存日志缓存。
- 可选写入文件。

### SettingsService

负责：

- 音量设置。
- 分辨率设置占位。
- 语言设置占位。
- debug 设置占位。

### AudioService

第一阶段只需要占位：

- button click
- hover
- screen transition

没有素材时使用静默实现，不要报错。

### LocalizationService

第一阶段可以简单实现：

- key -> 中文文本
- 缺失 key 时返回 key，并记录 warning。

### AssetRegistry

负责：

- placeholder 资源路径。
- 默认图标。
- 默认背景。
- 缺失素材 fallback。

### SaveService

第一阶段只做占位或设置保存。

不要提前实现复杂正式存档。

## 修改 Autoload 的规则

如果新增或修改 Autoload，AI 必须：

1. 说明服务职责。
2. 说明是否修改 `project.godot`。
3. 说明如何在 Godot 中确认 Autoload 生效。
4. 提供单独测试方式。
5. 增加 DebugService 日志。

## 服务可测试性

每个 Service 应尽量提供：

```gdscript
func reset_for_tests() -> void:
    pass
```

如果服务有状态，测试前应能重置。

## 禁止循环依赖

避免：

```text
ScreenRouter 依赖 AppState
AppState 又依赖 ScreenRouter
```

如果确实需要交互，应通过简单数据或 signal。
```

---

# `.cursor/rules/15_assets_import_pipeline.mdc`

```md
---
description: Asset import, placeholder and licensing rules.
alwaysApply: true
---

# Assets Import Pipeline

## 资源目录

游戏运行资源放在：

```text
assets/
  fonts/
  ui/
  icons/
  backgrounds/
  audio/
  placeholders/
  source/
```

网页 UI 原型资源放在：

```text
web_ui/assets/
```

如果某个资源最终要进入 Godot 游戏，应复制或移动到 `assets/` 下，不能只存在 `web_ui/`。

## 命名规则

使用小写和下划线：

```text
bg_deep_space_01.png
icon_energy.svg
panel_glow_blue.png
font_noto_sans_sc_regular.ttf
```

不要使用：

```text
新建文件.png
image (1).png
按钮最终最终版.png
```

## 版权规则

- 不使用来源不明的商用素材。
- 不直接导入参考游戏截图作为游戏资源。
- AI 生成的临时资源必须标记为 placeholder。
- 下载或引入开源字体/素材时，应在 `docs/` 中记录来源和许可。

## Placeholder 规则

缺资源时不得阻塞开发。

允许使用：

- 纯色 Panel
- 简单 SVG 图标
- Godot Label 占位
- 程序生成渐变背景
- `assets/placeholders/` 中的默认资源

但必须命名清楚：

```text
placeholder_card_art.svg
placeholder_enemy_ship.svg
placeholder_space_bg.png
```

## Godot 导入规则

- 不手动编辑 `.import` 文件，除非有明确原因。
- 不随意修改已有资源的导入设置。
- 字体放在 `assets/fonts/`。
- UI 图标优先使用 SVG 或高分辨率 PNG。
- 大背景图注意尺寸，避免无意义超大文件。
- UI 面板优先使用 Theme / StyleBox，而不是大量切图。

## Web 原型与 Godot 的关系

Web 原型可以使用 CSS 渐变、阴影、滤镜。

转换到 Godot 时：

- 简单面板：使用 StyleBoxFlat。
- 发光边框：使用 StyleBoxFlat + 外层 Panel / Shader / 贴图近似。
- 背景：使用 TextureRect 或程序背景。
- 图标：使用 TextureRect。
- 动效：使用 Tween 或 AnimationPlayer。

如果 Godot 无法 1:1 还原 CSS 效果，AI 必须说明采用的近似方案。
```

---

# `.cursor/rules/18_logging_debug_tools.mdc`

```md
---
description: Global logging, verbose debug and module test recording rules.
alwaysApply: true
---

# Logging and Debug Tools

## 核心原则

项目必须有统一日志系统。

禁止在业务代码中随意使用：

```gdscript
print()
prints()
printerr()
```

统一使用：

```gdscript
DebugService
```

如果 DebugService 尚未实现，本规则要求优先实现最小版本。

## DebugService 目标

DebugService 应支持：

- 全局日志开关。
- 日志等级。
- verbose 详细日志。
- channel 开关。
- 内存日志缓存。
- 测试 session。
- 可选写入 `user://logs/`。
- 输出结构化数据，方便后续单模块测试分析。

## 推荐日志等级

```text
TRACE
DEBUG
INFO
WARN
ERROR
```

建议含义：

- `TRACE`：非常细的流程记录，只在 verbose 模式开启。
- `DEBUG`：调试信息。
- `INFO`：正常关键流程。
- `WARN`：可恢复异常。
- `ERROR`：不可忽视错误。

## 推荐日志 channel

```text
app
screen
routing
ui
component
popup
settings
asset
data
save
audio
input
test
web_conversion
gameplay_mock
```

新增模块时可以新增 channel，但必须命名清楚。

## 推荐全局变量

DebugService 至少应支持类似概念：

```gdscript
var logging_enabled: bool = true
var verbose_enabled: bool = false
var memory_buffer_enabled: bool = true
var file_output_enabled: bool = false
var minimum_level: String = "INFO"
var enabled_channels: Dictionary = {}
```

可以通过 DebugOverlay 或 SettingsService 在未来切换。

## 推荐 API

```gdscript
DebugService.trace(channel: String, event: String, data: Dictionary = {}) -> void
DebugService.debug(channel: String, event: String, data: Dictionary = {}) -> void
DebugService.info(channel: String, event: String, data: Dictionary = {}) -> void
DebugService.warn(channel: String, event: String, data: Dictionary = {}) -> void
DebugService.error(channel: String, event: String, data: Dictionary = {}) -> void

DebugService.set_verbose_enabled(value: bool) -> void
DebugService.set_channel_enabled(channel: String, value: bool) -> void

DebugService.begin_test_session(session_name: String) -> void
DebugService.end_test_session() -> void
DebugService.clear_memory_buffer() -> void
DebugService.get_memory_buffer() -> Array
```

实际实现可以略有不同，但必须保持统一入口。

## 推荐日志结构

每条日志应尽量包含：

```json
{
  "time_msec": 123456,
  "frame": 100,
  "level": "INFO",
  "channel": "screen",
  "event": "screen_entered",
  "message": "optional human readable text",
  "data": {
    "screen_id": "main_menu"
  }
}
```

## 记录哪些数据

UI 模块测试时应记录：

- 页面进入
- 页面退出
- mock 数据加载
- 按钮点击
- 弹窗打开/关闭
- 组件状态变化
- 错误 fallback
- 缺失素材 fallback

Service 测试时应记录：

- 输入参数
- 状态变化
- 输出结果
- 错误原因

## 不要记录哪些数据

不要直接记录：

- 整个 Node 对象。
- 巨大的 Texture/Resource。
- 二进制内容。
- 无限递归字典。
- 隐私信息。
- 过长文本。

应该记录：

- node.name
- resource_path
- id
- 简短状态
- 数值

## Verbose 模式

verbose 开启时可以记录更细数据，例如：

- 布局尺寸
- 每个组件初始化数据
- 页面切换参数
- mock 数据详情

verbose 关闭时只记录关键流程。

## 人工测试要求

每个模块完成后，AI 必须告诉用户：

- 如何打开 verbose log。
- 预期 Output 中应看到哪些 channel。
- 如果失败，应复制哪些日志给 AI。

## DebugOverlay

未来应提供 DebugOverlay，用于：

- 显示 FPS。
- 显示当前 screen。
- 切换 verbose。
- 切换 channel。
- 查看最近日志。
- 导出日志。

第一阶段可以先实现简化版本。
```

---

# `.cursor/rules/20_ui_design_system.mdc`

```md
---
description: UI visual design system, tokens and theme rules.
alwaysApply: true
---

# UI Design System

## 核心原则

UI 必须有统一设计系统。

AI 不得每做一个页面就重新发明：

- 颜色
- 字体
- 按钮风格
- 面板风格
- 间距
- 阴影
- 圆角
- 发光效果

新增视觉风格必须沉淀为 token 或组件。

## 设计系统文件

建议维护：

```text
docs/ui/design_tokens.md
web_ui/shared/tokens.css
src/ui/theme/
assets/ui/
```

Godot 主题可以放：

```text
assets/ui/main_theme.tres
```

或按项目实际情况放在：

```text
src/ui/theme/
```

## 颜色 Token

推荐使用语义命名，不要直接到处写色值。

示例：

```text
color_bg_deep_space
color_bg_space_gradient_top
color_bg_space_gradient_bottom

color_panel_dark
color_panel_mid
color_panel_light
color_panel_glass

color_border_dim
color_border_blue
color_border_cyan

color_accent_blue
color_accent_cyan
color_accent_purple

color_warning_orange
color_danger_red
color_success_green

color_text_primary
color_text_secondary
color_text_muted
color_text_disabled
```

## 字体 Token

中文项目必须优先保证可读性。

推荐：

```text
font_title
font_subtitle
font_body
font_body_bold
font_number
font_mono
```

可选字体：

- Noto Sans SC
- 思源黑体
- 其他确认可商用字体

如果字体文件不存在，必须 fallback 到 Godot 默认字体，并记录 warning。

## 字号 Token

推荐：

```text
font_size_xs = 14
font_size_sm = 16
font_size_md = 20
font_size_lg = 28
font_size_xl = 40
font_size_display = 64
```

可根据实际视觉调整，但应统一。

## 间距 Token

推荐：

```text
space_4
space_8
space_12
space_16
space_24
space_32
space_48
space_64
```

## 圆角和描边

推荐：

```text
radius_sm
radius_md
radius_lg
border_width_thin
border_width_normal
```

## 常见组件状态

组件必须考虑：

- normal
- hover
- pressed
- disabled
- selected
- focused

## 科幻 UI 特征

可以使用：

- 半透明深色面板
- 细线边框
- 青蓝色发光
- 角标装饰
- 扫描线
- 星点背景
- 终端式网格
- 轻微噪声纹理

但不要影响文字可读性。

## Web 和 Godot 一致性

Web 原型中的 CSS variables 应尽量与 Godot token 对应。

示例：

```css
:root {
  --color-bg-deep-space: #050914;
  --color-accent-cyan: #4de8ff;
  --space-16: 16px;
}
```

Godot 实现时应使用相同语义 token，而不是重新挑颜色。

## 新增 UI 的要求

每次新增 UI，AI 必须说明：

- 使用了哪些 token。
- 是否新增 token。
- 是否复用了已有组件。
- 是否需要更新设计系统文档。
```

---

# `.cursor/rules/21_ui_layout_responsive.mdc`

```md
---
description: Responsive UI layout rules for Godot and web prototypes.
alwaysApply: true
---

# UI Layout and Responsive Rules

## 基准分辨率

第一阶段 UI 设计基准：

```text
1920 x 1080
```

必须至少考虑：

```text
1366 x 768
1600 x 900
1920 x 1080
2560 x 1440
```

## Godot 布局原则

关键 UI 优先使用：

- Control
- MarginContainer
- VBoxContainer
- HBoxContainer
- GridContainer
- PanelContainer
- ScrollContainer
- TextureRect
- Label
- Button

不要大量使用绝对坐标。

允许绝对定位：

- 背景星点
- 装饰线
- 角标
- 氛围光
- 非关键装饰元素

不允许关键交互元素只靠绝对坐标固定。

## Anchor 和 Container

AI 创建 UI 时必须关注：

- anchors
- offsets
- size flags
- custom minimum size
- container separation
- stretch mode
- expand / fill

如果使用绝对定位，必须说明原因。

## Safe Area

所有页面应有主内容安全边距。

推荐：

```text
外边距 48px，低分辨率可降到 24px。
```

不要把按钮贴在屏幕边缘。

## 文本适配

中文 UI 必须考虑：

- 文本过长
- 自动换行
- 省略号
- 行高
- 字号
- 按钮文字不被裁切
- Label 的 autowrap

如果文本可能很长，优先使用：

- ScrollContainer
- RichTextLabel
- 自动换行 Label

## Web 原型布局

Web 原型可使用：

- flex
- grid
- CSS variables
- media queries

但必须能转换到 Godot。

Web 到 Godot 的常见映射：

```text
display: flex row     -> HBoxContainer
display: flex column  -> VBoxContainer
gap                   -> separation / margin
padding               -> MarginContainer
grid                  -> GridContainer
position absolute     -> Control anchors / offsets
overflow auto         -> ScrollContainer
```

## 布局测试

AI 完成 UI 后必须提供不同分辨率测试清单：

```text
1. 1920x1080 检查是否接近设计稿。
2. 1366x768 检查是否溢出。
3. 2560x1440 检查是否过度拉伸。
4. 检查中文文本是否截断。
5. 检查按钮是否仍可点击。
6. 检查弹窗是否居中。
```

## 禁止

- 不要把全部 UI 做成一张背景图。
- 不要为了还原效果牺牲可读性。
- 不要让重要按钮在小分辨率下跑出屏幕。
- 不要用魔法数字堆满整个页面。
```

---

# `.cursor/rules/22_ui_component_library.mdc`

```md
---
description: Reusable UI component library rules.
alwaysApply: true
---

# UI Component Library

## 核心原则

页面应该由组件组合而成。

不要每个页面单独写一套按钮、面板、标题栏、资源栏。

## 第一阶段推荐组件

```text
SciFiButton
IconButton
PrimaryButton
SecondaryButton
DangerButton

SciFiPanel
GlassPanel
TerminalPanel

ScreenTitleBar
TopResourceBar
BackButton

ModalDialog
ConfirmDialog
ToastMessage
TooltipPanel

CardViewMock
CardPileViewMock
DeckPreviewItemMock

TabButton
ScrollList
ChoiceButton
ProgressBarSciFi
StatusBadge

DebugLogView
DebugToggleRow
```

可以按需实现，不必一次全部做完。

## 组件文件结构

示例：

```text
scenes/components/sci_fi_button.tscn
src/ui/components/sci_fi_button.gd
scenes/testbeds/sci_fi_button_testbed.tscn
docs/test_plans/sci_fi_button_test.md
```

## 组件 API

组件应提供清晰方法，例如：

```gdscript
func setup(data: Dictionary) -> void:
    pass

func set_disabled(value: bool) -> void:
    pass

func set_selected(value: bool) -> void:
    pass
```

组件 signal 示例：

```gdscript
signal pressed(action_id: String)
signal hovered(action_id: String)
signal state_changed(state: String)
```

## 组件数据

组件可以接受 Dictionary，但字段必须清楚。

示例：

```gdscript
{
  "id": "start_game",
  "text": "开始探索",
  "icon": "res://assets/icons/icon_start.svg",
  "disabled": false
}
```

## 组件禁止事项

组件不应该：

- 直接切换完整页面。
- 直接读写 SaveService。
- 直接实现玩法逻辑。
- 直接知道自己属于哪个 Screen。
- 硬编码大量页面专属文本。
- 使用与设计系统冲突的颜色。

## 组件状态

组件必须至少考虑：

- normal
- hover
- pressed
- disabled

视情况考虑：

- selected
- focused
- warning
- danger

## 组件 testbed

每个重要组件必须有 testbed。

testbed 应展示：

- 默认状态。
- hover/pressed 可交互状态。
- disabled 状态。
- 长文本。
- 缺失 icon。
- 不同尺寸。

## 组件日志

组件关键交互应记录日志：

```gdscript
DebugService.info("component", "button_pressed", {
    "component": "SciFiButton",
    "action_id": action_id
})
```

verbose 模式下可以记录组件 setup 数据。
```

---

# `.cursor/rules/23_ui_screen_architecture.mdc`

```md
---
description: UI screen structure, mock data and navigation rules.
alwaysApply: true
---

# UI Screen Architecture

## Screen 是完整页面

每个完整界面应该是一个 Screen。

示例：

```text
MainMenuScreen
SettingsScreen
RunSetupScreen
StarMapMockScreen
BattleMockScreen
RewardMockScreen
DeckPreviewMockScreen
```

## Screen 文件结构

每个 Screen 推荐包含：

```text
scenes/screens/<screen_name>.tscn
src/ui/screens/<screen_name>.gd
docs/ui/specs/<screen_name>.md
data/ui_mock/<screen_name>.json
scenes/testbeds/<screen_name>_testbed.tscn
docs/test_plans/<screen_name>_test.md
```

如果是纯占位页面，可以先省略部分文件，但必须有测试步骤。

## Screen 推荐节点结构

示例：

```text
BattleMockScreen
  ├── BackgroundLayer
  ├── SafeArea
  │   ├── TopBar
  │   ├── MainContent
  │   │   ├── EnemyArea
  │   │   ├── PlayerArea
  │   │   └── HandArea
  │   └── ActionBar
  ├── PopupAnchor
  └── DebugInfo
```

## Screen 脚本职责

Screen 脚本负责：

- 接收 mock data。
- 初始化组件。
- 响应按钮 signal。
- 发出页面级 signal。
- 请求 ScreenRouter 切换页面。
- 记录页面进入/退出日志。

Screen 脚本不负责：

- 真实战斗计算。
- 正式存档。
- 敌人 AI。
- 复杂卡牌效果。
- 全局设置持久化细节。

## Mock data

第一阶段 Screen 使用 mock data。

示例：

```json
{
  "title": "未知星域",
  "resources": {
    "fuel": 12,
    "credits": 80,
    "hull": 42
  },
  "buttons": [
    {
      "id": "start",
      "text": "开始探索"
    }
  ]
}
```

## Navigation

页面跳转统一通过 ScreenRouter。

Screen 可以发 signal：

```gdscript
signal navigate_requested(screen_id: String, params: Dictionary)
```

由 AppRoot 或 ScreenRouter 处理。

## Back 行为

有返回按钮的页面必须定义返回目标。

不要让返回按钮随意 `queue_free()` 当前页面。

## Screen 日志

Screen 必须记录：

- screen_entered
- screen_exited
- mock_data_loaded
- button_action
- navigate_requested
- layout_warning，如有

## Screen 测试

每个 Screen 完成后必须提供：

1. testbed 或主路由进入方式。
2. mock 数据。
3. 新手人工测试步骤。
4. 视觉 QA 检查点。
5. 预期日志。
```

---

# `.cursor/rules/24_ui_web_first_design_and_restoration.mdc`

```md
---
description: Web-first UI design and conversion to Godot UI.
alwaysApply: true
---

# Web-first UI Design and Godot Restoration

## 核心原则

视觉类 UI 默认先做网页设计稿，再做 Godot 还原。

默认流程：

```text
1. 生成 HTML/CSS 静态 UI 原型。
2. 用户用浏览器查看并反馈。
3. AI 根据反馈修改网页原型。
4. 用户确认。
5. AI 分析网页结构和 CSS。
6. AI 转换为 Godot Control 场景。
7. AI 提供 Godot 视觉 QA。
```

除非用户明确说“直接做 Godot”，否则不要跳过网页原型阶段。

## Web 原型位置

每个网页原型放在：

```text
web_ui/prototypes/<screen_name>/
  index.html
  styles.css
  mock_data.js 或 mock_data.json
  README.md
```

共用 token：

```text
web_ui/shared/tokens.css
```

共用资源：

```text
web_ui/assets/
```

## Web 原型要求

默认使用：

- HTML
- CSS
- 少量原生 JS

不要默认引入：

- React
- Vue
- Svelte
- Vite
- Tailwind
- Bootstrap
- npm 构建流程

除非用户明确批准。

原因：用户可以直接双击 `index.html` 审核。

## Web 原型必须包含

- 基准分辨率说明。
- 页面结构。
- 使用的 token。
- mock 数据。
- hover/active/disabled 状态，如果适用。
- 简单 README，告诉用户如何打开。

README 示例：

```text
打开方式：
双击 index.html 即可在浏览器查看。
推荐用 Chrome/Edge。
设计基准：1920x1080。
```

## 用户确认前禁止

在用户确认网页设计稿前，不要把视觉页面完整实现到 Godot，除非任务明确要求。

允许先做：

- Godot 空壳页面
- 路由占位
- mock data
- 组件基础版本

## 从 Web 转 Godot 的流程

AI 必须先输出转换分析：

```text
1. 页面区域拆解。
2. DOM 结构。
3. CSS 布局方式。
4. Godot 节点映射。
5. 需要复用的组件。
6. 需要新增的组件。
7. CSS 效果在 Godot 中的实现方式。
8. 无法 1:1 还原的地方和替代方案。
```

## 常见映射

```text
HTML section/div       -> Control / PanelContainer / MarginContainer
CSS flex row           -> HBoxContainer
CSS flex column        -> VBoxContainer
CSS grid               -> GridContainer
CSS padding            -> MarginContainer
CSS gap                -> theme separation / custom constants
CSS border-radius      -> StyleBoxFlat corner radius
CSS border             -> StyleBoxFlat border
CSS background         -> Panel / TextureRect / ColorRect
CSS box-shadow/glow    -> StyleBox/Shader/额外发光节点近似
CSS transition         -> Tween / AnimationPlayer
CSS hover              -> mouse_entered / mouse_exited / theme state
CSS absolute decor     -> Control with anchors / TextureRect
```

## 前端代码作为设计稿

如果用户提供前端页面代码，AI 必须：

1. 阅读 HTML 结构。
2. 阅读 CSS token。
3. 提取布局区域。
4. 提取组件。
5. 提取颜色、字号、间距。
6. 输出 Godot 节点树设计。
7. 再实现 Godot 场景。

不要直接凭感觉重做。

## 还原目标

目标不是像素级完全一致，而是：

```text
1920x1080 下视觉接近设计稿；
其他常见分辨率不崩；
交互清晰；
可继续扩展；
符合 Godot UI 结构。
```

## Godot 还原完成后

必须输出：

- Web 原型路径。
- Godot 场景路径。
- 对应组件列表。
- 还原差异说明。
- 人工对照检查清单。
- 预期日志。
```

---

# `.cursor/rules/27_ui_visual_qa.mdc`

```md
---
description: Visual QA checklist for UI screens and components.
alwaysApply: true
---

# UI Visual QA

## 每个 UI 完成后必须 QA

AI 完成任何 UI 组件或 Screen 后，必须输出视觉 QA 清单。

## 基础检查

```text
1. Godot 是否能打开场景。
2. 运行当前场景是否报错。
3. 1920x1080 下布局是否接近设计稿。
4. 1366x768 下是否溢出。
5. 2560x1440 下是否过度拉伸。
6. 中文文本是否被裁切。
7. 按钮是否可点击。
8. hover 是否有反馈。
9. disabled 是否明显不可用。
10. 返回按钮是否生效。
11. 弹窗是否居中。
12. 缺失资源是否 fallback。
13. Output 是否有预期日志。
```

## Web 到 Godot 对照表

从网页原型还原 Godot UI 时，AI 必须输出类似表格：

```text
区域 | Web 设计 | Godot 实现 | 差异 | 是否可接受
顶部标题栏 | 半透明玻璃面板 | PanelContainer + StyleBoxFlat | 阴影较弱 | 可接受
主按钮 | cyan glow | Button theme + hover 颜色 | 发光近似 | 可接受
背景星云 | CSS radial-gradient | TextureRect/ColorRect 渐变 | 细节不同 | 需要后续美术
```

## 截图反馈流程

如果用户用截图反馈问题，AI 必须：

1. 根据截图定位 UI 区域。
2. 只调整相关区域。
3. 不重写整个页面。
4. 说明调整了哪些参数。
5. 让用户重新截图确认。

## 新手人工测试输出模板

```text
人工测试步骤：
1. 打开 Godot。
2. 在 FileSystem 找到：res://scenes/testbeds/xxx_testbed.tscn。
3. 双击打开。
4. 按 F6 运行当前场景。
5. 检查：
   - 组件是否显示；
   - hover 是否变化；
   - 点击是否有反馈；
   - Output 是否出现 [component] 日志。
6. 切换窗口大小，检查是否溢出。
7. 如果异常，请截图并复制 Output 日志。
```

## 日志要求

UI QA 时应看到：

- screen_entered
- mock_data_loaded
- component_initialized
- button_pressed
- fallback_asset_used，如果有
- layout_warning，如果有

## 不合格情况

出现以下情况不能认为完成：

- Godot 打开场景报错。
- 关键按钮不可点击。
- 文字严重截断。
- 页面无法在 1366x768 显示主要内容。
- 缺失资源导致崩溃。
- 没有任何测试方式。
```

---

# `.cursor/rules/30_modular_testing_manual_qa.mdc`

```md
---
description: Modular testing, testbeds, manual QA and AI-test rules.
alwaysApply: true
---

# Modular Testing and Manual QA

## 核心要求

每个模块都必须可以单独测试。

模块包括：

- Autoload Service
- UI Component
- Screen
- Mock Data Adapter
- Theme / Style
- Logging Utility
- Data Loader
- Popup
- Scene Transition

## 测试层级

本项目第一阶段使用三类测试：

```text
1. 人工测试步骤
2. Godot testbed 场景
3. 轻量自动测试脚本
```

不默认引入第三方测试插件。若需要插件，必须用户确认。

## 人工测试

每个任务完成后必须提供人工测试步骤。

用户是 Godot 新手，所以步骤必须明确：

- 打开哪个文件。
- 按 F5 还是 F6。
- 应该看到什么。
- 应该点击什么。
- Output 应该出现什么日志。
- 失败时给 AI 什么信息。

## Testbed 场景

UI 组件和 Screen 必须优先提供 testbed。

命名：

```text
scenes/testbeds/<module_name>_testbed.tscn
```

testbed 应该：

- 不依赖完整游戏流程。
- 可以直接 F6 运行。
- 使用 mock data。
- 显示关键状态。
- 输出日志。
- 方便用户截图反馈。

## Mock Data

第一阶段优先使用：

```text
data/ui_mock/
```

mock data 应：

- 字段清楚。
- 不混入真实复杂玩法。
- 可被 testbed 直接加载。
- 出错时有 fallback。

## 自动测试

对于非 UI 纯逻辑模块，应尽量提供轻量自动测试。

推荐位置：

```text
tests/unit/
tests/integration/
tests/runners/
```

如果实现 test runner，优先支持命令行：

```text
godot --headless --path . -s res://tests/runners/test_runner.gd
```

如果用户不会命令行，仍必须提供 Godot 内运行方式或 testbed。

## Service 测试

Service 应提供：

```gdscript
reset_for_tests()
```

测试时应记录：

- 初始状态
- 输入
- 输出
- 状态变化
- 错误

## 日志和测试结合

每个测试应尽量调用：

```gdscript
DebugService.begin_test_session("module_name")
DebugService.end_test_session()
```

测试日志必须能够帮助判断模块行为。

## Definition of Done

一个模块完成必须满足：

1. 可以独立运行或独立调用。
2. 有 mock data 或明确输入。
3. 有人工测试步骤。
4. 有必要日志。
5. 失败时有可诊断信息。
6. 不依赖真实玩法系统。
7. 不需要用户理解复杂 Godot 内部机制。

## Bug 回归

修复 bug 后：

- 如果是 UI bug，更新 testbed 或 QA 清单。
- 如果是逻辑 bug，添加自动测试或测试脚本。
- 如果是配置问题，更新人工测试步骤。
```

---

# `.cursor/rules/40_gameplay_architecture_later.mdc`

```md
---
description: Phase-1 gameplay limitation and future gameplay architecture placeholder.
alwaysApply: true
---

# Gameplay Architecture Later

## 当前阶段

当前阶段重点不是玩法，而是：

```text
Godot 工程基础
UI 设计系统
网页 UI 原型
Godot UI 还原
日志系统
模块测试能力
```

因此，除非用户明确要求，不要实现完整玩法系统。

## 第一阶段允许

允许实现：

- mock 手牌
- mock 卡牌展示
- mock 敌人
- mock 资源栏
- mock 战斗界面
- mock 星图
- mock 奖励
- mock 事件选择
- 假按钮跳转
- 假数据刷新

## 第一阶段禁止

禁止在 UI 中硬编码真实玩法：

- 不要在卡牌 UI 里直接扣敌人血。
- 不要在按钮里写完整战斗结算。
- 不要把抽牌/弃牌/能量系统写死在 Screen。
- 不要把敌人 AI 写在敌人 UI 组件里。
- 不要提前设计不可变更的正式存档结构。

## 未来玩法接口预留

可以预留轻量接口，例如：

```gdscript
func refresh_view(data: Dictionary) -> void:
    pass
```

或 mock adapter：

```gdscript
func get_battle_mock_data() -> Dictionary:
    pass
```

但不要提前做复杂抽象。

## 后续第二阶段方向

第二阶段再展开：

- CardDef
- CardInstance
- BattleContext
- ActionQueue
- EnemyIntent
- StatusEffect
- RewardSystem
- RunState
- Save Migration

这些不属于第一阶段核心任务。

## UI 和玩法边界

UI 只做：

- 显示
- 输入
- 动画
- signal
- mock 数据展示

玩法做：

- 规则
- 计算
- 状态变更
- 随机
- 奖励
- 存档

第一阶段 UI 必须保持未来可接入真实玩法。
```

---

# 建议你给 Cursor 的第一条任务

复制完这些 rules 后，可以直接给 Cursor 下这个任务：

```text
请严格遵守 .cursor/rules 中的规范。

目标：
初始化第一阶段 Godot 项目骨架，不做真实玩法。

要求：
1. 创建推荐目录结构。
2. 创建 AppRoot 启动场景。
3. 创建最小 DebugService，支持 info/warn/error、verbose 开关、channel。
4. 创建最小 ScreenRouter，可以切换 MainMenuScreen 和 SettingsScreen。
5. 创建 MainMenuScreen mock 页面。
6. 创建 SettingsScreen mock 页面。
7. 创建至少一个 testbed 场景，用于测试 MainMenuScreen 或基础按钮。
8. 所有关键操作输出 DebugService 日志。
9. 给出 Godot 新手人工测试步骤。
10. 不接入卡牌战斗，不实现真实玩法。

完成后请输出：
- 修改文件列表
- 实现摘要
- 如何在 Godot 中设置/确认主场景
- 人工测试步骤
- 预期日志
- 已知风险
```

这套规则比较适合你现在的阶段：**先把游戏开发基础设施、UI 设计/还原流程、测试和日志系统搭起来，再进入玩法开发。**