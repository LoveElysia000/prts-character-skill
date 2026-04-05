<div align="center">

# PRTS Character Skill

从 `PRTS` 角色页提取资料，生成可复用的角色 Skill。

[安装](INSTALL.md) · [Prompt 规则中心](prompts/README.md) · [评估与测试](docs/README.md)

</div>

---

## 这是什么

这是一个面向 `PRTS` 角色页的角色 Skill 生成框架。

它的目标不是只做“角色百科问答”，而是生成一套更接近真实对话的角色提示：

- 能回答设定问题
- 能保持角色说话方式
- 能在指定关系身份下和用户互动
- 能通过后置补充规则持续调优

核心产物：

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

## 核心原则

### 固定数据层

网页只负责提炼角色本体，生成：

- `persona.md`
- `lore.md`

这一层只处理网页有依据的内容，不受朋友、恋人、同事、战友等关系身份影响。

### 后置增强层

固定数据层完成后，再叠加：

- `relationship.md`
- `custom.md`
- `custom_global.md`

这一层只增强互动表现，不改写网页提炼出的角色本体和设定事实。

### Prompt 是规则中心

`tools/` 负责执行：

- 抓取
- 解析
- 写入
- 回滚

真正的生成规则、分层原则、纠偏方法、校验方法，都放在 `prompts/`。

如果你想调优实际生成效果，优先看：

- [prompts/README.md](prompts/README.md)

如果你想做回归测试、记录 Claude 实测结果，优先看：

- [docs/README.md](docs/README.md)

---

## 最短使用路径

1. 安装依赖  
   见 [INSTALL.md](INSTALL.md)

2. 抓取页面

```bash
python3 tools/prts_collector.py --url "https://prts.wiki/w/林" --output-dir ./knowledge/lin
```

3. 解析页面

```bash
python3 tools/prts_parser.py --input ./knowledge/lin/page.html --output ./knowledge/lin/character_profile.json
```

4. 按 `prompts/` 里的规则整理：

- `persona.md`
- `lore.md`
- `relationship.md`
- `custom.md`

5. 写入最终 Skill

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林
```

---

## 文档分工

- [INSTALL.md](INSTALL.md)
  安装和运行
- [prompts/README.md](prompts/README.md)
  Prompt 分层、生成规则、情绪分层、纠偏与校验
- [docs/README.md](docs/README.md)
  评估、测试、样本记录、问题映射
- [docs/PRD.md](docs/PRD.md)
  当前产品目标和范围说明

如果按“用途”来看，可以简单理解成：

- `prompts/`
  回答“怎么生成”
- `docs/`
  回答“怎么测试、怎么记录、怎么回改”

---

## 当前状态

这套项目现在更准确地说是：

- 已经能用的角色 Skill 生成框架
- Prompt 分层已经比较完整的调优体系
- 但仍需要按具体角色和具体身份继续校准效果的项目
