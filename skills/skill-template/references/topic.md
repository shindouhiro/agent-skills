# Core Patterns

## When To Read

当用户需求涉及本主题的详细规则、边界情况、代码示例或长参考资料时读取本文件。

## Rules

- 在这里放置不适合塞进 `SKILL.md` 的详细规则。
- 每条规则都应该能直接影响 agent 的操作决策。
- 避免复制公开文档的大段内容；只保留任务中真正需要的判断标准、接口约定和示例。

## Examples

### Good

```md
用户要求：<具体请求>
应使用：<工作流、脚本或命令>
验证方式：<可重复的检查>
```

### Avoid

- 与 skill 执行无关的背景介绍。
- 只给人阅读、不会影响 agent 行动的长篇说明。
- 与 `SKILL.md` 重复的内容。

## Maintenance Notes

- 当上游框架、API 或工具版本变化时，更新相关规则和示例。
- 如果本文件继续膨胀，把不同主题拆成 `references/<specific-topic>.md`，并在 `SKILL.md` 的表格中显式链接。
