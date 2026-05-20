# OpenHuman 安装与使用完全指南

> **项目**：[tinyhumansai/openhuman](https://github.com/tinyhumansai/openhuman)
> **一句话**：你的个人 AI 超级智能。私有、简单、极其强大。
> **许可**：GNU General Public License
> **当前状态**：Early Beta（活跃开发中，可能有粗糙边缘）

---

## 目录

1. [OpenHuman 是什么](#1-openhuman-是什么)
2. [系统要求](#2-系统要求)
3. [快速安装（推荐）](#3-快速安装推荐)
4. [从源码构建](#4-从源码构建)
5. [首次启动与配置](#5-首次启动与配置)
6. [核心功能详解](#6-核心功能详解)
7. [配置与调优](#7-配置与调优)
8. [常见问题](#8-常见问题)
9. [卸载](#9-卸载)

---

## 1. OpenHuman 是什么

OpenHuman 是一个开源的个人 AI 助手，设计目标是融入你的日常生活。它不是聊天机器人，而是一个**常驻桌面的智能代理**：

- **桌面 mascot**：有一个会说话、会对周围环境做出反应的角色，能加入你的 Google Meet 会议
- **118+ 第三方集成**：一键 OAuth 接入 Gmail、Notion、GitHub、Slack、Stripe、Calendar、Drive、Linear、Jira 等
- **记忆树 + Obsidian Wiki**：本地优先的知识库，所有数据规范化为 Markdown 文件，存储在 SQLite 中，同时在 Obsidian 兼容的 vault 中可浏览编辑（灵感来自 Karpathy）
- **TokenJuice 智能压缩**：所有工具调用、网页抓取、邮件正文在进入 LLM 前经过压缩层，HTML→Markdown、长 URL 缩短，最高节省 80% 成本和延迟
- **自动同步**：每 20 分钟轮询所有已连接账户，将新数据拉入记忆树
- **多模型路由**：自动将任务分发给合适的 LLM（推理、快速、视觉），一个订阅搞定
- **本地 AI 可选**：支持通过 Ollama 运行本地模型

**与竞品对比**：

| 特性 | Claude Cowork | OpenClaw | Hermes Agent | **OpenHuman** |
|------|:---:|:---:|:---:|:---:|
| 开源 | ❌ 闭源 | ✅ MIT | ✅ MIT | ✅ GNU |
| 上手难度 | 中等 | 终端优先 | 终端优先 | **桌面 UI，几分钟** |
| 成本 | 订阅+附加费 | 自带模型 | 自带模型 | **一个订阅+TokenJuice** |
| 记忆 | 会话级 | 插件依赖 | 自学习 | **记忆树+Obsidian vault** |
| 集成数量 | 少量 | 自带 | 自带 | **118+ OAuth 一键** |
| 自动拉取 | ❌ | ❌ | ❌ | **✅ 20分钟轮询** |
| 模型路由 | 单模型 | 手动 | 手动 | **✅ 内置** |
| 原生工具 | 仅代码 | 仅代码 | 仅代码 | **代码+搜索+抓取+语音** |
| API 分散 | 需额外密钥 | 多厂商 | 多厂商 | **一个账户** |

---

## 2. 系统要求

### 桌面端

| 平台 | 架构支持 | 最低要求 |
|------|---------|---------|
| macOS | x86_64（Intel）、aarch64（Apple Silicon） | macOS 12+ |
| Linux | x86_64 | glibc 2.28+ |
| Windows | x86_64 | Windows 10+ |

### 源码构建额外要求

| 工具 | 版本 | 用途 |
|------|------|------|
| Git | 2.x+ | 克隆仓库和子模块 |
| Node.js | **≥ 24** | 前端和构建工具链 |
| pnpm | **10.10.0** | 包管理器 |
| Rust | **1.93.0**（含 rustfmt + clippy） | Tauri 桌面壳和原生模块 |
| CMake | 3.x+ | C/C++ 构建 |
| Ninja | 1.x+ | 快速构建系统 |
| ripgrep | 14.x+ | 代码搜索 |

---

## 3. 快速安装（推荐）

### macOS

```bash
# 方式一：一键安装脚本（推荐）
curl -fsSL https://raw.githubusercontent.com/tinyhumansai/openhuman/main/scripts/install.sh | bash

# 方式二：下载 DMG
# 访问 https://tinyhumans.ai/openhuman 下载最新 DMG，双击安装
```

安装后应用位于：`~/Applications/OpenHuman.app`

启动：
```bash
open ~/Applications/OpenHuman.app
```

### Linux (x86_64)

```bash
curl -fsSL https://raw.githubusercontent.com/tinyhumansai/openhuman/main/scripts/install.sh | bash
```

安装后二进制位于：`~/.local/bin/openhuman`

启动：
```bash
~/.local/bin/openhuman
```

> 脚本会自动将 `~/.local/bin` 加入 PATH（写入 `.bashrc`/`.zshrc`/`.profile`），新终端窗口可直接用 `openhuman`。

首次启动还会自动创建桌面入口文件：`~/.local/share/applications/openhuman.desktop`

### Windows

```powershell
# PowerShell 中运行
irm https://raw.githubusercontent.com/tinyhumansai/openhuman/main/scripts/install.ps1 | iex
```

### 安装脚本高级选项

```bash
# 查看帮助
curl -fsSL https://raw.githubusercontent.com/tinyhumansai/openhuman/main/scripts/install.sh | bash -s -- --help

# 干运行（不实际安装，只打印将要执行的操作）
curl -fsSL ... | bash -s -- --dry-run

# 详细输出
curl -fsSL ... | bash -s -- --verbose
```

### 验证安装

```bash
# macOS
ls -la ~/Applications/OpenHuman.app

# Linux
ls -la ~/.local/bin/openhuman
~/.local/bin/openhuman --version
```

---

## 4. 从源码构建

适合想贡献代码或使用最新开发版本的开发者。

### 4.1 克隆仓库

```bash
git clone https://github.com/tinyhumansai/openhuman.git
cd openhuman

# ⚠️ 重要：必须初始化子模块（包含 vendored Tauri/CEF 源码）
git submodule update --init --recursive
```

### 4.2 安装依赖

```bash
# 确保已安装 Node.js 24+、pnpm 10.10.0、Rust 1.93.0
node --version   # 应 ≥ v24
pnpm --version   # 应 = 10.10.0
rustc --version  # 应 ≥ 1.93.0

# 安装项目依赖
pnpm install
```

### 4.3 开发模式运行

```bash
# Web-only UI 开发（浏览器中调试前端）
pnpm dev

# 完整桌面应用开发
pnpm --filter openhuman-app dev:app
```

### 4.4 构建发布包

```bash
# 类型检查
pnpm typecheck

# 格式化检查
pnpm format:check

# Rust 编译检查
cargo check -p openhuman --lib

# 完整构建
pnpm build
```

### 4.5 提交前检查

```bash
# 运行所有检查
pnpm typecheck && pnpm format:check && cargo check -p openhuman --lib
```

> 详细贡献流程参考仓库中的 `CONTRIBUTING.md`（标准 PR 流程）和 `CONTRIBUTING-BEGINNERS.md`（AI 代理辅助开发者的复制粘贴 prompt）。

---

## 5. 首次启动与配置

### 5.1 创建账户

1. 启动 OpenHuman 桌面应用
2. 使用邮箱注册或通过 Google/GitHub OAuth 登录
3. 选择订阅计划（提供免费试用）

### 5.2 接入你的工具栈

登录后，在「Integrations」面板中一键 OAuth 接入：

- **邮件与日历**：Gmail、Google Calendar
- **文档与笔记**：Notion、Google Drive、Obsidian
- **代码与开发**：GitHub、GitLab、Linear、Jira
- **通讯**：Slack、Discord
- **支付与业务**：Stripe

每接入一个服务，OpenHuman 会读取历史数据并建立记忆索引。之后每 20 分钟自动拉取增量更新。

### 5.3 配置模型提供商

OpenHuman 内置模型路由，一个订阅覆盖多个模型。如果要使用自己的 API Key：

- 编辑配置文件（位于 `~/.openhuman/config.toml` 或应用内 Settings → Providers）
- 支持的提供商：OpenAI、Anthropic、Google、Groq、本地 Ollama

示例配置：

```toml
[providers.openai]
api_key = "sk-..."

[providers.anthropic]
api_key = "sk-ant-..."

[providers.ollama]
base_url = "http://localhost:11434"
```

### 5.4 连接 agentmemory（可选）

如果你已经在用 [agentmemory](https://github.com/rohitg00/agentmemory) 为 Claude Code/Cursor/Codex 等提供持久记忆：

```toml
[memory]
backend = "agentmemory"
# agentmemory 服务地址
url = "http://localhost:8765"
```

这样 OpenHuman 和你的编程代理共享同一份持久记忆。

---

## 6. 核心功能详解

### 6.1 桌面 Mascot（数字伙伴）

- 常驻桌面的动画角色
- 语音交互：STT（语音转文字）输入 → LLM 处理 → ElevenLabs TTS 输出，带口型同步
- 环境感知：可读取屏幕内容（需授权），对当前工作做出反应
- Google Meet 参与：作为真实参会者加入会议，可录音、总结

### 6.2 记忆树（Memory Tree）

与传统「聊天历史」不同，OpenHuman 的记忆是结构化的：

```
Memory Tree (SQLite)
├── 邮件（发件人/主题/摘要/时间）
├── 日历（事件/参与者/地点）
├── 代码仓库（提交/PR/Issue）
├── 文档（Notion/Drive 内容）
├── 消息（Slack/Discord 摘要）
└── 自动摘要（跨来源聚合主题）
```

所有条目被规范化为 ≤3000 token 的 Markdown 块，评分后归入层级摘要树。

### 6.3 Obsidian Vault 集成

记忆树中的每一块 Markdown 都会同步到本地的 Obsidian 兼容 Vault：

```
~/.openhuman/vault/
├── memory/
│   ├── emails/
│   ├── calendar/
│   ├── code/
│   └── messages/
├── agents/
└── daily-notes/
```

你可以用 Obsidian 打开这个 Vault，浏览、搜索、编辑所有记忆数据。灵感来自 Karpathy 的 obsidian-wiki 工作流。

### 6.4 TokenJuice 压缩引擎

每次工具调用、网页抓取、邮件读取的数据都会经过压缩管道：

1. HTML → Markdown 转换
2. 长 URL 缩短
3. 冗余工具输出去重
4. CJK/emoji 等多字节文本保留（不会错误截断）
5. 可配置的规则叠加

效果：**相同信息量，token 消耗降低最高 80%**，直接减少延迟和费用。

### 6.5 内置工具集

| 工具类型 | 功能 |
|---------|------|
| 文件系统 | 读/写/搜索文件 |
| Git | 提交/分支/日志/对比 |
| 代码检查 | Lint、格式化、测试运行 |
| Web 搜索 | 内置搜索引擎 |
| 网页抓取 | 抓取并解析网页内容 |
| 语音 | STT 输入 + ElevenLabs TTS 输出 |

无需安装插件，开箱即用。

### 6.6 自动同步

- 每 20 分钟轮询所有已连接的集成
- 增量拉取新数据进入记忆树
- 自动生成跨来源的主题摘要
- **早上打开 OpenHuman，它已经知道今天有什么会议、邮件、待办**

### 6.7 多模型智能路由

OpenHuman 根据任务类型自动选择最合适的模型：

| 任务类型 | 路由目标 | 特点 |
|---------|---------|------|
| 推理/分析 | 推理模型（如 o3、Claude Opus） | 深度思考 |
| 日常对话 | 快速模型（如 GPT-4o、Claude Sonnet） | 低延迟 |
| 图像理解 | 视觉模型 | 多模态 |

一个订阅覆盖所有模型，无需分别付费。

### 6.8 消息通道

OpenHuman 支持通过你已有的通讯工具接收和发送消息：
- 可从 Slack/Discord 接收指令
- 可在这些平台回复处理结果
- 所有工作流数据保留在本地，加密存储

---

## 7. 配置与调优

### 7.1 配置文件位置

```
macOS:   ~/Library/Application Support/openhuman/config.toml
Linux:   ~/.config/openhuman/config.toml
```

### 7.2 常用配置项

```toml
# === 模型路由 ===
[models]
reasoning = "claude-opus-4"       # 深度推理任务
fast = "gpt-4o"                   # 日常对话
vision = "gpt-4o"                 # 图像理解

# === 记忆配置 ===
[memory]
sync_interval = 1200              # 自动同步间隔（秒），默认 1200=20分钟
chunk_size = 3000                 # 记忆块最大 token 数
backend = "sqlite"                # "sqlite" 或 "agentmemory"

# === TokenJuice ===
[token_juice]
enabled = true
compress_html = true
shorten_urls = true
dedup_threshold = 0.85            # 去重相似度阈值

# === 隐私 ===
[privacy]
local_only = true                 # 所有数据仅存本地
encrypt_vault = true              # 加密 Obsidian Vault
screen_permission = "ask"         # "ask" | "always" | "never"

# === 桌面 ===
[desktop]
mascot_enabled = true
mascot_voice = "en-US-AriaNeural" # ElevenLabs voice ID
startup_behavior = "restore"      # "restore" | "fresh" | "minimized"
```

### 7.3 使用本地模型（Ollama）

```bash
# 1. 安装 Ollama
brew install ollama        # macOS
# 或访问 https://ollama.com 下载

# 2. 拉取模型
ollama pull llama3.2
ollama pull qwen2.5:72b

# 3. 在 config.toml 中配置
# [providers.ollama]
# base_url = "http://localhost:11434"
# default_model = "qwen2.5:72b"
```

---

## 8. 常见问题

### Q: 安装后无法启动？
**A:** 
- macOS：检查是否在「系统设置 → 隐私与安全性」中允许了来自未识别开发者的应用
- Linux：确保 `~/.local/bin` 在 PATH 中，`source ~/.bashrc` 后重试
- 查看日志：`tail -f ~/.openhuman/logs/app.log`

### Q: 内存/CPU 占用高？
**A:**
- TokenJuice 压缩和记忆同步会占用资源，可在配置中调高 `sync_interval`（如改为 3600 = 1小时）
- 关闭 mascot 动画：`[desktop] mascot_enabled = false`
- 减少同时连接的集成数量

### Q: 如何保证隐私？
**A:**
- 所有记忆数据存于本地 SQLite，不会上传云端
- 设置 `[privacy] local_only = true` 确保数据不外传
- Obsidian Vault 支持加密：`[privacy] encrypt_vault = true`
- API 调用仅传输经过 TokenJuice 压缩的必要上下文

### Q: 能否和 Claude Code/Cursor 共用记忆？
**A:** 可以。在 `config.toml` 中设置 `[memory] backend = "agentmemory"`，指向同一 agentmemory 实例。

### Q: 支持哪些语言？
**A:** UI 支持英文、简体中文、日文、韩文、德文。模型交互支持所有 LLM 原生语言。

### Q: 如何贡献代码？
**A:** 参考仓库中的 `CONTRIBUTING.md`（标准化 PR 流程）和 `CONTRIBUTING-BEGINNERS.md`（AI 代理辅助开发复制粘贴 prompt）。

---

## 9. 卸载

### macOS

```bash
# 删除应用
rm -rf ~/Applications/OpenHuman.app

# 删除用户数据（可选，会丢失所有记忆和配置）
rm -rf ~/Library/Application\ Support/openhuman
rm -rf ~/.openhuman
```

### Linux

```bash
# 删除二进制
rm -f ~/.local/bin/openhuman

# 删除桌面入口
rm -f ~/.local/share/applications/openhuman.desktop

# 删除用户数据（可选）
rm -rf ~/.config/openhuman
rm -rf ~/.openhuman
```

### Windows

```powershell
# 通过「设置 → 应用」卸载，或
Remove-Item -Recurse -Force "$env:LOCALAPPDATA\openhuman"
Remove-Item -Recurse -Force "$env:USERPROFILE\.openhuman"
```

---

## 快速链接

- **官网**：[tinyhumans.ai/openhuman](https://tinyhumans.ai/openhuman)
- **GitHub**：[tinyhumansai/openhuman](https://github.com/tinyhumansai/openhuman)
- **文档**：[docs](https://tinyhumans.ai/openhuman/docs)
- **Discord**：[社区](https://discord.gg/openhuman)
- **Reddit**：[r/openhuman](https://reddit.com/r/openhuman)
- **Twitter/X**：[@senamakel](https://x.com/senamakel)（作者）
- **问题反馈**：[GitHub Issues](https://github.com/tinyhumansai/openhuman/issues)

---

*文档版本：2026-05-20 · 基于 OpenHuman Early Beta · 中文版*
