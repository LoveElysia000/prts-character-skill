# 安装与运行

## 1. 安装依赖

```bash
pip3 install -r requirements.txt
```

## 2. 作为 Claude Code skill 仓库使用

```bash
mkdir -p .claude/skills
cp -R ./prts-character-skill .claude/skills/create-prts-character
```

之后可以在 Claude Code 里触发：

```text
/create-prts-character
```

## 3. 手动运行

抓取页面：

```bash
python3 tools/prts_collector.py --url "https://prts.wiki/w/林" --output-dir ./knowledge/lin
```

解析角色：

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

## 4. 生成后的文件

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

## 5. 后续调优

如果你想继续调整生成效果，优先看：

- [prompts/README.md](prompts/README.md)
- [docs/README.md](docs/README.md)
