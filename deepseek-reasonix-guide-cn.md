# DeepSeek-Reasonix · 中文使用文档

> DeepSeek 原生的终端 AI 编程代理，围绕前缀缓存稳定性设计——长会话下 token 成本始终低位运行，可以一直开着。
> GitHub: [esengine/DeepSeek-Reasonix](https://github.com/esengine/DeepSeek-Reasonix) · ⭐ 9,974

---

## 目录

- [这是什么](#这是什么)
- [核心特性](#核心特性)
- [与 Claude Code / Cursor / Aider 的对比](#与-claude-code--cursor--aider-的对比)
- [安装](#安装)
- [快速开始](#快速开始)
- [命令行模式详解](#命令行模式详解)
- [配置](#配置)
- [Skills 自定义](#skills-自定义)
- [Memory 记忆系统](#memory-记忆系统)
- [MCP 服务器扩展](#mcp-服务器扩展)
- [Hooks 钩子](#hooks-钩子)
- [会话持久化与重放](#会话持久化与重放)
- [成本控制](#成本控制)
- [常见问题](#常见问题)

---

## 这是什么

Reasonix（项目名 DeepSeek-Reasonix，npm 包名 `reasonix`）是一个 DeepSeek 原生的 AI 编程 Agent，运行在终端里。

**解决的问题：**
- 通用 Agent 框架（如 Claude Code、Cursor）默认用贵模型，长会话成本高达 $150-250/月
- 大多数轮次不需要最强推理，普通任务按最高配付费浪费严重
- 上下文越长、缓存命中率越低，通用框架的前缀缓存命中率实际 <20%

**核心理念：** 可以一直开着的编程工具。长会话保持低位运行。

---

## 核心特性

| 特性 | 说明 |
|------|------|
| **前缀缓存优先循环** | 三段式上下文（不可变前缀 + 只追加日志 + 临时草稿），确保 DeepSeek 字节级前缀缓存稳定命中 |
| **工具调用修复** | 4 层修复：参数展平、跨 `reasoning_content` 打捞、截断 JSON 补全、重复调用抑制 |
| **成本控制体系** | v4-flash（1×）→ v4-pro（12×）自动升级；turn-end 自动压缩；模型自我报告升级信号 |
| **持久化会话** | 工作区按目录隔离，重启不丢上下文 |
| **MCP 扩展** | 支持 stdio / SSE / Streamable HTTP多种传输方式 |
| **内嵌 Web 仪表盘** | TUI 内置带 Dashboard，可在浏览器查看会话历史 |
| **Skills / Hooks / Memory** | 可扩展的记忆、技能、生命周期钩子系统 |
| **QQ 通道** | 可将现有会话延伸到 QQ，作为远程通道（需配置 QQ 开放平台凭据） |

---

## 与 Claude Code / Cursor / Aider 的对比

| | Reasonix | Claude Code | Cursor | Aider |
|--|--|--|--|--|
| 后端 | **DeepSeek** | Anthropic | OpenAI / Anthropic | 任意（OpenRouter） |
| 协议 | **MIT 开源** | 闭源 | 闭源 | Apache 2 |
| 单任务成本 | **低** | 高 | 订阅 + 用量 | 不一 |
| DeepSeek 前缀缓存 | **专门工程化** | 不适用 | 不适用 | 偶发命中 |
| 内嵌 Web 仪表盘 | ✅ 支持 | — | — | — |
| 持久化工作区会话 | ✅ 支持 | 部分 | 不适用 | — |
| 开放社区共建 | ✅ | — | — | ✅ |

---

## 安装

### 环境要求

- **Node.js ≥ 22**
- macOS / Linux / Windows（PowerShell / Git Bash / Windows Terminal）

### 安装命令

```bash
cd my-project
npx reasonix code   # 首次运行会要求粘贴 DeepSeek API Key，之后会记住
```

> 推荐使用 `npx` 而非全局安装——每次都拿最新版。如果天天用，可以全局安装：
> ```bash
> npm install -g reasonix
> reasonix update   # 升级
> ```

### 获取 DeepSeek API Key

👉 https://platform.deepseek.com/api_keys

---

## 快速开始

### 1. 进入代码模式（主要入口）

```bash
npx reasonix code
```

启动后：
- 首次运行会要求输入 DeepSeek API Key
- Key 保存在 `~/.reasonix/config.json`，后续无需重复输入
- TUI 界面显示工作目录、模型、缓存命中率、当前成本

### 2. 常用 Slash 命令

| 命令 | 说明 |
|------|------|
| `/apply` | 确认执行 SEARCH/REPLACE 编辑 |
| `/model flash` | 切换到 v4-flash（便宜，快速） |
| `/model pro` | 切换到 v4-pro（最强推理） |
| `/skill new <name>` | 创建新 Skill |
| `/skill list` | 列出当前可用的 Skills |
| `/mcp add <name> <cmd>` | 添加 MCP 服务器 |
| `/search <query>` | Web 搜索 |
| `/effort <1-10>` | 调整任务投入程度 |
| `/plan` | 进入计划模式 |
| `/todo` | 任务清单模式 |
| `/recall_memory <query>` | 检索存储的记忆 |
| `/remember <content>` | 存储新记忆 |
| `/qq connect` | 连接 QQ 通道 |
| `Ctrl+D` | 结束会话 |

### 3. 工作目录

```bash
npx reasonix code --dir /path/to/project
```

---

## 命令行模式详解

| 命令 | 何时用 |
|------|--------|
| `reasonix code [dir]` | **编码 Agent——先用这个。** 带文件系统工具、SEARCH/REPLACE 审阅、shell 工具 |
| `reasonix chat` | 纯聊天——不带文件系统/shell 工具，适合思路助手 |
| `reasonix run "task"` | 一次性任务，结果流到 stdout，适合 shell 管道 |
| `reasonix doctor` | 体检：Node 版本、API Key、MCP 接线状态 |
| `reasonix update` | 升级 Reasonix 本身 |
| `reasonix replay <session>` | 重放历史会话 |
| `reasonix diff <session>` | 对比两次会话差异 |
| `reasonix events <session>` | 查看会话事件日志 |
| `reasonix stats` | 查看用量统计 |
| `reasonix index` | 重建语义索引 |
| `reasonix prune-sessions` | 清理旧会话 |
| `reasonix mcp` | MCP 服务器管理 |

完整命令列表：`reasonix --help`

---

## 配置

配置文件位置：`~/.reasonix/config.json`（全局）+ `<project>/.reasonix/`（项目级覆盖）

### 完整配置参考

👉 **在线配置指南**：https://esengine.github.io/DeepSeek-Reasonix/configuration.html?lang=zh

### 核心配置项

```json
{
  "model": {
    "preset": "auto",       // "flash" | "auto" | "pro"
    "explicit": null        // 直接指定模型，如 "deepseek-chat"
  },
  "search": {
    "engine": "mojeek"      // "mojeek" | "searxng" | "metaso"
  },
  "mcp": {
    "servers": []
  },
  "skills": {
    "inline": [],          // 内联 Skill
    "subagent": []         // subagent Skill
  },
  "permissions": {
    "shell": ["git *", "npm *", "pnpm *"]   // shell 白名单（精确前缀匹配）
  }
}
```

### Permissions 权限控制

```json
"permissions": {
  "shell": ["git *", "npm *", "pnpm *"]
}
```

只有白名单内的 shell 命令可以执行，防止误操作。

### Web 搜索配置

默认 Mojeek，可切换到自托管 SearXNG 或 Metaso：

```bash
/search-engine searxng https://your-searxng-instance.com
```

---

## Skills 自定义

Skill 是一个 markdown 文件，包含 `description` frontmatter + 正文内容，AI 在对话中调用。

### 创建 Skill

```bash
# 项目级 Skill
/skill new my-skill

# 全局 Skill（跨项目共用）
/skill new my-skill --global
```

文件位置：
- 项目级：`~/.reasonix/skills/my-skill.md`
- 全局：`~/.reasonix/skills/<name>.md`

### Skill 文件格式

```markdown
---
name: my-skill
description: 这个 Skill 做什么
runAs: subagent    // 可选：subagent 模式隔离运行
---

# My Skill

这里是对 AI 的指令内容...
```

### 内置 Skills

- `explore` — 代码探索
- `research` — 资料研究

---

## Memory 记忆系统

四类记忆，钉进不可变前缀，长久保留在上下文中：

| 类型 | 作用域 | 说明 |
|------|--------|------|
| `user` | 用户级 | 跨项目共享的用户偏好、知识 |
| `feedback` | 用户级 | 用户对 AI 输出的反馈，自动沉淀 |
| `project` | 项目级 | 仅本项目生效 |
| `reference` | 项目级 | 参考文档、技术债记录 |

### 常用命令

```bash
/remember <content>     # 存储记忆
/recall_memory <query>  # 检索记忆
```

---

## MCP 服务器扩展

MCP（Model Context Protocol）支持 stdio / SSE / Streamable HTTP 三种传输方式。

### 添加 MCP 服务器

```bash
/mcp add <name> <command>
# 例如：
/mcp add filesystemsandbox npx --yes @modelcontextprotocol/server-filesystem /tmp
```

### 配置文件中添加

```json
{
  "mcp": {
    "servers": [
      {
        "name": "filesystem",
        "transport": "stdio",
        "command": ["npx", "--yes", "@modelcontextprotocol/server-filesystem", "/tmp"]
      }
    ]
  }
}
```

---

## Hooks 钩子

Hooks 在生命周期事件触发时执行 shell 命令。

### 支持的事件

| 事件 | 触发时机 |
|------|----------|
| `PreToolUse` | 工具执行前（可拦截） |
| `PostToolUse` | 工具执行后 |
| `UserPromptSubmit` | 用户提交指令后 |
| `Stop` | 会话停止时 |

### 配置示例

```json
{
  "hooks": [
    {
      "name": "log-tool-use",
      "trigger": "PostToolUse",
      "command": "echo '{tool} took {duration}ms' >> /tmp/tool-log.txt"
    }
  ]
}
```

---

## 会话持久化与重放

### 会话持久化

工作区按目录隔离，会话文件保存在 `~/.reasonix/sessions/<dir-hash>/`。

```bash
# 查看所有会话
reasonix sessions list

# 删除某个会话
reasonix prune-sessions
```

### 重放会话

```bash
# 重放
reasonix replay <session-id>

# 对比两次差异
reasonix diff <session-a> <session-b>

# 查看事件日志
reasonix events <session-id>
```

---

## 成本控制

### 模型层级

| 预设 | 模型 | 成本倍率 | 说明 |
|------|------|----------|------|
| `flash` | v4-flash | 1× | 默认，适合大多数任务 |
| `auto` | v4-flash → v4-pro | 1-3× | 困难任务自动升级 |
| `pro` | v4-pro | ~12× | 最强推理，复杂任务 |

### 模型自动升级

当模型判断任务超出当前层级，会在回复第一行输出：

```
<<<NEEDS_PRO>>>
```

系统自动中止当前 flash 调用，重试 pro 级别。

### 切换模型

```bash
/model flash   # 切换到便宜模式
/model pro      # 切换到最强模式
```

### 成本可视化

TUI StatsPanel 显示每轮和会话累计成本：
- 绿色 <$0.05
- 黄色 $0.05-0.20
- 红色 ≥$0.20

---

## 常见问题

**Q: 为什么只支持 DeepSeek？**

A: Reasonix 每一层都为 DeepSeek 字节稳定的前缀缓存机制调过。选择单一后端是为了让缓存命中率最高。绑死一个后端是 feature，不是限制。

**Q: 支持多供应商吗？**

A: 故意不支持。DeepSeek 是当前性价比最高的选择，分散后端会破坏缓存稳定性。

**Q: 需要付费吗？**

A: 需要 DeepSeek API Key，按用量付费。DeepSeek 价格远低于 Claude/OpenAI，flash 模型成本极低。

**Q: 和 Cursor / VS Code 插件有什么区别？**

A: Reasonix 是终端优先，TUI + Web Dashboard，不做 IDE 集成。diff 在 `git diff`，文件树在 `ls`。

**Q: 完全离线 / 零成本可以用吗？**

A: 不可以，需要付费的 DeepSeek API Key。完全离线方案看 Aider + Ollama 或 Continue.dev。

**Q: Node.js 版本要求？**

A: Node ≥ 22。

**Q: Windows 支持吗？**

A: 支持 PowerShell / Git Bash / Windows Terminal，WSL 未测试。

---

## 更新日志

最新版本：v0.52.0

详细更新记录见：[CHANGELOG.md](https://github.com/esengine/DeepSeek-Reasonix/blob/main/CHANGELOG.md)

---

## 反馈与社区

- GitHub Issues：[esengine/DeepSeek-Reasonix](https://github.com/esengine/DeepSeek-Reasonix/issues)
- Discord（双语）：https://discord.gg/XF78rEME2D
- 官方网站：https://esengine.github.io/DeepSeek-Reasonix/
- 微信赞助：见原项目 README

---

*文档由 Hermès Agent 生成，基于官方 README 和项目结构整理。*