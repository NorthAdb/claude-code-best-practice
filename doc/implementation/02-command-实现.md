# Command 实现

> 中文翻译整理自 `implementation/claude-commands-implementation.md`

<table width="100%">
<tr>
<td><a href="../../">← 返回 Claude Code Best Practice</a></td>
<td align="right"><img src="../../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#weather-orchestrator"><img src="../../!/tags/implemented-hd.svg" alt="Implemented"></a>

天气编排命令在此仓库中实现，作为 **Command → Agent → Skill** 架构模式的入口，演示了命令如何编排多步骤工作流。

---

## Weather Orchestrator（天气编排命令）

**文件**: [`.claude/commands/weather-orchestrator.md`](../../.claude/commands/weather-orchestrator.md)

```yaml
---
description: Fetch weather data for Dubai and create an SVG weather card
model: haiku
---

# Weather Orchestrator Command

Fetch the current temperature for Dubai, UAE and create a visual SVG weather card.

## Workflow

### Step 1: Ask User Preference
使用 AskUserQuestion 工具询问用户想要摄氏度还是华氏度。

### Step 2: Fetch Weather Data
使用 Agent 工具调用 weather agent：
- subagent_type: weather-agent
- prompt: 获取迪拜当前的温度，单位：[用户选择的单位]

### Step 3: Create SVG Weather Card
使用 Skill 工具调用 weather-svg-creator skill：
- skill: weather-svg-creator
```

该命令编排了整个工作流：它询问用户偏好的温度单位，通过 Agent 工具调用 `weather-agent` 获取数据，再通过 Skill 工具调用 `weather-svg-creator` skill 创建可视化卡片。

---

## ![如何使用](../../!/tags/how-to-use.svg)

```bash
$ claude
> /weather-orchestrator
```

---

## ![如何实现](../../!/tags/how-to-implement.svg)

让 Claude 帮你创建——它会自动在 `.claude/commands/<name>.md` 生成带 YAML frontmatter 的命令文件。

---

<a href="https://github.com/shanraisshan/claude-code-best-practice#orchestration-workflow"><img src="../../!/tags/orchestration-workflow-hd.svg" alt="编排工作流"></a>

天气编排命令在 Command → Agent → Skill 编排模式中扮演 **Command** 角色。它作为入口——处理用户交互（温度单位选择），将数据获取委托给 `weather-agent`，再调用 `weather-svg-creator` skill 生成可视化输出。

<p align="center">
  <img src="../../orchestration-workflow/orchestration-workflow.svg" alt="Command → Agent → Skill 三层架构" width="100%">
</p>

| 组件 | 角色 | 本仓库实现 |
|------|------|------------|
| **Command** | 入口、用户交互 | [`/weather-orchestrator`](../../.claude/commands/weather-orchestrator.md) |
| **Agent** | 用预加载 skill 获取数据 | [`weather-agent`](../../.claude/agents/weather-agent.md) + [`weather-fetcher`](../../.claude/skills/weather-fetcher/SKILL.md) |
| **Skill** | 独立创建最终输出 | [`weather-svg-creator`](../../.claude/skills/weather-svg-creator/SKILL.md) |
