# 问题到 Prompt 的映射表

这是一份调优定位文档，用于把测试问题映射回对应 Prompt。

用途：

- 用于在评估后快速判断“该改哪个 prompt”
- 减少每次都凭感觉乱改

## 映射规则

### 1. 不像角色

常见表现：

- 说话方式不像
- 情绪机制不像
- 价值观、边界、互动气质不对

优先调整：

- `prompts/persona_analyzer.md`
- `prompts/persona_builder.md`

### 2. 设定事实不对

常见表现：

- 背景写错
- 人物关系写错
- 页面没写却写成事实

优先调整：

- `prompts/lore_analyzer.md`
- `prompts/lore_builder.md`
- `prompts/validator.md`

### 3. 身份不成立

常见表现：

- 恋人不像恋人
- 战友不像战友
- 同事和搭档写得没差别
- 距离感、亲密度、边界不对

优先调整：

- `prompts/relationship_builder.md`

### 4. 某个身份下的场景不自然

常见表现：

- 疲惫场景不真实
- 撒娇场景太油
- 争执修复太模板
- 生气、失望、心软没有层次

优先调整：

- `prompts/relationship_scene_builder.md`

### 5. 对话太模板 / 不像真人

常见表现：

- 太像安慰模板
- 太像小作文
- 每个场景结构都一样
- 情绪不同但语气没差别

优先调整：

- `prompts/custom_builder.md`
- `prompts/relationship_scene_builder.md`
- `prompts/validator.md`

### 6. 修错层

常见表现：

- 本来是关系问题，却改了角色本体
- 本来是表现问题，却改成设定事实

优先调整：

- `prompts/merger.md`
- `prompts/correction_handler.md`
- `prompts/validator.md`

## 一句话判断法

- 在纠正“她是谁”：
  改 `persona_*`
- 在纠正“她的设定事实”：
  改 `lore_*`
- 在纠正“她以什么身份对你说话”：
  改 `relationship_builder.md`
- 在纠正“她在某个场景下怎么说”：
  改 `relationship_scene_builder.md`
- 在纠正“她整体为什么太模板、不够像真人”：
  改 `custom_builder.md`
