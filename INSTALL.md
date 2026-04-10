# 安装与运行

## 1. 安装依赖

```bash
pip3 install -r requirements.txt
```

安装完成后，命令行里会用到 3 个脚本：

- `tools/prts_collector.py`
  抓取 `PRTS Wiki` 页面，保存原始 HTML 和纯文本
- `tools/prts_parser.py`
  解析抓取结果，生成轻量角色资料 JSON
- `tools/character_skill_writer.py`
  把各层 Markdown 文件写成最终角色目录和 `SKILL.md`

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

下面用《明日方舟》角色 `林` 举例。

### 第一步：抓取页面

建议先给角色建一个独立资料目录：

```bash
python3 tools/prts_collector.py --url "https://prts.wiki/w/林" --output-dir ./knowledge/lin
```

这一步会生成：

- `./knowledge/lin/page.html`
- `./knowledge/lin/page.txt`
- `./knowledge/lin/collection_summary.json`

### 第二步：解析角色页

```bash
python3 tools/prts_parser.py --input ./knowledge/lin/page.html --output ./knowledge/lin/character_profile.json
```

这一步会生成：

- `./knowledge/lin/character_profile.json`

### 第三步：整理分层文件

根据 `prompts/` 里的规则，整理角色资料，通常至少会涉及：

- `persona.md`
- `lore.md`
- `relationship.md`
- `custom.md`

如果你只是想先创建目录，也可以暂时不传这些文件，写入器会生成默认占位内容。

### 第四步：写入最终 Skill

最简单的用法：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林
```

这会直接创建：

```text
./characters/lin/
```

如果你已经准备好了分层文件，推荐显式传入：

```bash
python3 tools/character_skill_writer.py \
  --action create \
  --slug lin \
  --name 林 \
  --persona ./characters/lin/persona.md \
  --lore ./characters/lin/lore.md \
  --relationship ./characters/lin/relationship.md \
  --custom ./characters/lin/custom.md \
  --source-url "https://prts.wiki/w/林"
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
