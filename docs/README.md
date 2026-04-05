# 评估与测试文档

这个目录放的是“生成之后怎么测、怎么记、怎么定位问题”的文档。
它不参与 Skill 生成，也不应该写回 `SKILL.md` 本体。

## 文档列表

### A. 项目与范围

- [PRD.md](PRD.md)
  当前产品目标、范围和分层说明

### B. 测试执行

- [claude_test_workflow.md](claude_test_workflow.md)
  Claude 实测时的操作流程
- [eval_cases.md](eval_cases.md)
  固定场景测试集，用于回归测试
- [eval_log_template.md](eval_log_template.md)
  Claude 实测记录模板

### C. 调优定位

- [issue_mapping.md](issue_mapping.md)
  问题到 Prompt 的映射表

## 推荐使用顺序

1. 先看 [PRD.md](PRD.md)，确认当前项目范围
2. 看 [claude_test_workflow.md](claude_test_workflow.md)，明确 Claude 里怎么测、怎么记
3. 用 [eval_cases.md](eval_cases.md) 固定测试场景
4. 用 [eval_log_template.md](eval_log_template.md) 记录 Claude 实测结果
5. 用 [issue_mapping.md](issue_mapping.md) 判断该回改哪个 Prompt

## 按场景使用

- 想知道项目现在支持什么：
  先看 [PRD.md](PRD.md)
- 想知道 Claude 里怎么测：
  先看 [claude_test_workflow.md](claude_test_workflow.md)
- 想直接拿一组固定问题去测：
  先看 [eval_cases.md](eval_cases.md)
- 想把测试结果记下来：
  先看 [eval_log_template.md](eval_log_template.md)
- 想知道“不像”的结果该改哪一层：
  先看 [issue_mapping.md](issue_mapping.md)

## 核心原则

- 测试记录只放在 `docs/`
- 不要把测试结论写回 skill 本体
- 评估时先看角色，再看身份，再看真实对话感，最后看是否越界
