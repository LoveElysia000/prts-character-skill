---
name: create-prts-character
description: "Generate a character skill from a PRTS wiki page. Extract lore + persona and build a reusable mixed-role skill. | 从 PRTS 角色 Wiki 页面生成角色 Skill，抽取设定与人格。"
argument-hint: "[character-name-or-url]"
version: "0.1.0"
user-invocable: true
allowed-tools: Read, Write, Edit, Bash
---

# PRTS Character Skill Builder

## 触发条件

当用户说以下任意内容时启动：

- `/create-prts-character`
- “帮我做一个角色 skill”
- “根据这个 PRTS 页面生成角色”
- “把这个角色蒸馏成 skill”

---

## 核心目标

根据 `PRTS` 角色页面，生成一个混合型角色 Skill：

- 能像角色说话
- 能回答角色设定问题
- 遇到设定缺失时明确说“原页面未提供”

---

## 工具使用规则

| 任务 | 使用工具 |
|------|---------|
| 读取页面 HTML / 文本 | `Bash` |
| 读取 Markdown / TXT | `Read` |
| 抓取 PRTS 页面 | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/prts_collector.py` |
| 解析 PRTS 页面 | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/prts_parser.py` |
| 写入角色 Skill | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/character_skill_writer.py` |
| 版本管理 | `Bash` → `python3 ${CLAUDE_SKILL_DIR}/tools/version_manager.py` |

基础目录：`./characters/{slug}/`

---

## 主流程

### Step 1：收集输入

参考 `prompts/intake.md`，只问 4 个问题：

1. 角色名
2. 页面 URL
3. 关系身份：朋友 / 恋人 / 同事 / 战友 / 自定义
4. 目标风格：陪聊 / 设定问答 / 混合

### Step 2：抓取页面

优先使用：

```bash
python3 ${CLAUDE_SKILL_DIR}/tools/prts_collector.py \
  --url "{url}" \
  --output-dir ./knowledge/{slug}
```

默认输出：

- `page.html`
- `page.txt`
- `collection_summary.json`

### Step 3：解析角色资料

```bash
python3 ${CLAUDE_SKILL_DIR}/tools/prts_parser.py \
  --input ./knowledge/{slug}/page.html \
  --output ./knowledge/{slug}/character_profile.json
```

### Step 4：生成固定数据层

网页解析结果要先拆成两部分：

- `persona.md`
- `lore.md`

其中：

- `persona.md` 负责角色本体的人格、说话方式、互动边界
- `lore.md` 负责角色设定事实、背景、关系、能力等可问答内容
- 这一步只根据网页资料提炼，不受“朋友 / 恋人 / 同事”等关系身份影响

推荐使用的 Prompt：

- `prompts/persona_analyzer.md`
- `prompts/persona_builder.md`
- `prompts/lore_analyzer.md`
- `prompts/lore_builder.md`

### Step 5：生成后置增强层

在固定数据层完成后，再补两层后置内容：

- `relationship.md`
  只负责“角色如何以指定关系身份面向用户互动”
- `custom.md`
  只负责“让角色更饱满、更稳定、更像活人”

推荐使用的 Prompt：

- `prompts/relationship_builder.md`
- `prompts/relationship_scene_builder.md`
- `prompts/custom_builder.md`

这两层只能增强表现，不能改写网页提炼出的角色本体和设定事实。
当前仓库已经提供了对应 Prompt 模板，但是否自动执行它们，取决于上层调用流程。

### Step 6：组装最终 Skill

再组合成完整 `SKILL.md`，组合顺序为：

- `persona.md`
- `custom_global.md`
- `relationship.md`
- `custom.md`
- `lore.md`

### Step 7：持续修正

当用户说：

- “这句不像她”
- “这个设定不对”
- “补充这一段剧情”

进入增量修正模式，先判断问题属于固定数据层还是后置增强层，再使用 `prompts/merger.md` 和 `prompts/correction_handler.md` 写回对应文件。
