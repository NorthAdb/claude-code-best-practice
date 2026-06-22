# MCP 服务器最佳实践

> 中文翻译整理自 `best-practice/claude-mcp.md`
> 学习顺序：第 6 篇
> 官方文档：[MCP Servers](https://code.claude.com/docs/en/mcp)

---

## 什么是 MCP？

MCP（Model Context Protocol）是 Claude Code 的**扩展协议**，让 Claude 可以连接外部工具、数据库和 API。

**通俗理解：** MCP 就像 Claude Code 的"USB 接口"——需要什么功能就插什么外设。

---

## 日常推荐的 MCP 服务器

> 社区共识：不要贪多，15 个不如 4 个用得精。

| MCP 服务器 | 功能 | 推荐理由 |
|------------|------|----------|
| **Context7** | 获取最新库文档 | 防止 Claude 用过时的训练数据生成虚假 API |
| **Playwright** | 浏览器自动化 | 截图、导航、表单测试，前端开发必备 |
| **Claude in Chrome** | 连接真实 Chrome | 查看控制台日志、网络请求、DOM |
| **DeepWiki** | GitHub 仓库文档 | 获取任何仓库的架构文档 |
| **Excalidraw** | 架构图生成 | 画流程图、系统设计图 |

**工作流：** Research（Context7/DeepWiki）→ Debug（Playwright/Chrome）→ Document（Excalidraw）

---

## 配置方式

### 服务器类型

| 类型 | 传输方式 | 示例 |
|------|----------|------|
| **stdio** | 启动本地进程 | `npx`、`python`、二进制文件 |
| **http** | 连接远程 URL | HTTP/SSE 端点 |

### `.mcp.json` 配置示例

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"]
    },
    "deepwiki": {
      "command": "npx",
      "args": ["-y", "deepwiki-mcp"]
    },
    "remote-api": {
      "type": "http",
      "url": "https://mcp.example.com/mcp?token=${MCP_API_TOKEN}"
    }
  }
}
```

### settings.json 中的 MCP 控制

```json
{
  "enableAllProjectMcpServers": true,
  "enabledMcpjsonServers": ["memory", "github", "filesystem"],
  "disabledMcpjsonServers": ["experimental-server"]
}
```

### MCP 工具权限规则

```json
{
  "permissions": {
    "allow": [
      "mcp__context7__*",
      "mcp__playwright__browser_snapshot"
    ],
    "deny": [
      "mcp__dangerous-server__*"
    ]
  }
}
```

> **注意：** 在 `allow` 规则中，`mcp__*` 这种裸通配符不被支持。必须写为 `mcp__服务器名__*` 的形式。

---

## MCP 作用域

MCP 服务器可以在三个层级定义：

| 层级 | 位置 | 用途 |
|------|------|------|
| **项目级** | `.mcp.json`（项目根目录） | 团队共享，提交到 git |
| **用户级** | `~/.claude.json`（`mcpServers` 键） | 个人跨项目配置 |
| **Subagent 级** | Agent frontmatter（`mcpServers` 字段） | 特定 agent 专用 |

**优先级：** Subagent > Project > User

---

## 最佳实践

1. **精选少量** — 5 个以内最佳，太多会降低性能和准确性
2. **用环境变量保护密钥** — 使用 `${VAR}` 语法，不要硬编码 API key
3. **上下文感知** — Context7 + DeepWiki 一起用覆盖一切文档需求
4. **浏览器自动化二选一** — Playwright 或 Claude in Chrome，不要同时用
5. **定期清理** — 不再需要的 MCP 服务器及时移除

---

## 更多阅读

- [Browser Automation MCP 对比报告](../../reports/claude-in-chrome-v-chrome-devtools-mcp.md)
- [MCP 规范](https://modelcontextprotocol.io/)
- [Reddit 讨论：5 个真正让我快 10 倍的 MCP](https://reddit.com/r/mcp/comments/1qarjqm/)

---

> 下一篇：[07-记忆系统-memory.md](./07-记忆系统-memory.md)
