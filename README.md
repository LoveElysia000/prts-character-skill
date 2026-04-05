<div align="center">

# PRTS Character Skill

从 `PRTS` 角色 Wiki 页面提取资料，生成一个可复用的角色 Skill。

[安装](#安装) · [快速开始](#快速开始) · [核心原则](#核心原则) · [项目结构](#项目结构)

</div>

---

## 这是什么

这是一个面向 `PRTS` 角色页的角色 Skill 生成模板。

它的目标不是只做“角色百科问答”，而是生成一套更接近真实对话的角色提示：

- 能回答设定问题
- 能保持角色说话方式
- 能在指定关系身份下和用户互动
- 能通过后置补充规则继续调优

项目当前的核心产物是：

- `persona.md`
  角色本体的人格、说话方式、情绪机制、互动边界
- `lore.md`
  角色设定事实、背景、关系、能力等可问答信息
- `relationship.md`
  用户与角色之间的关系身份层，只决定角色如何面向用户互动
- `custom.md`
  角色丰富层，用来补高频场景、语言纹理、长期陪伴感、降级策略等
- `SKILL.md`
  最终整合后的可用 Skill

---

## 安装

安装依赖：

```bash
pip3 install -r requirements.txt
```

如果你想把它作为 Claude Code skill 仓库使用：

```bash
mkdir -p .claude/skills
cp -R ./prts-character-skill .claude/skills/create-prts-character
```

---

## 快速开始

### 在 Claude Code 里使用

触发：

```text
/create-prts-character
```

生成前会收集 4 个信息：

1. 角色名
2. `PRTS` 页面 URL
3. 用户与角色的关系身份
4. Skill 偏向

生成完成后，角色目录会写到：

```text
characters/{slug}/
```

### 手动运行

抓取页面：

```bash
python3 tools/prts_collector.py --url "https://prts.wiki/w/林" --output-dir ./knowledge/lin
```

解析页面：

```bash
python3 tools/prts_parser.py --input ./knowledge/lin/page.html --output ./knowledge/lin/character_profile.json
```

写入最终 Skill：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林
```

如果你已经准备好了关系身份层，可以显式传入：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林 --relationship ./characters/lin/relationship.md
```

---

## 核心原则

这套流程最重要的是分层。

### 1. 固定数据层

网页只负责提炼角色本体，生成：

- `persona.md`
- `lore.md`

这一层只处理网页有依据的内容，不受朋友、恋人、同事、战友等关系身份影响。

### 2. 后置增强层

固定数据层完成后，再叠加：

- `relationship.md`
- `custom.md`
- `custom_global.md`

这一层只增强互动表现，不改写网页提炼出的角色本体和设定事实。

### 3. Prompt 是规则中心

`tools/` 负责执行：

- 抓取
- 解析
- 写入
- 回滚

真正的生成规则、分层原则、纠偏方法、校验方法，都放在 `prompts/`。

如果你想调优实际生成效果，优先看：

- `prompts/README.md`

---

## Prompt 层

`prompts/` 现在按职责分成 5 层：

1. 输入层
2. 固定数据层
3. 后置增强层
4. 修正层
5. 校验层

最值得关注的几个文件：

- `prompts/README.md`
- `prompts/persona_analyzer.md`
- `prompts/persona_builder.md`
- `prompts/relationship_builder.md`
- `prompts/relationship_scene_builder.md`
- `prompts/custom_builder.md`
- `prompts/validator.md`

如果你想继续把角色做得更像真实对话，主要也是调这里，而不是调 `tools/`。

---

## 生成顺序

推荐顺序：

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

补一句原则：

- Prompt 用来定义生成方向，不用来写固定答案。

---

## 项目结构

```text
prts-character-skill/
├── SKILL.md
├── prompts/
│   ├── README.md
│   ├── intake.md
│   ├── persona_analyzer.md
│   ├── persona_builder.md
│   ├── lore_analyzer.md
│   ├── lore_builder.md
│   ├── relationship_builder.md
│   ├── relationship_scene_builder.md
│   ├── custom_builder.md
│   ├── merger.md
│   ├── correction_handler.md
│   └── validator.md
├── tools/
│   ├── prts_collector.py
│   ├── prts_parser.py
│   ├── character_skill_writer.py
│   └── version_manager.py
├── knowledge/
├── characters/
└── custom_global.md
```

---

## 当前限制

当前版本已经能稳定抽取不少角色信息，但还不是完整自动化成品。

目前比较稳的部分：

- 核心档案
- 履历和档案文本
- 语音记录
- 一部分基础表格信息

目前还容易脏的部分：

- 复杂技能表
- 部分天赋表
- 带模板噪音的战斗字段

所以更准确地说，它现在是一套：

- 已经能用的角色 Skill 生成框架
- Prompt 分层已经比较完整的调优体系
- 但仍需要按具体角色和具体身份继续校准效果的项目
