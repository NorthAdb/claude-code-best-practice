# CLI 启动参数最佳实践

> 中文翻译整理自 `best-practice/claude-cli-startup-flags.md`
> 学习顺序：第 8 篇
> 官方文档：[CLI Reference](https://code.claude.com/docs/en/cli-reference)

---

## 什么是 CLI 启动参数？

从终端启动 Claude Code 时，你可以通过启动参数控制其行为——用什么模型、什么权限模式、是否恢复之前的会话等。

---

## 常用启动方式

```bash
claude                                    # 交互模式
claude "修复这个 bug"                     # 交互模式 + 初始提示
claude -p "运行所有测试"                   # 一次性任务（非交互）
claude --model opus                       # 指定模型
claude -w                                 # 在工作树中启动
```

---

## 参数速查表

### 会话管理

| 参数 | 简写 | 说明 |
|------|------|------|
| `--continue` | `-c` | 继续当前目录最近的会话 |
| `--resume` | `-r` | 恢复指定会话 |
| `--from-pr <编号>` | | 恢复链接到 PR 的会话 |
| `--session-id <UUID>` | | 使用指定会话 ID |
| `--no-session-persistence` | | 禁止会话持久化 |
| `--remote` | | 在 claude.ai 创建新网页会话 |

### 模型 & 配置

| 参数 | 说明 |
|------|------|
| `--model <名称>` | 设置模型（`sonnet`、`opus`、`haiku` 或完整 ID） |
| `--fallback-model <名称>` | 主模型不可用时的备用模型 |
| `--betas <列表>` | API 请求的 Beta 头 |

### 权限 & 安全

| 参数 | 说明 |
|------|------|
| `--dangerously-skip-permissions` | 跳过所有权限检查（危险！） |
| `--permission-mode <模式>` | 设置权限模式：`default`、`plan`、`acceptEdits`、`bypassPermissions` |
| `--allowedTools <工具>` | 免确认执行的工具 |
| `--disallowedTools <工具>` | 从模型上下文中移除的工具 |

### 输出 & 格式

| 参数 | 简写 | 说明 |
|------|------|------|
| `--print` | `-p` | 非交互模式（一次性任务） |
| `--output-format <格式>` | | `text`、`json`、`stream-json` |
| `--verbose` | | 详细日志输出 |

### Agent & Subagent

| 参数 | 说明 |
|------|------|
| `--agent <名称>` | 为当前会话指定 agent |
| `--teammate-mode <模式>` | Agent 团队显示模式：`auto`、`in-process`、`tmux` |

### 子命令

| 命令 | 说明 |
|------|------|
| `claude agents` | 列出已配置的 agent |
| `claude auth` | 管理认证 |
| `claude doctor` | 运行诊断 |
| `claude install` | 安装或切换构建版本 |
| `claude mcp` | 配置 MCP 服务器 |
| `claude plugin` | 管理插件 |
| `claude update` | 更新到最新版本 |

---

## 实操场景

### 场景 1：日常开发
```bash
claude --model sonnet --permission-mode acceptEdits
```

### 场景 2：代码审查
```bash
claude -p "审查当前分支的所有变更" --model opus
```

### 场景 3：批量处理
```bash
claude -p "修复 src/ 目录下所有 TypeScript 错误" --model sonnet
```

### 场景 4：后台任务
```bash
claude --bg "重构 auth 模块" --model haiku
```

### 场景 5：隔离工作
```bash
claude -w
```

---

## 常用环境变量

这些需要在启动 Claude 之前设置：

| 变量 | 说明 |
|------|------|
| `CLAUDE_CODE_SIMPLE=1` | 简易模式（只有 Bash + 编辑工具） |
| `CLAUDE_CODE_EFFORT_LEVEL=high` | 控制推理力度 |
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` | 启用实验性 Agent 团队 |
| `DISABLE_AUTOUPDATER=1` | 禁用自动更新 |

---

> 下一篇：[09-能力强化-power-ups.md](./09-能力强化-power-ups.md)
