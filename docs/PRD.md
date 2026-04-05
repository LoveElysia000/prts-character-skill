# PRTS Character Skill PRD

这是一份项目范围文档，用于说明当前产品目标、边界和分层架构。

## 产品概述

`PRTS Character Skill` 是一个面向角色 Wiki 的角色 Skill 生成框架。

用户提供 `PRTS` 角色页 URL，系统抓取并解析页面内容，再按分层规则生成可复用的角色 Skill。

这个项目当前同时服务两件事：

- 生成角色本体
- 为关系身份和真实对话效果提供后置增强空间

## 当前目标

生成一套能够同时满足下面三点的角色 Skill：

- 能回答设定问答
- 能保持角色本体
- 能在指定关系身份下更接近真实对话

## 输入

必填输入：

- 角色名
- `PRTS` 角色页 URL
- 用户与角色的关系身份
- Skill 偏向

可选输入：

- 用户补充的关系身份规则
- 用户补充的角色丰富规则
- 后续纠错和人工调优结论

## 输出

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

## 分层架构

### 1. 固定数据层

来自网页，只负责：

- `persona.md`
- `lore.md`

原则：

- 只提炼网页有依据的内容
- 不受关系身份影响

### 2. 后置增强层

用于增强互动表现，只负责：

- `relationship.md`
- `custom.md`
- `custom_global.md`

原则：

- 只增强表现
- 不改写网页提炼出的角色本体和设定事实

### 3. 校验与调优层

用于：

- 纠偏
- 回归测试
- Claude 实测记录
- 问题定位

## 当前范围

当前支持：

- 单个 `PRTS` 页面
- 单角色生成
- 混合型角色 Skill
- 关系身份层
- 角色丰富层
- Prompt 分层调优
- Claude 实测记录与回归测试

当前不包含：

- 多页面自动聚合
- 跨站点支持
- 自动训练 / 微调模型
- 自动执行全部 Prompt 调优链路

## 文档边界

- `README.md`
  项目入口和最短使用路径
- `prompts/README.md`
  生成规则中心
- `docs/README.md`
  评估、测试、记录和问题定位入口

## 核心模块

1. 页面抓取器
2. 页面解析器
3. 固定数据层 Prompt
4. 后置增强层 Prompt
5. 纠偏与校验 Prompt
6. Character Skill 写入器
7. 版本管理器
