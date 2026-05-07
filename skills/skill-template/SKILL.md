---
name: your-skill-name
description: Concise capability and trigger description. Use when the user asks for <task/domain>, mentions <tool/framework/file type>, or needs <workflow outcome>. Include all important trigger phrases because Codex only sees this metadata before loading the skill body.
metadata:
  author: Your Name
  version: "0.1.0"
  source: Optional source link, docs version, or generation note
---

# Your Skill Name

> 用一句话说明这个 skill 的默认立场、适用版本或核心约束。

## Preferences

- 优先使用项目已有约定、工具链和目录结构。
- 只把执行任务必须知道的内容放在 `SKILL.md`，细节资料放到 `references/` 并按需读取。
- 当任务需要确定性、重复执行或容易出错时，优先使用 `scripts/` 中的脚本。
- 当用户请求超出本 skill 边界时，简要说明边界并使用最接近的可靠方法继续。

## Workflow

1. 识别用户目标、输入文件、输出格式和验收标准。
2. 检查本地上下文，确认是否存在项目规范、已有实现或测试命令。
3. 只读取当前任务需要的参考文件；不要批量加载所有资料。
4. 执行最小必要改动，保持风格和项目现有模式一致。
5. 运行与风险匹配的验证步骤，并在结果中说明验证范围。

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Core Patterns | 本 skill 的核心模式、约束和示例。 | [topic](references/topic.md) |

## Quick Reference

### Minimal Output Pattern

```md
目标：<用户实际目标>
输入：<文件、链接、需求或上下文>
处理：<关键步骤>
验证：<命令、检查点或人工验收标准>
输出：<最终交付物>
```

### Decision Rules

- 如果规则稳定且短小，写在 `SKILL.md`。
- 如果规则详细、按框架变化或超过几屏，移到 `references/<topic>.md`。
- 如果流程依赖精确命令、格式转换或批处理，放到 `scripts/`。
- 如果输出依赖模板、图片、字体或样例文件，放到 `assets/`。

## Validation Checklist

- `name` 使用小写短横线命名，且与目录名一致。
- `description` 明确写出触发场景、相关工具或文件类型。
- `SKILL.md` 保持精简，详细资料通过表格链接到 `references/`。
- 所有可选资源都能解释其用途，没有无关 README、变更日志或安装文档。
- 示例命令与项目包管理器、运行环境和用户规范一致。
