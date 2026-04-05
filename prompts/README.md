# Prompts 分层说明

这个目录下的 Prompt 按职责分成 5 层。

## 1. 输入层

- `intake.md`

负责生成前收集信息：

- 角色名
- 页面 URL
- 关系身份
- Skill 偏向

## 2. 固定数据层

这层只负责“从网页里提炼角色本体和设定事实”，不受关系身份影响。

- `persona_analyzer.md`
  从页面资料里提炼人格、说话方式、情绪反应、互动倾向
- `persona_builder.md`
  把人格信息整理成 `persona.md`
- `lore_analyzer.md`
  从页面资料里提炼可问答的设定事实
- `lore_builder.md`
  把事实信息整理成 `lore.md`

原则：

- 只提炼网页有依据的内容
- 页面没写就不要补成事实
- 朋友、恋人、同事等身份不参与这一层

## 3. 后置增强层

这层在固定数据层之后使用，用于增强互动表现，但不能改写网页固定数据。

- `relationship_builder.md`
  生成 `relationship.md`，定义角色如何以指定关系身份面向用户互动，以及情绪表达在该身份下如何偏移
- `custom_builder.md`
  生成 `custom.md`，定义高频场景、情绪表达节奏、语言纹理、长期陪伴感、降级策略、出戏边界
- `relationship_scene_builder.md`
  专门补“某种身份下不够像真实对话场景”的问题，把高频关系场景和情绪表达链写得更接近真人对话

原则：

- 只能增强表现，不改写角色设定
- 所有规则都必须服从 `persona.md` 和 `lore.md`

## 4. 修正层

- `merger.md`
  用户补充新资料或剧情时，先判断属于固定数据层还是后置增强层，再做增量合并
- `correction_handler.md`
  用户指出 OOC 或设定错误时，先判断层级，再决定修正 `persona`、`lore`、`relationship` 还是 `custom`

## 5. 校验层

- `validator.md`
  生成后做分层自检，检查固定数据层、关系身份层、角色丰富层有没有互相越界

## 推荐补充顺序

1. `intake.md`
2. `persona_analyzer.md`
3. `persona_builder.md`
4. `lore_analyzer.md`
5. `lore_builder.md`
6. `relationship_builder.md`
7. `custom_builder.md`
8. `relationship_scene_builder.md`
9. `merger.md` / `correction_handler.md`
10. `validator.md`

## 情绪表达分层规则

- `persona_*`
  负责角色本体的情绪机制，例如这个角色平静、不满、生气、失望、无奈、心软时本来怎么表现
- `relationship_builder.md`
  负责同样的情绪在不同关系身份下会如何偏移，例如更克制、更直接、更容易缓和
- `relationship_scene_builder.md`
  负责把这些情绪放进具体场景，写出真实对话中的起手、承接、爆发和收束
- `custom_builder.md`
  负责沉淀长期有效的表达规律，例如情绪节奏、说话纹理、事后如何收束

说明：

- `relationship_builder.md` 和 `custom_builder.md` 当前主要作为规范和模板使用
- 它们定义“应该如何生成这两层内容”，但不等于所有上层调用流程都已自动接入
- `relationship_scene_builder.md` 适合在你发现“身份没问题，但真实对话感不够”时补用
- Prompt 用来定义生成方向，不用来写固定答案

## 一句话原则

- 网页数据负责定角色
- 关系身份负责定互动位置
- 自定义补充负责补高频场景、情绪层次、语言纹理、关系持续感和降级策略
- 修正层负责按层级持续纠偏，避免把固定数据和增强表现混改
