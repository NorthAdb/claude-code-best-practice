# 技能（Skills）最佳实践

> 中文翻译整理自 `best-practice/claude-skills.md`
> 学习顺序：第 4 篇
> 官方文档：[Skills](https://code.claude.com/docs/en/skills)

---

## 什么是 Skill？

Skill 是 Claude Code 中的**可复用知识包**。它是一份 Markdown 文件，告诉 Claude 如何完成某个特定领域的任务。

**想象一下：** Skill 就像一本"菜谱"。当你想做一道菜时，你翻开对应的菜谱，照着步骤做。做完后合上菜谱，不影响你做下一道菜。

---

## 前置元数据字段（16 个）

每个 Skill 定义文件（`.claude/skills/<名称>/SKILL.md`）使用 YAML frontmatter 配置：

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `name` | string | 否 | 显示名称和 `/命令`。省略时使用目录名 |
| `description` | string | 推荐 | 技能功能描述——**这是触发条件，不是摘要** |
| `when_to_use` | string | 否 | 何时调用的额外上下文——触发短语和示例 |
| `argument-hint` | string | 否 | 自动补全时的提示（如 `[issue-number]`） |
| `arguments` | string/list | 否 | 位置参数名，用于 `$name` 替换 |
| `disable-model-invocation` | boolean | 否 | `true` 则阻止 Claude 自动调用 |
| `user-invocable` | boolean | 否 | `false` 则从 `/` 菜单隐藏（用于 agent 预加载） |
| `allowed-tools` | string | 否 | 免确认的工具 |
| `disallowed-tools` | string/list | 否 | 禁止的工具 |
| `model` | string | 否 | 运行时的模型 |
| `effort` | string | 否 | 推理力度覆盖 |
| `context` | string | 否 | `fork` 则在独立子代理中运行 |
| `agent` | string | 否 | `context: fork` 时的子代理类型 |
| `hooks` | object | 否 | 生命周期钩子 |
| `paths` | string/list | 否 | Glob 模式限制自动激活范围 |
| `shell` | string | 否 | `` !`command` `` 块的 shell |

---

## 官方内置 Skill（10 个）

| # | Skill | 用途 |
|---|-------|------|
| 1 | `code-review` | 审查代码变更中的正确性错误 |
| 2 | `batch` | 批量操作多个文件 |
| 3 | `debug` | 调试失败的命令或代码 |
| 4 | `loop` | 按间隔重复执行 |
| 5 | `claude-api` | 构建 Claude API / Anthropic SDK 应用 |
| 6 | `fewer-permission-prompts` | 减少权限提示 |
| 7 | `run` | 启动并驱动项目应用 |
| 8 | `verify` | 构建并运行应用以验证更改 |
| 9 | `run-skill-generator` | 教 `/run` 和 `/verify` 如何构建和启动项目 |
| 10 | `simplify` | 审查代码简化机会 |

---

## 最佳实践

### Skill 设计的核心理念

> 来自 Thariq（Anthropic 团队）：

1. **description 是触发条件，不是摘要**
   - 错误的写法：`"代码审查技能"`
   - 正确的写法：`"Review the current diff for correctness bugs — use when changes are ready for review"`

2. **Skill 是目录，不是文件**
   - 可以包含 `references/`、`scripts/`、`examples/` 子目录
   - Claude 会自动加载这些子目录内容

3. **建立 Gotchas 章节**
   - 记录 Claude 在这个领域常犯的错误
   - 随时间积累，成为最高信号密度的内容

4. **不说显而易见的事**
   - 重点写哪些让 Claude 偏离默认行为的信息
   - 不需要写 "You are an AI assistant..."

5. **给目标，不给步骤**
   - 错误的写法："第 1 步做 X，第 2 步做 Y"
   - 正确的写法："目标是确保所有 API 端点都有认证检查。你可以决定如何实现。"

6. **包含脚本和库**
   - Claude 应该组合你的工具，而不是自己重写样板代码

7. **使用 `!command` 动态注入**
   - 在 SKILL.md 中嵌入 `!ls src/`，Claude 运行 Skill 时会执行该命令并将结果注入提示

### Skill 的两大模式

**模式 1：普通 Skill（共享上下文）**
```yaml
---
name: my-skill
description: 对当前上下文注入领域知识
---
```

**模式 2：Fork Skill（隔离上下文）**
```yaml
---
name: my-heavy-skill
description: 在隔离环境中执行，不干扰主会话
context: fork
agent: general-purpose
---
```
适合：需要大量工具调用或文件操作的任务，主会话只看到最终结果。

---

### Skill vs Command vs Subagent

| | Skill | Command | Subagent |
|--|-------|---------|----------|
| **本质** | 知识/指令包 | 工作流入口 | 独立助手 |
| **运行方式** | 注入到当前上下文或 fork | 在当前上下文执行 | 独立上下文 |
| **最适合** | 告诉 Claude "怎么做" | 告诉 Claude "做什么" | 让另一个 Claude "替你做" |
| **复用方式** | 跨项目复制 | 跨项目复制 | 跨项目复制 |

---

## 一个典型的 Skill 示例

```yaml
---
name: api-patterns
description: 实现 REST API 端点时使用的模式
---
## API 设计约定

### 路由命名
- 资源名使用复数：`/api/users`，`/api/posts`
- 嵌套路由不超过 2 层

### 错误响应格式
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "用户友好的错误描述",
    "details": {}
  }
}
```

### Gotchas
- 使用 `request.body` 前始终做类型验证
- 始终设置 `Content-Type: application/json` 响应头
```

---

> 下一篇：[05-设置-settings.md](./05-设置-settings.md)
