# Pi 安装与使用指南

> **Pi** 是一个极简的 AI Agent 工具包，包含交互式编码 Agent CLI、统一多提供商 LLM API、Agent 运行时和终端 UI 库。

---

## 目录

- [项目简介](#项目简介)
- [系统要求](#系统要求)
- [安装方法](#安装方法)
  - [方法一：通过 npm 安装（推荐）](#方法一通过-npm-安装推荐)
  - [方法二：通过 install.sh 脚本安装](#方法二通过-installsh-脚本安装)
  - [方法三：从源码构建](#方法三从源码构建)
- [快速开始](#快速开始)
- [认证配置](#认证配置)
  - [OAuth 订阅认证](#oauth-订阅认证)
  - [API Key 认证](#api-key-认证)
  - [配置文件认证](#配置文件认证)
- [核心功能](#核心功能)
  - [交互式模式](#交互式模式)
  - [工具使用](#工具使用)
  - [会话管理](#会话管理)
  - [模型切换](#模型切换)
- [高级功能](#高级功能)
  - [提示模板（Prompt Templates）](#提示模板prompt-templates)
  - [技能（Skills）](#技能skills)
  - [扩展（Extensions）](#扩展extensions)
  - [主题（Themes）](#主题themes)
  - [Pi 包管理](#pi-包管理)
- [编程式使用](#编程式使用)
  - [使用 Pi AI SDK](#使用-pi-ai-sdk)
  - [使用 Pi Agent SDK](#使用-pi-agent-sdk)
- [故障排除](#故障排除)
- [参考资料](#参考资料)

---

## 项目简介

**Pi** 是 [earendil-works](https://github.com/earendil-works) 开发的 AI Agent 工具包，设计哲学是**保持核心极简，通过扩展实现无限可能**。

### 核心包

| 包名 | 说明 |
|------|------|
| `@earendil-works/pi-coding-agent` | 交互式编码 Agent CLI，支持终端 UI |
| `@earendil-works/pi-agent-core` | Agent 运行时，支持工具调用和状态管理 |
| `@earendil-works/pi-ai` | 统一多提供商 LLM API（OpenAI、Anthropic、Google 等） |
| `@earendil-works/pi-tui` | 终端 UI 库，支持差分渲染 |

### 主要特性

- ✅ **多提供商支持**：支持 25+ 个 LLM 提供商
- ✅ **工具调用**：自动工具发现、调用和结果处理
- ✅ **会话管理**：分支、压缩、持久化存储
- ✅ **可扩展**：通过扩展、技能、主题和提示模板自定义
- ✅ **多种运行模式**：交互式、打印/JSON、RPC、SDK 嵌入
- ✅ **TypeScript 原生**：完全类型安全

---

## 系统要求

- **Node.js**: >= 22.19.0
- **操作系统**: macOS、Linux、Windows（支持 WSL）
- **终端**: 支持 Unicode 和现代终端特性（推荐 iTerm2、Windows Terminal、alacritty）

### 验证 Node.js 版本

```bash
node --version
# 应显示 v22.19.0 或更高版本
```

如果 Node.js 版本过低，请通过 [nvm](https://github.com/nvm-sh/nvm) 或 [fnm](https://github.com/Schniz/fnm) 升级：

```bash
# 使用 nvm
nvm install 22
nvm use 22

# 使用 fnm
fnm install 22
fnm use 22
```

---

## 安装方法

### 方法一：通过 npm 安装（推荐）

```bash
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
```

> `--ignore-scripts` 禁用依赖生命周期脚本。Pi 正常 npm 安装不需要安装脚本。

### 方法二：通过 install.sh 脚本安装

```bash
curl -fsSL https://pi.dev/install.sh | sh
```

此脚本会自动检测系统架构并安装最新版本。

### 方法三：从源码构建

适合需要修改源码或贡献代码的开发者。

```bash
# 克隆仓库
git clone https://github.com/earendil-works/pi.git
cd pi

# 安装依赖（不运行生命周期脚本）
npm install --ignore-scripts

# 构建所有包
npm run build

# 运行测试
./test.sh

# 从源码运行 pi
./pi-test.sh
```

#### 源码构建顺序

由于包之间有依赖关系，必须按顺序构建：

```bash
cd packages/tui && npm run build
cd ../ai && npm run build
cd ../agent && npm run build
cd ../coding-agent && npm run build
```

或者使用根目录的构建命令：

```bash
npm run build
```

---

## 快速开始

### 1. 设置 API Key

#### 方式 A：环境变量

```bash
# Anthropic（推荐）
export ANTHROPIC_API_KEY=sk-ant-...

# 或 OpenAI
export OPENAI_API_KEY=sk-...

# 或 DeepSeek
export DEEPSEEK_API_KEY=sk-...
```

#### 方式 B：交互式登录

```bash
pi
/login  # 选择提供商并输入凭证
```

### 2. 启动 Pi

```bash
# 使用环境变量中的 API Key
pi

# 或指定提供商和模型
pi --provider anthropic --model claude-sonnet-4-5

# 或指定工作目录
pi /path/to/your/project
```

### 3. 开始使用

启动后，你会看到交互式终端界面。直接输入你的需求：

```
> 帮我写一个读取 CSV 文件并统计每列数据的 Python 脚本
```

Pi 会自动使用内置工具（`read`、`write`、`edit`、`bash`）来完成任务。

---

## 认证配置

Pi 支持两种认证方式：**OAuth 订阅**和**API Key**。

### OAuth 订阅认证

支持以下订阅服务：

| 提供商 | 说明 |
|--------|------|
| **ChatGPT Plus/Pro** | OpenAI Codex，官方推荐的 OSS 使用方式 |
| **Claude Pro/Max** | Anthropic 订阅，按 token 计费（不计入 Claude 计划限制） |
| **GitHub Copilot** | GitHub Copilot 订阅 |

使用方式：

```bash
pi
/login  # 选择提供商，按提示完成 OAuth 流程
/logout # 清除凭证
```

### API Key 认证

支持 25+ 个提供商：

| 提供商 | 环境变量 | auth.json 键 |
|--------|----------|---------------|
| Anthropic | `ANTHROPIC_API_KEY` | `anthropic` |
| OpenAI | `OPENAI_API_KEY` | `openai` |
| Azure OpenAI | `AZURE_OPENAI_API_KEY` | `azure-openai-responses` |
| DeepSeek | `DEEPSEEK_API_KEY` | `deepseek` |
| Google Gemini | `GEMINI_API_KEY` | `google` |
| Mistral | `MISTRAL_API_KEY` | `mistral` |
| Groq | `GROQ_API_KEY` | `groq` |
| Cerebras | `CEREBRAS_API_KEY` | `cerebras` |
| xAI | `XAI_API_KEY` | `xai` |
| OpenRouter | `OPENROUTER_API_KEY` | `openrouter` |
| Vercel AI Gateway | `AI_GATEWAY_API_KEY` | `vercel-ai-gateway` |
| Hugging Face | `HF_TOKEN` | `huggingface` |
| Fireworks | `FIREWORKS_API_KEY` | `fireworks` |
| Together AI | `TOGETHER_API_KEY` | `together` |
| Kimi For Coding | `KIMI_API_KEY` | `kimi-coding` |
| MiniMax | `MINIMAX_API_KEY` | `minimax` |
| Xiaomi MiMo | `XIAOMI_API_KEY` | `xiaomi` |
| Amazon Bedrock | `AWS_ACCESS_KEY_ID` / `AWS_PROFILE` | `amazon-bedrock` |
| Cloudflare AI Gateway | `CLOUDFLARE_API_KEY` | `cloudflare-ai-gateway` |
| Cloudflare Workers AI | `CLOUDFLARE_API_KEY` | `cloudflare-workers-ai` |

### 配置文件认证

凭证存储在 `~/.pi/agent/auth.json`，创建时权限为 `0600`（仅用户可读写）。

```json
{
  "anthropic": { "type": "api_key", "key": "sk-ant-..." },
  "openai": { "type": "api_key", "key": "sk-..." },
  "deepseek": { "type": "api_key", "key": "sk-..." }
}
```

#### Key 格式支持

`key` 字段支持三种格式：

1. **字面量值**：直接使用
   ```json
   { "type": "api_key", "key": "sk-ant-..." }
   ```

2. **环境变量引用**：使用变量值
   ```json
   { "type": "api_key", "key": "MY_ANTHROPIC_KEY" }
   ```

3. **Shell 命令**：执行命令并使用 stdout（进程生命周期内缓存）
   ```json
   { "type": "api_key", "key": "!security find-generic-password -ws 'anthropic'" }
   { "type": "api_key", "key": "!op read 'op://vault/item/credential'" }
   ```

### 凭证解析顺序

1. CLI `--api-key` 参数
2. `auth.json` 中的条目（API key 或 OAuth token）
3. 环境变量
4. `models.json` 中的自定义提供商密钥

---

## 核心功能

### 交互式模式

启动后，界面从上到下分为：

- **启动头部**：显示快捷键、加载的 AGENTS.md、提示模板、技能和扩展
- **消息区**：用户消息、助手回复、工具调用和结果、通知、错误、扩展 UI
- **编辑器**：输入区域，边框颜色表示思考级别
- **底部栏**：工作目录、会话名称、Token/缓存使用量、成本、上下文使用率、当前模型

#### 编辑器快捷键

| 功能 | 操作 |
|------|------|
| 文件引用 | 输入 `@` 模糊搜索项目文件 |
| 路径补全 | Tab 键补全路径 |
| 多行输入 | Shift+Enter（Windows Terminal 用 Ctrl+Enter） |
| 粘贴图片 | Ctrl+V（Windows 用 Alt+V）或拖拽到终端 |
| 执行 Bash | `!command` 执行并发送输出给 LLM，`!!command` 执行但不发送 |

#### 常用命令

在编辑器中输入 `/` 触发命令：

| 命令 | 说明 |
|------|------|
| `/login`, `/logout` | OAuth 认证 |
| `/model` | 切换模型 |
| `/settings` | 思考级别、主题、消息传递、传输设置 |
| `/resume` | 从之前的会话继续 |
| `/new` | 开始新会话 |
| `/name <name>` | 设置会话显示名称 |
| `/session` | 显示会话信息 |
| `/tree` | 跳转到会话任意时间点并继续 |
| `/fork` | 从之前的用户消息创建新会话 |
| `/clone` | 复制当前分支到新会话 |
| `/compact [prompt]` | 手动压缩上下文 |
| `/copy` | 复制最后一条助手消息到剪贴板 |
| `/export [file]` | 导出会话为 HTML |
| `/share` | 上传为私有 GitHub gist |
| `/reload` | 重新加载配置 |
| `/hotkeys` | 显示所有快捷键 |
| `/quit` | 退出 |

#### 键盘快捷键

| 快捷键 | 动作 |
|--------|------|
| Ctrl+C | 清除编辑器 |
| Ctrl+C 两次 | 退出 |
| Escape | 取消/中止 |
| Escape 两次 | 打开 `/tree` |
| Ctrl+L | 打开模型选择器 |
| Ctrl+P / Shift+Ctrl+P | 向前/向后切换模型 |
| Shift+Tab | 切换思考级别 |
| Ctrl+O | 折叠/展开工具输出 |
| Ctrl+T | 折叠/展开思考块 |

### 工具使用

Pi 默认给模型四个工具：

- **`read`**: 读取文件内容
- **`write`**: 写入文件
- **`edit`**: 编辑文件
- **`bash`**: 执行 shell 命令

模型根据你的请求自动选择和使用工具。

### 会话管理

#### 会话持久化

会话自动保存到 `~/.pi/agent/sessions/`，按工作目录组织。

```bash
pi -c                  # 继续最近的会话
pi -r                  # 浏览并选择历史会话
pi --no-session        # 临时模式（不保存）
pi --session <path|id> # 使用特定会话文件或 ID
pi --fork <path|id>    # 从特定会话创建分支
```

#### 会话分支

**`/tree`** - 在原地导航会话树。选择任意历史点继续，所有历史保存在单个文件中。

**`/fork`** - 从当前分支的某个用户消息创建新会话文件。

**`/clone`** - 将当前分支复制到新会话文件。

#### 上下文压缩

长会话可能耗尽上下文窗口。压缩会总结旧消息同时保留近期消息。

- **手动压缩**: `/compact` 或 `/compact <自定义指令>`
- **自动压缩**: 默认启用，在上下文溢出或接近限制时触发

### 模型切换

#### 支持的模型

Pi 内置支持所有主要提供商的工具调用模型，列表随每次发布更新。

切换模型：

```bash
# 交互式选择
/model

# 或使用快捷键
Ctrl+L        # 打开模型选择器
Ctrl+P        # 切换到下一个模型
Shift+Ctrl+P  # 切换到上一个模型
```

---

## 高级功能

### 提示模板（Prompt Templates）

可复用的 Markdown 格式提示。输入 `/模板名` 展开。

创建提示模板：

```markdown
<!-- ~/.pi/agent/prompts/review.md -->
Review this code for bugs, security issues, and performance problems.
Focus on: {{focus}}
```

放置位置：
- `~/.pi/agent/prompts/`（全局）
- `.pi/prompts/`（项目级）
- Pi 包中（可分享）

### 技能（Skills）

按需加载的能力包，遵循 [Agent Skills 标准](https://agentskills.io)。通过 `/skill:名称` 调用或让 Agent 自动加载。

创建技能：

```markdown
<!-- ~/.pi/agent/skills/my-skill/SKILL.md -->
# My Skill
Use this skill when the user asks about X.

## Steps
1. Do this
2. Then that
```

放置位置：
- `~/.pi/agent/skills/`
- `~/.agents/skills/`
- `.pi/skills/` / `.agents/skills/`（从当前目录向上遍历）
- Pi 包中（可分享）

### 扩展（Extensions）

TypeScript 模块，可扩展 Pi 的自定义工具、命令、快捷键、事件处理器和 UI 组件。

```typescript
export default function (pi: ExtensionAPI) {
  pi.registerTool({ name: "deploy", ... });
  pi.registerCommand("stats", { ... });
  pi.on("tool_call", async (event, ctx) => { ... });
}
```

扩展可以做到：
- 自定义工具（或替换内置工具）
- 子 Agent 和计划模式
- 自定义压缩和总结
- 权限门控和路径保护
- 自定义编辑器和 UI 组件
- 状态栏、头部、底部
- Git 检查点和自动提交
- SSH 和沙箱执行
- MCP 服务器集成
- 游戏（比如 Doom）

放置位置：
- `~/.pi/agent/extensions/`
- `.pi/extensions/`
- Pi 包中（可分享）

### 主题（Themes）

内置主题：`dark`、`light`。主题支持热重载。

放置位置：
- `~/.pi/agent/themes/`
- `.pi/themes/`
- Pi 包中（可分享）

### Pi 包管理

通过 npm 或 git 分享扩展、技能、提示和主题。

```bash
# 安装包
pi install npm:@foo/pi-tools
pi install npm:@foo/pi-tools@1.2.3      # 固定版本
pi install git:github.com/user/repo
pi install git:github.com/user/repo@v1  # 标签或提交
pi install https://github.com/user/repo

# 管理包
pi remove npm:@foo/pi-tools
pi uninstall npm:@foo/pi-tools          # remove 的别名
pi list                                 # 列出已安装包
pi update                               # 更新 pi 和包（跳过固定版本）
pi update --extensions                  # 只更新包
pi update --self                        # 只更新 pi
pi update --self --force                # 强制重装 pi
pi update npm:@foo/pi-tools             # 更新单个包
pi config                               # 启用/禁用扩展、技能、提示、主题
```

#### 创建 Pi 包

在 `package.json` 中添加 `pi` 字段：

```json
{
  "name": "my-pi-package",
  "keywords": ["pi-package"],
  "pi": {
    "extensions": ["./extensions"],
    "skills": ["./skills"],
    "prompts": ["./prompts"],
    "themes": ["./themes"]
  }
}
```

没有 `pi` manifest 时，Pi 自动从约定目录（`extensions/`、`skills/`、`prompts/`、`themes/`）发现。

---

## 编程式使用

### 使用 Pi AI SDK

```typescript
import { Type, getModel, stream, complete, Context, Tool } from '@earendil-works/pi-ai';

// 获取模型（支持自动补全）
const model = getModel('openai', 'gpt-4o-mini');

// 定义工具（使用 TypeBox 模式）
const tools: Tool[] = [{
  name: 'get_time',
  description: 'Get the current time',
  parameters: Type.Object({
    timezone: Type.Optional(Type.String({ description: 'Optional timezone' }))
  })
}];

// 构建上下文
const context: Context = {
  systemPrompt: 'You are a helpful assistant.',
  messages: [{ role: 'user', content: 'What time is it?' }],
  tools
};

// 流式输出
const s = stream(model, context);
for await (const event of s) {
  switch (event.type) {
    case 'text_delta':
      process.stdout.write(event.delta);
      break;
    case 'toolcall_end':
      console.log(`\nTool called: ${event.toolCall.name}`);
      break;
    case 'done':
      console.log(`\nFinished: ${event.reason}`);
      break;
  }
}

// 非流式输出
const response = await complete(model, context);
for (const block of response.content) {
  if (block.type === 'text') {
    console.log(block.text);
  }
}
```

### 使用 Pi Agent SDK

```typescript
import { AuthStorage, createAgentSession, ModelRegistry, SessionManager } from "@earendil-works/pi-coding-agent";

const authStorage = AuthStorage.create();
const modelRegistry = ModelRegistry.create(authStorage);
const { session } = await createAgentSession({
  modelRegistry,
  sessionManager: new SessionManager()
});
```

---

## 故障排除

### 安装问题

**Q: npm install 失败**
```bash
# 使用 --ignore-scripts
npm install -g --ignore-scripts @earendil-works/pi-coding-agent

# 或清除缓存后重试
npm cache clean --force
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
```

**Q: 权限错误**
```bash
# 使用 npx（无需全局安装）
npx @earendil-works/pi-coding-agent

# 或修改 npm 全局目录权限
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH
```

### 认证问题

**Q: 提示 "No credentials found"**
- 检查环境变量是否正确设置
- 运行 `/login` 进行交互式认证
- 检查 `~/.pi/agent/auth.json` 是否存在且格式正确

**Q: API Key 无效**
- 确认 Key 有正确的权限（需要 tool calling 支持）
- 检查 Key 是否过期
- 尝试重新生成 Key

### 运行时问题

**Q: 模型不响应工具调用**
- 确认使用的是支持工具调用的模型
- 使用 `/model` 检查当前模型

**Q: 上下文窗口溢出**
- 使用 `/compact` 手动压缩
- 启用自动压缩（默认已启用）
- 使用 `/tree` 创建分支会话

**Q: 终端显示异常**
- 确认终端支持 Unicode
- 检查终端字体设置
- 参考 [docs/terminal-setup.md](https://github.com/earendil-works/pi/blob/main/packages/coding-agent/docs/terminal-setup.md)

### 网络问题

**Q: 更新检查失败**
```bash
# 跳过版本检查
export PI_SKIP_VERSION_CHECK=1
pi

# 或完全离线模式
pi --offline
# 或
export PI_OFFLINE=1
```

---

## 参考资料

- **官方网站**: [pi.dev](https://pi.dev)
- **GitHub 仓库**: [earendil-works/pi](https://github.com/earendil-works/pi)
- **Discord 社区**: [discord.com/invite/3cU7Bz4UPx](https://discord.com/invite/3cU7Bz4UPx)
- **npm 包**: [@earendil-works/pi-coding-agent](https://www.npmjs.com/package/@earendil-works/pi-coding-agent)
- **完整文档**: [pi.dev/docs/latest](https://pi.dev/docs/latest)
- **贡献指南**: [CONTRIBUTING.md](https://github.com/earendil-works/pi/blob/main/CONTRIBUTING.md)

### 相关项目

- **Pi Chat**: [earendil-works/pi-chat](https://github.com/earendil-works/pi-chat) - Slack/聊天自动化
- **OpenClaw**: [openclaw/openclaw](https://github.com/openclaw/openclaw) - SDK 集成示例
- **Pi Share HF**: [badlogic/pi-share-hf](https://github.com/badlogic/pi-share-hf) - 分享 OSS 会话数据

---

*本文档基于 Pi 项目官方 README 和相关文档编写，旨在帮助中文用户快速上手。*
