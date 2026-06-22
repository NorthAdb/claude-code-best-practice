# 命令（Commands）最佳实践

> 中文翻译整理自 `best-practice/claude-commands.md`
> 学习顺序：第 3 篇
> 官方文档：[Slash Commands](https://code.claude.com/docs/en/slash-commands)

---

## 什么是 Command？

Command 是 Claude Code 中的**斜杠命令**（如 `/code-review`），用于快速执行常用工作流。你可以把每天重复做的事定义为一个命令，一键执行。

---

## 前置元数据字段（16 个）

每个命令定义文件（`.claude/commands/<名称>.md`）使用 YAML frontmatter 配置：

| 字段 | 类型 | 必需 | 说明 |
|------|------|------|------|
| `name` | string | 否 | 显示名称和 `/命令` 标识。省略时使用目录名 |
| `description` | string | 推荐 | 命令功能描述。显示在自动补全中 |
| `when_to_use` | string | 否 | 何时调用的额外上下文——触发短语或示例请求 |
| `argument-hint` | string | 否 | 自动补全时的提示（如 `[issue-number]`） |
| `arguments` | string/list | 否 | 位置参数名，用于命令内容中的 `$name` 替换 |
| `disable-model-invocation` | boolean | 否 | `true` 则阻止 Claude 自动调用 |
| `user-invocable` | boolean | 否 | `false` 则从 `/` 菜单隐藏 |
| `paths` | string/list | 否 | Glob 模式，限制命令只在匹配文件路径时激活 |
| `allowed-tools` | string | 否 | 命令激活时免确认的工具 |
| `disallowed-tools` | string/list | 否 | 命令激活时禁止的工具 |
| `model` | string | 否 | 命令运行时的模型（`haiku`、`sonnet`、`opus`） |
| `effort` | string | 否 | 覆盖模型推理力度（`low`、`medium`、`high`、`xhigh`、`max`） |
| `context` | string | 否 | `fork` 则在独立子代理中运行 |
| `agent` | string | 否 | `context: fork` 时的子代理类型（默认 `general-purpose`） |
| `shell` | string | 否 | `` !`command` `` 块的 shell：`bash`（默认）或 `powershell` |
| `hooks` | object | 否 | 生命周期钩子 |

---

## 系统内置命令（85 个）

按功能分类：

### 认证类
`/login` `/logout` `/setup-bedrock` `/setup-vertex` `/upgrade`

### 配置类
`/color` `/config` `/focus` `/keybindings` `/permissions` `/privacy-settings` `/radio` `/sandbox` `/scroll-speed` `/statusline` `/stickers` `/terminal-setup` `/theme` `/tui` `/voice`

### 上下文类
`/context` `/cost` `/insights` `/stats` `/status` `/usage` `/usage-credits`

### 调试类
`/doctor` `/feedback` `/heapdump` `/help` `/powerup` `/release-notes` `/tasks` `/copy`

### 导出类
`/export`

### 扩展类
`/agents` `/chrome` `/hooks` `/ide` `/mcp` `/plugin` `/reload-plugins` `/reload-skills` `/skills`

### 记忆类
`/memory`

### 模型类
`/advisor` `/effort` `/fast` `/model` `/passes` `/plan` `/ultraplan`

### 项目类
`/add-dir` `/diff` `/init` `/review` `/security-review` `/team-onboarding` `/ultrareview`

### 远程类
`/autofix-pr` `/desktop` `/install-github-app` `/install-slack-app` `/mobile` `/remote-control` `/remote-env` `/schedule` `/teleport` `/web-setup`

### 会话类
`/background` `/branch` `/btw` `/cd` `/clear` `/compact` `/exit` `/fork` `/goal` `/recap` `/rename` `/resume` `/rewind` `/stop` `/workflows`

---

## 最佳实践

### 何时创建自定义命令

- **每天多次执行的工作流** — 如代码审查、部署、测试
- **多步骤操作** — 需要多个提示才能完成的任务
- **团队共享流程** — 让团队用统一的命令执行标准流程

### 命令 vs Subagent vs Skill

| | Command | Subagent | Skill |
|--|---------|----------|-------|
| **最佳用途** | 快捷入口 | 并行任务 | 知识注入 |
| **是否可被 Claude 自动调用** | 是 | 是（PROACTIVELY） | 是 |
| **上下文隔离** | 可选（fork） | 是 | 可选（fork） |
| **适合** | 工作流触发 | 后台处理 | 领域知识 |

### Tips

- Boris Cherny（Claude Code 创建者）：**用 Command 代替 Subagent 来做你的工作流**
- 每天使用超过 1 次的操作，就应该做成 Command 或 Skill
- 一个 Command 定义文件通常不超过 20 行

---

## 一个自定义命令示例

```yaml
---
name: deploy
description: 部署当前分支到生产环境
argument-hint: [env]
arguments: env
allowed-tools: Bash(git *), Bash(npm run *)
---
执行部署流程：
1. 运行测试：`npm test`
2. 构建项目：`npm run build`
3. 部署到 $env 环境
4. 验证部署状态
```

---

> 下一篇：[04-技能-skills.md](./04-技能-skills.md)
