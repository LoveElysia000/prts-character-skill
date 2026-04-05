# 安装说明

## 1. 安装依赖

```bash
pip3 install -r requirements.txt
```

## 2. 作为 Claude Code skill 仓库使用

```bash
mkdir -p ~/.claude/skills
cp -R /Users/Ein/project2/prts-character-skill ~/.claude/skills/create-prts-character
```

之后可以在 Claude Code 里触发：

```text
/create-prts-character
```

## 3. 直接使用已经生成好的角色

当前仓库里已有：

- `/Users/Ein/project2/prts-character-skill/characters/amiya/SKILL.md`
- `/Users/Ein/project2/prts-character-skill/characters/lin/SKILL.md`

最简单的用法，就是把其中一个 `SKILL.md` 当作角色提示词直接给模型使用。

如果你想在 Claude Code 里像 `/lin` 这样调用，可以这样安装：

```bash
mkdir -p ~/.claude/skills/lin
cp /Users/Ein/project2/prts-character-skill/characters/lin/SKILL.md ~/.claude/skills/lin/SKILL.md
```

```bash
mkdir -p ~/.claude/skills/amiya
cp /Users/Ein/project2/prts-character-skill/characters/amiya/SKILL.md ~/.claude/skills/amiya/SKILL.md
```

然后在 Claude Code 里尝试：

```text
/lin
```

或：

```text
/amiya
```

## 4. 全局规则和单角色补充

全角色共享规则在：

- `/Users/Ein/project2/prts-character-skill/custom_global.md`

单角色补充规则在：

- `/Users/Ein/project2/prts-character-skill/characters/{slug}/relationship.md`
- `/Users/Ein/project2/prts-character-skill/characters/{slug}/custom.md`

推荐顺序：

1. 先网页生成角色。
2. 先确认用户与角色的关系身份，必要时补 `characters/{slug}/relationship.md`。
3. 再调全局的 `custom_global.md`。
4. 再按角色补 `characters/{slug}/custom.md`。
5. 重新生成或覆盖角色 `SKILL.md`。

## 5. 直接运行脚本

抓取页面：

```bash
python3 tools/prts_collector.py --url "https://prts.wiki/w/林" --output-dir ./knowledge/lin
```

解析角色：

```bash
python3 tools/prts_parser.py --input ./knowledge/lin/page.html
```

写入 Skill：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林
```

如果你想带上单角色补充规则：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林 --custom ./characters/lin/custom.md
```

如果你想显式带上关系身份规则：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林 --relationship ./characters/lin/relationship.md
```

## 6. 生成后的文件在哪

角色会写到：

```text
characters/{slug}/
```

里面通常有：

- `persona.md`
- `lore.md`
- `relationship.md`
- `custom.md`
- `SKILL.md`
- `meta.json`

如果你要把生成结果安装到 Claude 的全局 skill 目录，建议把 `SKILL.md` 复制到：

```text
~/.claude/skills/{slug}/SKILL.md
```

例如：

```bash
mkdir -p ~/.claude/skills/lin
cp characters/lin/SKILL.md ~/.claude/skills/lin/SKILL.md
```
