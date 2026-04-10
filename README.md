<div align="center">

# PRTS Character Skill

面向《明日方舟》`PRTS Wiki` 角色页的 Character Skill 生成项目。

[安装与运行](INSTALL.md) · [Prompt 规则中心](prompts/README.md) · [评估与测试](docs/README.md)

</div>

---

## 项目定位

这里的 `PRTS`，指的是《明日方舟》的 Wiki 站点 `prts.wiki`。

这个项目的核心目标，不是做一个泛用百科抓取器，而是围绕《明日方舟》角色页资料，整理出一套可复用的 Character Skill 生成流程。它会把角色页面中的设定、口吻、互动边界和可问答信息拆成不同层，再组合成最终可使用的 `SKILL.md`。

换句话说，这是一个服务于《明日方舟》角色塑造的项目：

- 从 `PRTS Wiki` 页面提取角色资料
- 把资料分成“角色本体”和“互动增强”两大层
- 生成更接近真实对话体验的角色 Skill

---

## 这个项目解决什么问题

很多角色类 Prompt 只能回答设定问题，但一进入对话就会变得很模板化。这个项目希望同时解决三件事：

- 角色设定能答对
- 角色说话方式尽量稳定
- 在指定关系身份下，互动更像“这个角色本人”而不是通用聊天模板

因此，项目当前同时关注两部分：

1. 角色本体提炼  
   从《明日方舟》`PRTS Wiki` 页面中抽取有依据的设定事实、人格倾向、说话方式。

2. 对话表现增强  
   在不破坏原设的前提下，叠加关系身份、语言纹理、长期互动感和降级策略。

---

## 核心产物

项目最终会围绕单个角色生成一组分层文件：

- `persona.md`  
  角色本体的人格、说话方式、情绪机制、互动边界
- `lore.md`  
  角色设定事实、经历、关系、能力等可问答信息
- `relationship.md`  
  角色以什么身份面向用户互动，只负责关系位置与距离感
- `custom.md`  
  用于补足高频场景、语言纹理、长期陪伴感、降级策略等
- `SKILL.md`  
  最终整合后的 Character Skill

常见输出目录结构如下：

```text
characters/{slug}/
├── meta.json
├── persona.md
├── lore.md
├── relationship.md
├── custom.md
├── SKILL.md
└── versions/
```

---

## 分层原则

### 1. 固定数据层

固定数据层只负责网页中有依据的角色信息，产出：

- `persona.md`
- `lore.md`

这层的原则是：

- 只提炼《明日方舟》`PRTS Wiki` 页面里有证据的内容
- 页面没写的内容，不补成既定事实
- 不受朋友、恋人、同事、战友等关系身份影响

### 2. 后置增强层

固定数据层完成后，再叠加互动增强层：

- `relationship.md`
- `custom.md`
- `custom_global.md`

这层的原则是：

- 只增强互动表现
- 不改写角色本体和设定事实
- 所有增强都必须服从 `persona.md` 和 `lore.md`

### 3. 校验与调优层

项目还提供一套专门的校验和回改入口，用来处理：

- OOC 纠偏
- 设定错误修正
- 实测记录
- 回归测试
- 问题定位

---

## 最短使用路径

如果你只想尽快跑通一次完整流程，可以按下面顺序来：

1. 安装依赖  
   见 [INSTALL.md](INSTALL.md)

2. 新建一个角色工作目录  
   下面以 `林` 为例，约定资料先放到 `./knowledge/lin/`

3. 抓取《明日方舟》角色页

```bash
python3 tools/prts_collector.py --url "https://prts.wiki/w/林" --output-dir ./knowledge/lin
```

运行后你会在 `./knowledge/lin/` 看到：

- `page.html`
- `page.txt`
- `collection_summary.json`

4. 解析页面内容

```bash
python3 tools/prts_parser.py --input ./knowledge/lin/page.html --output ./knowledge/lin/character_profile.json
```

运行后会得到：

- `./knowledge/lin/character_profile.json`

5. 按 `prompts/` 中的分层规则整理角色资料  
   至少准备下面四份文件：

- `persona.md`
- `lore.md`
- `relationship.md`
- `custom.md`

如果你现在只是想先跑通流程，也可以先只准备 `persona.md` 和 `lore.md`，把另外两层留空，写入器会自动生成默认占位内容。

6. 写入最终 Skill

最完整的写法是显式传入各层文件：

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

如果你只是想先把目录结构创建出来，也可以先用最简命令：

```bash
python3 tools/character_skill_writer.py --action create --slug lin --name 林
```

这条命令会在 `./characters/lin/` 下生成：

- `persona.md`
- `lore.md`
- `relationship.md`
- `custom.md`
- `SKILL.md`
- `meta.json`
- `versions/`

如果你已经有部分文件，也可以只传你已经准备好的那几层，例如：

```bash
python3 tools/character_skill_writer.py \
  --action create \
  --slug lin \
  --name 林 \
  --persona ./characters/lin/persona.md \
  --lore ./characters/lin/lore.md \
  --relationship ./characters/lin/relationship.md
```

---

## 仓库结构怎么理解

这个仓库可以简单拆成三部分：

- `tools/`  
  负责抓取、解析、写入、版本管理等执行逻辑
- `prompts/`  
  负责定义怎么生成、怎么分层、怎么纠偏、怎么校验
- `docs/`  
  负责记录评估方法、测试案例、问题映射和产品范围

如果你是第一次看这个项目，推荐这样进入：

1. 先看本页，了解项目目标和分层思路
2. 再看 [INSTALL.md](INSTALL.md)，跑通基础流程
3. 然后看 [prompts/README.md](prompts/README.md)，理解 Prompt 规则中心
4. 最后看 [docs/README.md](docs/README.md)，了解怎么评估和回改

---

## 文档入口

- [INSTALL.md](INSTALL.md)  
  安装、运行和基础使用流程
- [prompts/README.md](prompts/README.md)  
  Prompt 分层、生成规则、校验与修正入口
- [docs/README.md](docs/README.md)  
  评估、测试、样本记录、问题定位入口
- [docs/PRD.md](docs/PRD.md)  
  项目当前目标、边界和范围说明

如果按用途理解：

- `README.md` 回答“这是什么、适合从哪里开始”
- `prompts/` 回答“怎么生成”
- `docs/` 回答“怎么验证、怎么记录、怎么回改”

---

## 当前范围

当前这个项目更准确地说是：

- 面向《明日方舟》`PRTS Wiki` 的单角色 Skill 生成框架
- 已经具备较完整 Prompt 分层和调优思路的项目
- 适合继续按具体角色、具体关系身份做细化校准的基础工程

当前支持：

- 单个 `PRTS Wiki` 页面输入
- 单角色生成
- 角色本体层与关系层分离
- Prompt 分层调优
- Claude 实测记录与回归测试

当前不包含：

- 多页面自动聚合
- 跨站点支持
- 模型训练或微调
- 全链路自动调优执行
