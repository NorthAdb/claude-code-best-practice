# 设置（Settings）最佳实践

> 中文翻译整理自 `best-practice/claude-settings.md`
> 学习顺序：第 5 篇
> 官方文档：[Settings](https://code.claude.com/docs/en/settings)

---

## 什么是 Settings？

Settings 是 Claude Code 的**配置文件体系**。通过 `settings.json`，你可以控制 Claude 的行为、权限、模型、外观等几乎所有方面。

---

## 配置优先级

从高到低（高优先级覆盖低优先级）：

| 优先级 | 位置 | 范围 | 用途 |
|--------|------|------|------|
| 1 | **托管设置**（MDM/注册表） | 组织 | 安全策略，不可覆盖 |
| 2 | **命令行参数** | 会话 | 临时单次覆盖 |
| 3 | `.claude/settings.local.json` | 项目 | 个人配置（被 gitignore） |
| 4 | `.claude/settings.json` | 项目 | 团队共享配置 |
| 5 | `~/.claude/settings.json` | 用户 | 全局默认配置 |

**注意：**
- `deny` 规则拥有最高安全优先级，不能被低优先级的 allow/ask 规则覆盖
- 数组配置（如 `permissions.allow`）是**跨作用域合并并去重**的

---

## 核心配置

### 通用设置

| 键 | 类型 | 默认值 | 说明 |
|-----|------|--------|------|
| `model` | string | `"default"` | 默认模型（`sonnet`、`opus`、`haiku` 或完整 ID） |
| `agent` | string | - | 默认主会话 agent |
| `language` | string | `"english"` | Claude 首选响应语言 |
| `outputStyle` | string | `"default"` | 输出风格（如 `"Explanatory"`） |
| `plansDirectory` | string | `~/.claude/plans` | `/plan` 输出目录 |
| `autoMemoryEnabled` | boolean | `true` | 启用自动记忆 |

### 权限设置

```json
{
  "permissions": {
    "allow": ["Edit(*)", "Write(*)", "Bash(npm run *)"],
    "ask": ["Bash(rm *)", "Bash(git push *)"],
    "deny": ["Read(.env)", "Bash(curl *)"]
  }
}
```

**权限模式：**

| 模式 | 行为 |
|------|------|
| `default` | 标准权限检查，需要确认 |
| `acceptEdits` | 自动接受文件编辑和常见文件系统命令 |
| `dontAsk` | 自动拒绝未预批准的调用 |
| `bypassPermissions` | 跳过所有权限检查（危险） |
| `auto` | 自动安全检查和确认（Shift+Tab 切换） |
| `plan` | 只读探索模式 |

### 显示 & UX

- **statusLine** — 自定义状态栏
- **spinnerTipsEnabled** — 等待时显示提示
- **spinnerVerbs** — 自定义加载动词
- **spinnerTipsOverride** — 自定义加载提示
- **respectGitignore** — 文件选择器是否遵循 .gitignore

### MCP 服务器设置

| 键 | 类型 | 说明 |
|-----|------|------|
| `enableAllProjectMcpServers` | boolean | 自动批准所有 `.mcp.json` 服务器 |
| `enabledMcpjsonServers` | array | 白名单特定服务器 |
| `disabledMcpjsonServers` | array | 黑名单特定服务器 |

### 模型配置

**模型别名：**

| 别名 | 说明 |
|------|------|
| `"default"` | 账户推荐模型 |
| `"sonnet"` | 最新 Sonnet 模型 |
| `"opus"` | 最新 Opus 模型 |
| `"haiku"` | 快速 Haiku 模型 |
| `"opusplan"` | Opus 规划 + Sonnet 执行 |
| `"fable"` | Claude Fable 5 长程推理模型 |

**推理力度：**

| 级别 | 说明 |
|------|------|
| Max | 最大推理深度（Opus 4.6 专用） |
| XHigh | 超强推理 |
| High | 完整推理（默认） |
| Medium | 平衡模式 |
| Low | 最少推理，最快响应 |

---

## 一个完整配置示例

```json
{
  "model": "opus",
  "outputStyle": "Explanatory",
  "permissions": {
    "allow": [
      "Edit(*)",
      "Write(*)",
      "Bash(npm run *)",
      "Bash(git *)"
    ],
    "deny": [
      "Bash(rm *)",
      "Read(.env)"
    ]
  },
  "spinnerTipsEnabled": true,
  "spinnerVerbs": {
    "mode": "replace",
    "verbs": ["分析代码", "思考方案", "优化性能"]
  },
  "statusLine": {
    "type": "command",
    "command": "echo '我的 Claude'"
  },
  "attribution": {
    "commit": "Co-Authored-By: Claude <noreply@anthropic.com>"
  },
  "env": {
    "CLAUDE_AUTOCOMPACT_PCT_OVERRIDE": "80"
  }
}
```

---

> 下一篇：[06-MCP服务器-mcp.md](./06-MCP服务器-mcp.md)
