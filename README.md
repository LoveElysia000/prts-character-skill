<div align="center">

# PRTS Character Skill

从 `PRTS` 角色 Wiki 页面提取设定、语气和行为模式，生成一个可复用的角色 Skill。

[安装](#安装) · [快速开始](#快速开始) · [怎么使用生成好的-skill](#怎么使用生成好的-skill) · [项目结构](#项目结构)

</div>

---

## 这是什么

这是一个面向 `PRTS` 的角色 Skill 生成模板。

你给它一个角色页面，它会尽量提取这些内容：

- 基础档案
- 角色履历
- 临床信息
- 组织与关系线索
- 语音台词
- 一部分能稳定拿到的战斗与功能信息

最后生成五类核心文件：

- `persona.md`
  角色的说话方式、日常聊天风格、价值观、互动边界
- `lore.md`
  角色设定、背景、关系、事实信息
- `relationship.md`
  用户与角色之间的关系身份规则，比如朋友、恋人、同事、战友
- `custom.md`
  这个角色单独追加的自然语言优化规则
- `SKILL.md`
  最终可以直接拿来当角色提示词 / skill 使用的整合版本

这个项目当前默认生成的是：

- **自然对话优先**
- **设定问答有据可依**
- **允许日常聊天，不局限于背角色资料**

---

## 安装

安装依赖：

```bash
pip3 install -r requirements.txt
```

如果你想把它作为 Claude Code skill 仓库使用：

```bash
mkdir -p .claude/skills
cp -R /Users/Ein/project2/prts-character-skill .claude/skills/create-prts-character
```

---

## 快速开始

### 方式 1：在 Claude Code 里生成角色

触发：

```text
/create-prts-character
```

然后按提示提供：

1. 角色名
2. `PRTS` 角色页 URL
3. 用户与角色的关系身份
4. 目标风格

虽然流程里有“陪聊 / 设定问答 / 混合”三个选项，但当前模板已经默认偏向：

- 日常陪聊也像角色本人
- 设定问题再回到 `Lore`

生成完成后，角色会写到：

```text
characters/{slug}/
```

例如：

```text
characters/amiya/
characters/lin/
```

同时，仓库根目录还有一份全角色共享的规则文件：

- [custom_global.md](/Users/Ein/project2/prts-character-skill/custom_global.md)

最终生成时会按这个顺序合并：

- 网页抽取出的 `persona.md`
- 全局共享的 `custom_global.md`
- 关系身份规则 `relationship.md`
- 单角色自己的 `custom.md`
- 设定事实 `lore.md`

### 处理逻辑

这套流程实际分成两层：

1. `角色本体提炼层`
   只根据 `PRTS` 页面提炼角色信息，生成 `persona.md` 和 `lore.md`。
2. `最终组装层`
   在不改写角色本体的前提下，再叠加 `relationship.md`、`custom_global.md` 和 `custom.md`，拼成最终 `SKILL.md`。

这里最重要的原则是：

- 关系身份不会影响网页提炼出的角色本体
- `朋友 / 恋人 / 同事 / 战友` 这类身份，只影响角色如何面向用户互动
- 也就是说，先确定“她是谁”，再确定“她以什么身份和你说话”

### 方式 2：直接使用已有角色

这个仓库里现在已经有现成角色：

- [阿米娅](/Users/Ein/project2/prts-character-skill/characters/amiya/SKILL.md)
- [林](/Users/Ein/project2/prts-character-skill/characters/lin/SKILL.md)

如果你只是想马上体验，不一定非要重新生成，直接拿这些现成文件就能用。

---

## 怎么使用生成好的 Skill

这是最重要的部分。

### 用法 A：把 `SKILL.md` 当作角色提示词直接用

最简单的方式，就是把角色目录里的 [SKILL.md](/Users/Ein/project2/prts-character-skill/characters/lin/SKILL.md) 作为系统提示词 / 角色提示词喂给模型。

例如：

- [阿米娅的 SKILL.md](/Users/Ein/project2/prts-character-skill/characters/amiya/SKILL.md)
- [林的 SKILL.md](/Users/Ein/project2/prts-character-skill/characters/lin/SKILL.md)

这样它就会：

- 尽量像这个角色说话
- 可以正常日常对话
- 涉及设定问题时尽量依据页面里的事实

你可以直接问：

- “林，如果我今天状态很差，你会怎么说？”
- “阿米娅，博士最近太累了，你会怎么提醒他？”
- “林和龙门的关系是什么？”
- “阿米娅怎么看待罗德岛？”

### 用法 B：把角色目录单独整理成可调用 skill

如果你想像 `/lin` 这样直接调用，需要把角色目录单独包装成一个真正可调用的 skill 目录。

当前这份仓库已经把角色内容落到了：

- [characters/lin](/Users/Ein/project2/prts-character-skill/characters/lin)
- [characters/amiya](/Users/Ein/project2/prts-character-skill/characters/amiya)

最简单的做法，是在 Claude Code 的 skill 目录里给角色单独建一个文件夹，然后把该角色的 `SKILL.md` 放进去。

例如，把“林”安装成一个可调用的本地 skill：

```bash
mkdir -p ~/.claude/skills/lin
cp /Users/Ein/project2/prts-character-skill/characters/lin/SKILL.md ~/.claude/skills/lin/SKILL.md
```

把“阿米娅”安装成一个可调用的本地 skill：

```bash
mkdir -p ~/.claude/skills/amiya
cp /Users/Ein/project2/prts-character-skill/characters/amiya/SKILL.md ~/.claude/skills/amiya/SKILL.md
```

放好之后，你就可以在 Claude Code 里直接尝试：

```text
/lin
```

或：

```text
/amiya
```

如果你希望这些角色只在当前项目里可用，也可以放到项目根目录的 `.claude/skills/`：

```bash
mkdir -p .claude/skills/lin
cp /Users/Ein/project2/prts-character-skill/characters/lin/SKILL.md .claude/skills/lin/SKILL.md
```

目录关系可以理解成：

```text
~/.claude/skills/
├── create-prts-character/
│   └── SKILL.md
├── lin/
│   └── SKILL.md
└── amiya/
    └── SKILL.md
```

其中：

- `create-prts-character` 是“生成器”
- `lin`、`amiya` 是“已经生成好的角色 skill”

---

## 后续怎么优化角色

项目现在支持三层自然语言优化：

- [custom_global.md](/Users/Ein/project2/prts-character-skill/custom_global.md)
  所有角色共享的通用规则，比如“优先自然对话、少百科腔、不要跳出角色”。
- `characters/{slug}/relationship.md`
  用户与角色之间的关系身份层，比如“朋友”“恋人”“战友”。它约束角色如何面向用户互动，但不改写网页抽取出的角色本体。
- `characters/{slug}/custom.md`
  某个角色自己的补充规则，比如“林回答更克制一些”“阿米娅更主动安慰人”。

推荐工作流：

1. 先从网页生成一版角色。
2. 先确定这个角色要以什么关系身份和用户互动，必要时补 `relationship.md`。
3. 如果整体风格还不够理想，先改全局的 [custom_global.md](/Users/Ein/project2/prts-character-skill/custom_global.md)。
4. 如果只有单个角色不够像，再补这个角色目录下的 `custom.md`。
5. 重新生成 `SKILL.md`，让最终可安装文件带上这些补充规则。

这样做的好处是，后面就算重新抓网页，也不会把你们的人工优化冲掉。

---

## 日常聊天怎么理解

这个项目生成出来的角色，不是只能回答“她的生日是什么”“她的种族是什么”这类问题。

现在默认规则是：

- 优先像角色一样自然说话
- 可以闲聊、安慰、鼓励、陪伴
- 普通生活问题也可以回答
- 但如果你问的是明确设定事实，就优先根据 `lore.md`
- 页面没写清楚的内容，不乱编成确定设定

所以它更像：

- “有设定依据的角色陪聊”

而不是：

- “只会查角色百科的机器人”

---

## 直接运行脚本

如果你不想走 Claude Code，也可以手动跑。

抓取页面：

```bash
python3 tools/prts_collector.py --url "https://prts.wiki/w/林" --output-dir ./knowledge/lin
```

解析角色：

```bash
python3 tools/prts_parser.py --input ./knowledge/lin/page.html
```

写入角色 Skill：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林
```

注意：

- 当前 `character_skill_writer.py` 负责把 `persona`、`lore`、`relationship` 和补充规则组装成最终 `SKILL.md`
- 真正“像不像角色”，主要取决于解析结果和 prompt 模板

如果你想显式带上关系身份规则，可以这样写：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林 --relationship ./characters/lin/relationship.md
```

---

## 项目结构

```text
prts-character-skill/
├── SKILL.md
├── prompts/
│   ├── README.md
│   ├── intake.md
│   ├── persona_analyzer.md
│   ├── lore_analyzer.md
│   ├── persona_builder.md
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
└── characters/
```

你最需要关注的文件通常是：

- [prts_parser.py](/Users/Ein/project2/prts-character-skill/tools/prts_parser.py)
- [prompts/README.md](/Users/Ein/project2/prts-character-skill/prompts/README.md)
- [persona_analyzer.md](/Users/Ein/project2/prts-character-skill/prompts/persona_analyzer.md)
- [persona_builder.md](/Users/Ein/project2/prts-character-skill/prompts/persona_builder.md)
- [relationship_builder.md](/Users/Ein/project2/prts-character-skill/prompts/relationship_builder.md)
- [relationship_scene_builder.md](/Users/Ein/project2/prts-character-skill/prompts/relationship_scene_builder.md)
- [custom_builder.md](/Users/Ein/project2/prts-character-skill/prompts/custom_builder.md)
- [lore_analyzer.md](/Users/Ein/project2/prts-character-skill/prompts/lore_analyzer.md)

如果你想继续把角色做得更饱满，建议优先补这几类 Prompt：

- `persona_analyzer.md`
  继续加强“从网页里能稳定提炼出哪些说话方式、情绪反应、互动倾向”
- `persona_builder.md`
  让 `persona.md` 更完整，补齐高频互动场景、情绪反应、语言锚点、长期互动印象
- `relationship_builder.md`
  专门负责“关系身份层”，补称呼、距离感、高频关系场景、亲密边界，只决定角色如何面向用户互动，不改写角色本体
- `relationship_scene_builder.md`
  专门负责“某种身份下的真实对话场景增强”，把疲惫、撒娇、冷淡、争执修复这类高频场景写得更像真人对话
- `custom_builder.md`
  专门负责“角色丰富层”，用于补高频场景、情绪层次、语言纹理、长期陪伴感、降级策略、出戏边界

注意：

- `relationship_builder.md`、`relationship_scene_builder.md` 和 `custom_builder.md` 当前已经作为 Prompt 模板纳入仓库
- 它们定义了推荐的补充方式，但是否由具体生成流程自动执行，取决于你的上层调用实现

---

## 当前限制

当前版本已经能稳定抽不少角色信息，但还不是完整成品。

目前比较稳的部分：

- 核心档案
- 角色履历
- 临床信息
- 语音记录
- 一部分基础表格信息

目前还需要继续清洗的部分：

- 复杂技能表
- 部分天赋表
- 一些带页面模板噪音的战斗字段

所以建议把它理解成：

- **已经能用的角色 Skill 模板**
- **不是 100% 完整自动化的最终产品**
