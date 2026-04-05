# PRTS Character Skill PRD

## 产品概述

`PRTS Character Skill` 是一个面向角色 Wiki 的 meta-skill。

用户提供 `PRTS` 角色页 URL，系统自动抓取并分析页面内容，生成一个可直接调用的角色 Skill。该 Skill 同时具备两类能力：

- 角色扮演能力
- 设定问答能力

## 目标用户

- 想做角色扮演助手的人
- 想把游戏角色蒸馏成长期可复用 skill 的创作者
- 想以某个游戏 Wiki 为语料做角色模板的人

## 输入

- 角色名
- `PRTS` 角色页 URL
- 用户期望风格

可选附加输入：

- 额外剧情页面
- 角色语音页
- 用户手动补充设定

## 输出

目录结构：

```text
characters/{slug}/
├── meta.json
├── persona.md
├── lore.md
├── SKILL.md
└── versions/
```

## 系统流程

```text
输入 URL
  ↓
抓取页面
  ↓
解析为标准化角色资料
  ↓
提取 persona 特征
  ↓
提取 lore 事实
  ↓
写入角色 Skill
  ↓
支持后续修正和回滚
```

## 第一版范围

第一版只支持：

- 单个 `PRTS` 页面
- 单角色生成
- 混合型角色 Skill

不包含：

- 多页面自动聚合
- 跨站点支持
- 自动图片/语音资产下载

## 核心模块

1. 页面抓取器
2. 页面解析器
3. Persona 分析器
4. Lore 分析器
5. Character Skill 写入器
6. 版本管理器
