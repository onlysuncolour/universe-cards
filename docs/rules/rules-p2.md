你这个判断很关键。

**第二阶段的 Rule 不能写成“玩法规则说明书”。**
因为玩法、数值、卡牌效果、战斗节奏一定会反复改。

第二阶段的 Rule 应该约束的是：

> **玩法可以推翻，但推翻时不能污染 UI、不能破坏工程结构、不能失去测试能力、不能失去日志追踪、不能让 AI 把临时代码写成正式系统。**

也就是说：

```text
机制可以变。
架构边界不能乱。
数据入口不能乱。
测试入口不能丢。
日志不能丢。
UI 和玩法不能耦合。
实验代码和正式代码必须分开。
```

---

# 一、第二阶段 Rule 的核心思想

我建议第二阶段采用这套原则：

## 1. 固定“接入方式”，不固定“玩法内容”

不要在 Rule 里写死：

```text
玩家每回合固定 3 点能量。
玩家每回合固定抽 5 张牌。
攻击牌一定造成直接伤害。
敌人一定每回合显示意图。
```

这些都是玩法设计，很可能会变。

应该写成：

```text
当前玩法参数必须放在数据或配置中。
战斗状态变化必须经过 BattleCore。
UI 只能读取 BattleViewModel。
卡牌效果必须经过 EffectResolver。
随机必须来自统一 RNG。
所有战斗行为必须能记录 BattleEvent。
```

---

## 2. 区分 Prototype / Candidate / Stable

第二阶段一定要允许试错。

建议所有玩法任务分成三种模式：

| 模式 | 含义 | 允许程度 |
|---|---|---|
| Prototype | 快速验证想法 | 可以临时、可以硬编码，但必须隔离 |
| Candidate | 准备进入主流程的候选方案 | 必须数据化、可测试、有日志 |
| Stable | 当前正式方案 | 受测试保护，修改必须有迁移计划 |

这能避免 AI 每次做实验都把正式代码弄乱。

---

## 3. 测试保护的是“当前有效设计”，不是旧设计

玩法会改，所以测试也要跟着改。

但测试分两类：

### 永久不变量测试

这些不能因为玩法改版就删：

```text
UI 不直接修改战斗状态
战斗逻辑可 headless 运行
随机可复现
日志有 seed
卡牌数据能校验
非法操作不会导致崩溃
```

### 当前设计测试

这些可以随着改版更新：

```text
当前默认每回合抽几张
当前能量机制如何运作
当前状态效果如何结算
当前敌人意图如何显示
```

这点很重要。不要让 AI 误以为“测试永远不能变”。玩法大改时，当前设计测试当然要变，但必须说明为什么变。

---

# 二、第二阶段推荐 Rule 文件结构

进入第二阶段时，需要把第一阶段的：

```text
40_gameplay_architecture_later.mdc
```

改掉或替换掉。因为它现在的作用是“禁止复杂玩法”，第二阶段就不能继续这么写。

推荐新增或替换为：

```text
.cursor/rules/
  40_phase2_gameplay_iteration.mdc
  41_gameplay_architecture_boundaries.mdc
  42_gameplay_data_content_pipeline.mdc
  43_battle_core_state_machine.mdc
  44_card_effect_status_system.mdc
  45_enemy_intent_ai.mdc
  46_run_map_reward_event_system.mdc
  47_rng_balance_simulation.mdc
  48_gameplay_testing_logging.mdc
  49_save_versioning_during_iteration.mdc
  50_ai_gameplay_workflow.mdc
```

但不要一开始全部写得特别死。

我建议第二阶段第一批只启用这 6 个：

```text
40_phase2_gameplay_iteration.mdc
41_gameplay_architecture_boundaries.mdc
42_gameplay_data_content_pipeline.mdc
43_battle_core_state_machine.mdc
48_gameplay_testing_logging.mdc
50_ai_gameplay_workflow.mdc
```

等卡牌、敌人、地图、奖励开始稳定后，再细化：

```text
44_card_effect_status_system.mdc
45_enemy_intent_ai.mdc
46_run_map_reward_event_system.mdc
47_rng_balance_simulation.mdc
49_save_versioning_during_iteration.mdc
```

---

# 三、每个 Rule 应该管什么

---

## 40_phase2_gameplay_iteration.mdc

这个是第二阶段最重要的 Rule。

它不规定具体玩法，而是规定“玩法怎么试错”。

核心内容：

```text
1. 所有玩法任务必须标注模式：
   Prototype / Candidate / Stable。

2. Prototype 代码必须放在：
   src/prototypes/
   scenes/testbeds/
   data/prototypes/
   docs/experiments/

3. Prototype 允许临时硬编码，但禁止：
   - 写进正式 UI Screen
   - 写进正式存档
   - 污染正式数据池
   - 删除稳定系统
   - 伪装成正式实现

4. Candidate 必须：
   - 数据驱动
   - 有 testbed
   - 有日志
   - 有当前设计文档
   - 有最小自动测试或手动测试

5. Stable 修改必须：
   - 说明影响范围
   - 更新测试
   - 更新文档
   - 提供回归测试步骤

6. 用户推翻玩法时，AI 不应抵抗旧设计，而应：
   - 找出哪些文件受影响
   - 保留稳定基础设施
   - 删除或归档废弃实验
   - 更新 current design 文档
   - 更新测试
```

建议维护实验文档：

```text
docs/experiments/
  exp_001_energy_combat.md
  exp_002_heat_system.md
  exp_003_no_energy_card_play.md
```

每个实验文档写：

```text
实验目标：
假设：
涉及文件：
测试方式：
结论：
是否进入 Candidate：
是否废弃：
```

---

## 41_gameplay_architecture_boundaries.mdc

这个 Rule 负责保护架构边界。

第二阶段最容易出问题的是：AI 把战斗逻辑写进 UI。

必须禁止。

推荐核心架构：

```text
Input / UI
  ↓
BattleCommand
  ↓
BattleCore
  ↓
BattleState
  ↓
BattleEvent
  ↓
BattleViewModel
  ↓
UI Display / Animation
```

也就是：

```text
玩家点击卡牌
→ UI 发出 PlayCardCommand
→ BattleCore 校验并执行
→ 修改 BattleState
→ 产生 BattleEvent
→ 生成 BattleViewModel
→ UI 根据 ViewModel 刷新
```

Rule 里要明确：

### UI 可以做

```text
显示手牌
显示敌人
显示血量
显示资源
播放动画
发送玩家输入
```

### UI 不可以做

```text
直接扣敌人血
直接抽牌
直接洗牌
直接结算状态
直接判断卡牌是否能打出
直接修改 BattleState
```

### BattleCore 不可以做

```text
直接操作 Godot Node
直接播放动画
直接访问具体按钮
直接依赖某个 Screen
```

这个边界一旦守住，即使玩法大改，UI 也不会全崩。

---

## 42_gameplay_data_content_pipeline.mdc

玩法会频繁改，所以卡牌、敌人、奖励、事件必须数据化。

但也不要一开始过度复杂。

推荐数据目录：

```text
data/gameplay/
  cards/
  enemies/
  encounters/
  rewards/
  statuses/
  run/
  balance/
  prototypes/
```

或者：

```text
data/prototypes/
  exp_001/
  exp_002/
```

Rule 重点：

```text
1. 所有卡牌、敌人、状态、奖励必须有唯一 id。
2. UI 不直接读取原始 JSON 做玩法判断。
3. 数据必须由 DataRegistry 或 GameplayDataRegistry 加载。
4. 数据加载时必须校验。
5. 缺字段要有清晰错误日志。
6. Prototype 数据不得混入正式卡池。
7. 删除或重命名 id 时必须说明影响。
```

卡牌数据可以先简单，例如：

```json
{
  "id": "plasma_shot",
  "name_key": "card.plasma_shot.name",
  "type": "attack",
  "cost": {
    "energy": 1
  },
  "target": "enemy",
  "effects": [
    {
      "type": "damage",
      "amount": 7
    }
  ]
}
```

但 Rule 不应该强行规定永远必须有 energy。

可以写成：

```text
当前版本可以使用 energy cost。
如果后续改成 heat、cooldown、charge 或无费用系统，
必须通过 CostResolver 或类似统一入口修改，
不要让 UI 和卡牌组件直接判断费用。
```

---

## 43_battle_core_state_machine.mdc

战斗系统必须有稳定的“流程骨架”。

但是具体流程可以改。

初期可以用：

```text
BATTLE_START
PLAYER_TURN_START
PLAYER_ACTION
PLAYER_TURN_END
ENEMY_TURN_START
ENEMY_ACTION
ENEMY_TURN_END
CHECK_BATTLE_END
REWARD
```

但 Rule 应该写成：

```text
当前阶段可以使用上述默认流程。
如果流程改版，必须更新：
- battle state machine
- current_combat_loop.md
- battle smoke tests
- battle testbed
```

核心规则：

```text
1. 战斗状态必须集中在 BattleState。
2. 玩家操作必须通过 BattleCommand。
3. 战斗结算必须产生 BattleEvent。
4. UI 动画必须消费 BattleEvent，而不是重新计算逻辑。
5. 每个关键阶段必须有日志。
6. 随机必须可复现。
7. 战斗核心必须可以不打开 UI 单独测试。
```

建议事件类型：

```text
BattleStarted
TurnStarted
TurnEnded
CardDrawn
CardPlayed
CardMoved
DamageDealt
BlockGained
StatusApplied
EnemyIntentChanged
EnemyActionResolved
BattleEnded
```

这些事件非常有用：

- UI 可以拿来播动画；
- 测试可以拿来断言；
- 日志可以拿来复盘；
- 后续模拟战斗可以复用。

---

## 44_card_effect_status_system.mdc

这个 Rule 不建议一开始写得太死，因为卡牌效果会大改。

它应该规定：

```text
1. CardDef 是卡牌定义。
2. CardInstance 是战斗中的卡牌实例。
3. CardView 是 UI 显示。
4. 三者不能混在一起。
```

卡牌效果推荐通过 EffectResolver：

```text
CardDef.effects
  ↓
EffectResolver
  ↓
EffectHandler
  ↓
BattleEvent
```

Rule 里要写：

```text
1. 新增普通效果时，优先新增通用 EffectHandler。
2. 不要为单张卡在 BattleCore 里写 if card_id == xxx。
3. 特殊机制可以先 Prototype，但进入 Candidate 后必须整理为：
   - 通用 effect
   - 或明确命名的 special handler
4. 状态效果必须通过统一 hook 或 resolver 生效。
5. 状态效果不能散落在 UI、敌人、卡牌按钮里。
```

但不要一开始写 20 个 hook。

可以先少量：

```text
on_turn_start
on_turn_end
on_card_played
modify_damage
modify_block
```

后续需要再加。

---

## 45_enemy_intent_ai.mdc

敌人系统要避免写死在敌人 UI 里。

Rule 应该规定：

```text
1. EnemyDef 是数据。
2. EnemyState 是战斗中状态。
3. EnemyView 是 UI。
4. EnemyAI / IntentResolver 负责决定行动。
5. 敌人意图必须可以通过日志和 testbed 观察。
6. 敌人行动必须可复现。
```

敌人模式可以先简单：

```json
{
  "id": "raider_drone",
  "hp": 32,
  "pattern": [
    {
      "intent": "attack",
      "damage": 6
    },
    {
      "intent": "defend",
      "block": 5
    }
  ]
}
```

后续再扩展条件行为。

---

## 46_run_map_reward_event_system.mdc

这部分是星图、奖励、事件。

Rule 重点：

```text
1. RunState 不等于 BattleState。
2. 星图、奖励、事件都属于 Run 层。
3. 战斗结束后通过 RewardSystem 生成奖励。
4. UI 奖励页面只展示 RewardChoices，不直接改牌组。
5. 事件选择通过 EventChoiceCommand 执行。
6. 当前阶段可以 mock，但不得写死在 UI。
```

推荐结构：

```text
src/gameplay/run/
src/gameplay/rewards/
src/gameplay/events/
data/gameplay/events/
data/gameplay/rewards/
```

---

## 47_rng_balance_simulation.mdc

这个对卡牌游戏非常重要。

Rule 必须规定：

```text
1. 所有随机必须来自统一 RNG。
2. 每局必须有 run_seed。
3. 每场战斗可以派生 battle_seed。
4. 抽牌、敌人随机、奖励随机、地图随机都要能复现。
5. 日志必须记录 seed。
```

推荐后续做模拟脚本：

```text
tools/simulate_battles.gd
tests/runners/battle_sim_runner.gd
```

输出：

```text
胜率
平均回合数
平均损血
卡牌使用率
死亡原因
敌人胜率
```

这对后面平衡性很有用。

---

## 48_gameplay_testing_logging.mdc

第二阶段必须强化测试和日志。

每个玩法模块都应该有：

```text
1. headless 测试
2. testbed 场景
3. mock fixture
4. verbose log
5. 人工测试步骤
```

推荐测试目录：

```text
tests/gameplay/
  battle/
  cards/
  effects/
  enemies/
  run/
  rewards/
  fixtures/
  runners/
```

推荐 testbed：

```text
scenes/testbeds/gameplay/battle_core_testbed.tscn
scenes/testbeds/gameplay/card_effect_testbed.tscn
scenes/testbeds/gameplay/enemy_intent_testbed.tscn
scenes/testbeds/gameplay/reward_testbed.tscn
```

Battle testbed 最好有按钮：

```text
Start Battle
Draw Card
Play First Card
End Turn
Enemy Act
Dump State
Toggle Verbose Log
Reset Seed
```

这样你作为新手也能单独测。

日志建议记录：

```text
battle_started
command_received
command_rejected
card_played
effect_resolved
state_changed
event_emitted
turn_started
turn_ended
battle_ended
```

Verbose 模式下记录：

```text
seed
deck ids
hand ids
draw pile ids
discard pile ids
enemy hp
player hp
pending events
```

---

## 49_save_versioning_during_iteration.mdc

第二阶段早期不要过度追求正式存档兼容。

因为玩法会推翻很多次。

Rule 应该写：

```text
1. 第二阶段早期使用 dev save。
2. dev save 可以在大改后清空。
3. 任何 save 格式必须带 version。
4. 改 save 结构必须说明是否兼容旧版本。
5. 如果不兼容，必须提供清空开发存档的方式。
6. 正式存档迁移在玩法稳定后再做。
```

例如：

```json
{
  "version": 3,
  "mode": "dev",
  "run_seed": 123456,
  "deck": ["plasma_shot", "shield_burst"]
}
```

现在不需要太早做复杂 migration。

---

## 50_ai_gameplay_workflow.mdc

专门约束 AI 做玩法任务。

每次玩法任务开始前，AI 必须问或判断：

```text
本次是 Prototype、Candidate 还是 Stable？
```

如果用户没说，默认：

```text
Prototype
```

除非是在改已有稳定系统。

AI 每次必须输出：

```text
1. 本次模式：Prototype / Candidate / Stable
2. 影响范围
3. 修改文件
4. 是否改数据格式
5. 是否改测试
6. 是否改日志
7. 如何单独测试
8. 如何回滚
```

如果用户说“推翻之前那个系统”，AI 必须：

```text
1. 找出旧系统涉及文件。
2. 说明哪些保留、哪些删除、哪些归档。
3. 更新 current design 文档。
4. 更新测试。
5. 不要保留无用旧逻辑。
```

---

# 四、第二阶段建议的目录结构

可以在第一阶段基础上新增：

```text
src/gameplay/
  battle/
    battle_state.gd
    battle_core.gd
    battle_command.gd
    battle_event.gd
    battle_view_model_builder.gd
  cards/
    card_def.gd
    card_instance.gd
    card_loader.gd
  effects/
    effect_resolver.gd
    effect_handler.gd
  enemies/
    enemy_def.gd
    enemy_state.gd
    enemy_intent_resolver.gd
  run/
    run_state.gd
    run_controller.gd
  rewards/
    reward_generator.gd
  rng/
    run_rng.gd
  data/
    gameplay_data_registry.gd

data/gameplay/
  cards/
  enemies/
  statuses/
  rewards/
  encounters/
  run/
  balance/

data/prototypes/
  exp_001/
  exp_002/

scenes/testbeds/gameplay/
  battle_core_testbed.tscn
  card_effect_testbed.tscn
  enemy_intent_testbed.tscn

docs/design/
  current_combat_loop.md
  current_card_model.md
  current_run_loop.md
  balance_baseline.md

docs/experiments/
  exp_001_energy_combat.md
  exp_002_heat_system.md

tests/gameplay/
  battle/
  cards/
  effects/
  fixtures/
  runners/
```

---

# 五、一个很重要的稳定接口：BattleViewModel

第二阶段想要玩法频繁改但 UI 不崩，建议引入：

```text
BattleState → BattleViewModel → UI
```

UI 不直接读 BattleState。

比如 BattleState 里可能是：

```text
玩家血量
护盾
手牌
抽牌堆
弃牌堆
敌人状态
当前回合
当前阶段
```

但 UI 需要的是：

```text
手牌显示数据
按钮是否可用
敌人血条百分比
敌人意图文本
当前提示文案
可选目标
```

所以用 BattleViewModelBuilder 转换。

这样以后你把“能量系统”改成“热量系统”，UI 不一定要大改，只要 ViewModel 输出结构相对稳定。

---

# 六、玩法频繁推翻时，Rule 应该怎么处理

建议 Rule 里明确写：

## 允许推翻

```text
玩法设计允许被推翻。
AI 不应以旧测试、旧代码、旧设计为理由阻止改版。
```

## 但必须保留基础设施

推翻机制时，不能推翻这些：

```text
日志系统
测试入口
数据加载入口
UI/玩法分离
RNG 可复现
testbed 场景
当前设计文档
```

## 改版流程

当你说：

```text
我想推翻现在的能量系统，改成飞船热量系统。
```

AI 应该按这个流程：

```text
1. 确认这是 Prototype 还是 Candidate。
2. 找出 energy 相关文件。
3. 判断哪些是设计特定代码，哪些是通用基础设施。
4. 创建 exp_xxx_heat_system 实验文档。
5. 在 prototype 或 candidate 范围实现。
6. 更新 testbed。
7. 更新当前设计测试。
8. 保留不变量测试。
9. 输出手动测试步骤和日志说明。
```

---

# 七、Rule 里可以写的反例和正例

这部分很有用，建议写进第二阶段规则。

## 反例 1

错误：

```text
在 CardView.gd 的按钮点击里直接 enemy.hp -= 7
```

正确：

```text
CardView 发出 play_card_requested。
BattleCore 接收 PlayCardCommand。
EffectResolver 结算 damage。
BattleState 更新。
BattleEvent 记录 DamageDealt。
UI 根据 BattleViewModel 刷新。
```

---

## 反例 2

错误：

```text
if card_id == "plasma_shot":
    deal_damage(7)
```

正确：

```text
卡牌数据里定义 damage effect。
EffectResolver 根据 effect.type 调用 DamageEffectHandler。
```

特殊牌可以有专门 handler，但不能散落在 BattleCore 里。

---

## 反例 3

错误：

```text
每个地方都 randomize()
```

正确：

```text
RunRNG 统一生成随机数。
日志记录 seed。
测试可以复现同样结果。
```

---

## 反例 4

错误：

```text
为了测试战斗，必须从主菜单一路点到战斗。
```

正确：

```text
BattleCore 可以 headless 测。
BattleScreen 可以用 mock data 测。
BattleIntegrationTestbed 可以直接进入一场固定 seed 战斗。
```

---

# 八、我建议第二阶段第一批 Rule 的最小内容

如果你不想一下子写太多，可以先制定这 6 个。

```text
40_phase2_gameplay_iteration.mdc
```

管：

```text
Prototype / Candidate / Stable
实验目录
推翻流程
归档流程
```

```text
41_gameplay_architecture_boundaries.mdc
```

管：

```text
UI 不写玩法
BattleCore 不碰 UI
State / Command / Event / ViewModel
```

```text
42_gameplay_data_content_pipeline.mdc
```

管：

```text
卡牌、敌人、奖励数据化
ID
DataRegistry
校验
prototype 数据隔离
```

```text
43_battle_core_state_machine.mdc
```

管：

```text
BattleState
BattleCommand
BattleEvent
Battle phase
headless battle
```

```text
48_gameplay_testing_logging.mdc
```

管：

```text
每个玩法模块可单测
testbed
fixtures
verbose log
seed log
```

```text
50_ai_gameplay_workflow.mdc
```

管：

```text
AI 每次做玩法前判断模式
完成后输出测试、日志、影响范围、风险
```

等第一版战斗跑起来后，再补：

```text
44_card_effect_status_system.mdc
45_enemy_intent_ai.mdc
46_run_map_reward_event_system.mdc
47_rng_balance_simulation.mdc
49_save_versioning_during_iteration.mdc
```

---

# 九、进入第二阶段时给 Cursor 的任务模板

以后你可以这样下任务：

```text
请遵守第二阶段 gameplay rules。

目标：
实现一个 Prototype 级别的 headless 战斗核心，不接正式 UI。

模式：
Prototype。

要求：
1. 创建 BattleState、BattleCore、BattleCommand、BattleEvent 的最小版本。
2. 支持固定玩家、固定敌人、固定 5 张测试牌。
3. 支持开始战斗、打出一张伤害牌、结束回合。
4. 使用固定 seed。
5. 通过 DebugService 输出 battle channel 日志。
6. 创建 battle_core_testbed，可以单独 F6 运行。
7. 创建最小测试 fixture。
8. 不实现完整卡牌系统，不实现正式奖励系统，不改存档。

验收：
1. 运行 testbed 后能看到战斗状态。
2. 点击 Play First Card 后敌人 HP 下降。
3. Output 出现 battle_started、card_played、damage_dealt 日志。
4. 点击 Reset Seed 后结果可复现。
5. UI 没有直接修改敌人 HP。

完成后输出：
- 修改文件
- 实现摘要
- 人工测试步骤
- 预期日志
- 已知临时代码
- 如果进入 Candidate，需要补什么
```

---

# 十、结论

第二阶段 Rule 的重点不是“把玩法定死”，而是建立一套：

```text
可试错
可替换
可删除
可测试
可复现
可记录
不污染 UI
不污染正式系统
```

的玩法开发机制。

你真正要保护的是这些东西：

```text
1. UI 和玩法分离
2. 数据驱动
3. BattleState / Command / Event / ViewModel
4. 统一 RNG
5. 统一日志
6. 每个模块有 testbed
7. Prototype 和正式系统隔离
8. 推翻玩法时有迁移/归档流程
```

这样即使你后面把能量系统、卡牌费用、敌人意图、奖励逻辑、星图结构全部推翻，项目也不会变成一团乱。