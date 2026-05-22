# UI-TARS Desktop — 安装与使用指南

> ByteDance 开源的多模态 AI Agent 桌面应用：用自然语言控制你的电脑和浏览器。
> The Open-Source Multimodal AI Agent Stack: Connecting Cutting-Edge AI Models and Agent Infra.

## 项目概述

`bytedance/UI-TARS-desktop` 是字节跳动开源的多模态 AI Agent 技术栈，包含两个核心产品：

| 产品 | 定位 | 形态 |
|------|------|------|
| **Agent TARS** | 通用多模态 AI Agent | CLI 命令行 + Web UI |
| **UI-TARS Desktop** | 桌面 GUI Agent | 原生桌面应用（macOS/Windows） |

两者都基于 UI-TARS 视觉语言模型，能看懂你的屏幕截图，并用自然语言指令操控鼠标、键盘、浏览器。

### 核心数据
- ⭐ 31.7k Stars | 🍴 3,150 Forks
- License: Apache 2.0
- 论文：[UI-TARS: Pioneering Automated GUI Interaction with Native Agents](https://arxiv.org/abs/2501.12326)
- 模型：[UI-TARS-1.5-7B](https://huggingface.co/ByteDance-Seed/UI-TARS-1.5-7B) (Hugging Face)

### 能做什么？

```
"帮我在 VS Code 里打开自动保存功能，延迟设为 500 毫秒"
"帮我查一下 UI-TARS-Desktop 项目最新的 Issue"
"帮我在 Priceline 上订一张从圣何塞到纽约最早的机票"
"帮我画一张杭州一个月天气的图表"
"帮我在 booking.com 订预算 5000 美元的酒店"
```

Agent 会像真人一样：看屏幕 → 理解界面 → 移动鼠标 → 点击 → 输入 → 确认结果。

### 核心特性

- 🤖 **自然语言操控**：用中文或英文描述任务，Agent 自动执行
- 🖥️ **屏幕截图 + 视觉识别**：基于 UI-TARS 视觉语言模型理解任何界面
- 🎯 **精确键鼠控制**：点击、拖拽、输入、滚动，精确定位 UI 元素
- 💻 **跨平台**：macOS / Windows 桌面 + 浏览器操控
- 🔄 **实时反馈**：每一步操作都有状态显示和截屏回放
- 🔐 **本地处理**：支持本地模型部署，数据不出本机
- 🌐 **远程操控**：支持远程控制其他电脑和浏览器（免费）
- 🧰 **MCP 集成**：内核基于 MCP 协议，可挂载第三方工具
- 📦 **SDK 可用**：`@ui-tars/sdk` 支持构建自定义 GUI Agent

---

## 产品对比：选哪个？

| 场景 | 推荐产品 | 安装方式 |
|------|---------|---------|
| 用自然语言操控本地桌面 | **UI-TARS Desktop** | 下载安装包或 `brew install` |
| 在终端里用 Agent 自动化 | **Agent TARS CLI** | `npx @agent-tars/cli@latest` |
| 在浏览器里用 Web UI | **Agent TARS Web UI** | 启动 CLI 后自动打开 |
| 自定义开发 GUI Agent | **@ui-tars/sdk** | `npm install @ui-tars/sdk` |
| 远程控制其他电脑 | **UI-TARS Desktop Remote** | Desktop App 内置 |

---

## 前置条件

### UI-TARS Desktop（桌面应用）
- macOS 10.15+ 或 Windows 10+
- **单显示器**（多显示器可能导致部分任务失败）
- **Chrome / Edge / Firefox**（如需使用 Browser Operator）
- 辅助功能和屏幕录制权限（macOS 需手动开启）

### Agent TARS CLI
- **Node.js >= 22**
- npm 或 npx
- 模型 API Key（Hugging Face 或火山引擎）

---

## 安装方式

### 方式一：UI-TARS Desktop（桌面应用 · 推荐入门）

#### macOS

```bash
# 方法 A：Homebrew 一键安装（推荐）
brew install --cask ui-tars

# 方法 B：手动下载
# 访问 https://github.com/bytedance/UI-TARS-desktop/releases/latest
# 下载最新的 .dmg 文件，拖入 Applications 文件夹
```

安装后，**务必开启系统权限**：

1. **系统设置 → 隐私与安全性 → 辅助功能** → 开启 UI TARS
2. **系统设置 → 隐私与安全性 → 屏幕录制** → 开启 UI TARS

> ⚠️ 这两项权限是必须的，否则 Agent 无法截图和操控鼠标。

#### Windows

1. 访问 [Releases 页面](https://github.com/bytedance/UI-TARS-desktop/releases/latest)
2. 下载最新的 `.exe` 安装包
3. 双击安装并运行

---

### 方式二：Agent TARS CLI（命令行 · 开发者首选）

```bash
# 方法 A：npx 直接运行（无需安装）
npx @agent-tars/cli@latest

# 方法 B：全局安装
npm install @agent-tars/cli@latest -g

# 运行并指定模型
agent-tars --provider volcengine \
  --model doubao-1-5-thinking-vision-pro-250428 \
  --apiKey your-api-key
```

启动后会自动打开一个 Web UI 界面，你也可以在纯终端中使用。

---

### 方式三：@ui-tars/sdk（自定义开发）

```bash
npm install @ui-tars/sdk

# 快速尝试 CLI SDK
npx @ui-tars/cli start
```

SDK 提供三个核心抽象：

```
GUIAgent ─── UITarsModel (视觉模型)
    │
    └── Operator (执行器)
         ├── NutJSOperator  (桌面操控)
         ├── WebOperator    (浏览器操控)
         └── MobileOperator (移动端操控)
```

---

## 配置模型

UI-TARS Desktop 支持两种模型后端：

### 选项 A：Hugging Face 部署 UI-TARS-1.5-7B

1. 访问 [UI-TARS-1.5-7B on Hugging Face](https://huggingface.co/ByteDance-Seed/UI-TARS-1.5-7B)
2. 点击右上角 **"Deploy from Hugging Face"**
3. 选择 UI-TARS-1.5-7B 模型
4. 参考 [部署文档](https://github.com/bytedance/UI-TARS/blob/main/README_deploy.md) 获取 **Base URL**、**API Key** 和 **Model Name**
5. 打开 UI-TARS Desktop → Settings，填写：

```yaml
Language: en
VLM Provider: Hugging Face for UI-TARS-1.5
VLM Base URL: https://your-endpoint.huggingface.cloud/v1/
VLM API KEY: hf_xxxxxxxxxxxxx
VLM Model Name: UI-TARS-1.5-7B
```

> ⚠️ Base URL 必须以 `/v1/` 结尾。Provider 必须选 "Hugging Face for UI-TARS-1.5"，否则 Action 解析会出错。

### 选项 B：火山引擎 Doubao-1.5-UI-TARS（国内推荐）

1. 访问 [火山引擎 Doubao-1.5-UI-TARS](https://console.volcengine.com/ark/region:ark+cn-beijing/model/detail?Id=doubao-1-5-ui-tars)
2. 点击右上角 **"立即体验"** → **"API 接入"**
3. 在弹窗中获取 **API Key**（步骤 1）和 **Base URL + Model Name**（步骤 2，切换到 OpenAI SDK 标签页）
4. 打开 UI-TARS Desktop → Settings，填写：

```yaml
Language: cn
VLM Provider: VolcEngine Ark for Doubao-1.5-UI-TARS
VLM Base URL: https://ark.cn-beijing.volces.com/api/v3
VLM API KEY: your-api-key
VLM Model Name: doubao-1.5-ui-tars-250328
```

> Provider 必须选 "VolcEngine Ark for Doubao-1.5-UI-TARS"。

---

## 使用指南

### UI-TARS Desktop 使用流程

#### 1. 启动应用

安装并配置好模型后，打开 UI TARS 应用，你会看到主界面。

#### 2. 选择操控模式

UI-TARS Desktop 支持两种模式：

| 模式 | 功能 | 说明 |
|------|------|------|
| **Computer Operator** | 控制当前电脑 | 看屏幕→识别界面→操控鼠标键盘 |
| **Browser Operator** | 控制浏览器 | 直接在浏览器中执行网页操作 |

> 使用 Browser Operator 前，确保 Chrome / Edge / Firefox 已安装。

#### 3. 开始新对话

点击开始按钮，在新的对话中输入自然语言指令：

```
帮我在 VS Code 设置里把自动保存打开，延迟 500 毫秒
```

```
帮我在 GitHub 上查一下 UI-TARS-Desktop 项目最新的 Open Issue
```

```
帮我把桌面上所有 .png 文件移动到一个叫 screenshots 的文件夹
```

Agent 会：
1. 截取当前屏幕
2. 分析界面元素
3. 规划操作步骤
4. 执行鼠标/键盘操作
5. 验证结果
6. 如果出错，自动重试

#### 4. 实时监控

执行过程中你可以看到：
- 当前屏幕截图
- Agent 的"思考过程"
- 每一步的操作日志
- 执行状态（运行中/已完成/出错）

---

### Agent TARS CLI 使用

#### 基础命令

```bash
# 启动 CLI
agent-tars

# 指定 Provider 和模型
agent-tars --provider anthropic --model claude-sonnet-4-20250514 --apiKey $ANTHROPIC_API_KEY

# 使用火山引擎
agent-tars --provider volcengine --model doubao-1-5-thinking-vision-pro-250428 --apiKey $VOLC_API_KEY
```

#### CLI 内交互

启动后直接在终端输入指令：

```
> 帮我在浏览器里打开 GitHub Trending 页面，截图保存到桌面
> 帮我在浏览器里搜索"UI-TARS paper"，打开第一篇论文
```

CLI 支持：
- **Headful 模式**：Web UI 界面，可视化操作过程
- **Headless 模式**：纯命令行，适合自动化脚本
- **Hybrid Browser Agent**：GUI Agent / DOM / 混合策略，自动选择最优方式
- **MCP 工具挂载**：接入第三方工具和服务
- **事件流（Event Stream）**：实时追踪数据流和调试

#### 支持的 Provider

| Provider | 模型 | 说明 |
|----------|------|------|
| `volcengine` | `doubao-1-5-thinking-vision-pro-250428` | 火山引擎（国内） |
| `anthropic` | `claude-sonnet-4-20250514` | Claude（需 Vision 能力） |

---

### SDK 自定义开发

```typescript
import { GUIAgent } from '@ui-tars/sdk';
import { NutJSOperator } from '@ui-tars/sdk/operator';

const agent = new GUIAgent({
  model: {
    baseURL: 'https://your-endpoint/v1/',
    apiKey: 'your-api-key',
    model: 'UI-TARS-1.5-7B',
  },
  operator: new NutJSOperator(),
});

// 执行任务
agent.run('帮我在 VS Code 里打开 settings.json');
```

SDK 支持构建自定义 Operator：
- **NutJSOperator**：桌面应用操控（鼠标/键盘/截图）
- **WebOperator**：浏览器操控（DOM 交互/页面导航）
- **MobileOperator**：移动端操控
- **自定义 Operator**：实现 `Operator` 接口即可

---

## 远程操控（Remote Operator）

UI-TARS Desktop 内置了远程操控功能，可以控制其他电脑或浏览器：

1. 在 UI-TARS Desktop 中选择 **Remote Operator** 模式
2. 输入远程设备的连接信息
3. 发送自然语言指令 → Agent 在远程设备上执行

> ⚠️ 官方的免费 Remote Operator 服务已于 2025 年 8 月 20 日停止。如需部署自己的远程 Agent，可使用火山引擎的 [Computer Use Agent](https://console.volcengine.com/vefaas/region:vefaas+cn-beijing/application/create?templateId=680b0a890e881f000862d9f0&channel=github&source=ui-tars) 和 [Browser Use Agent](https://console.volcengine.com/vefaas/region:vefaas+cn-beijing/application/create?templateId=67f7b4678af5a6000850556c&channel=github&source=ui-tars) 模板。

---

## 进阶配置

### Settings 详解

打开 UI-TARS Desktop → Settings，可配置：

| 配置项 | 说明 | 推荐值 |
|--------|------|--------|
| Language | 界面语言 | `cn` 或 `en` |
| VLM Provider | 视觉模型提供商 | Hugging Face / VolcEngine |
| VLM Base URL | 模型 API 地址 | 以 `/v1/` 结尾 |
| VLM API KEY | API 密钥 | — |
| VLM Model Name | 模型名称 | `UI-TARS-1.5-7B` 或 `doubao-1.5-ui-tars-250328` |
| Chat Provider | 对话模型（可选） | 用于复杂推理 |

详见 [Settings 配置指南](https://github.com/bytedance/UI-TARS-desktop/blob/main/docs/setting.md)。

---

## 项目结构

```
UI-TARS-desktop/
├── apps/
│   └── ui-tars/               # Electron 桌面应用
│       ├── src/                # 应用源代码
│       ├── resources/          # 图标和资源
│       └── images/             # 安装截图
├── packages/
│   ├── ui-tars-sdk/            # @ui-tars/sdk (GUI Agent 开发工具包)
│   ├── agent-tars-cli/         # @agent-tars/cli (命令行工具)
│   └── ...                     # 其他子包
├── docs/                       # 文档
│   ├── quick-start.md          # 快速开始
│   ├── setting.md              # 设置指南
│   ├── sdk.md                  # SDK 开发指南
│   └── deployment.md           # 模型部署指南
├── README.md                   # 项目总览
├── CONTRIBUTING.md             # 贡献指南
└── LICENSE                     # Apache 2.0
```

---

## 常见问题

### Q: macOS 上启动报权限错误？

去 **系统设置 → 隐私与安全性**，确保 **辅助功能** 和 **屏幕录制** 两项都已授权给 UI TARS。如果还是不行，尝试移除后重新添加。

### Q: 多显示器不工作？

UI-TARS Desktop 目前仅支持单显示器设置。多显示器可能导致截图定位偏移和任务失败。

### Q: 模型返回结果不对？

检查 Settings 中的 **VLM Provider** 选择是否正确。UI-TARS-1.5 模型有专属的 Action 解析逻辑，选错 Provider 会导致指令无法正确执行。

### Q: 有没有免费模型？

- Hugging Face 的 UI-TARS-1.5-7B 是开源模型，你可以用自己的 GPU 部署
- 火山引擎的 Doubao-1.5-UI-TARS 提供一定免费额度
- Agent TARS CLI 支持接入你自己的 OpenAI 兼容 API

### Q: 支持 Linux 吗？

桌面应用目前仅支持 macOS 和 Windows。Agent TARS CLI 和 SDK 支持 Linux（Node.js 环境）。

### Q: 如何关闭正在执行的任务？

在 UI-TARS Desktop 界面中直接点击停止按钮，或发送 AbortSignal 到 GUIAgent。

---

## 最佳实践

1. **任务描述越具体越好**：不是"帮我改设置"，而是"帮我在 VS Code 设置里把自动保存打开，延迟 500 毫秒"
2. **从简单任务开始**：先试"打开 Chrome"→ 再试"打开 GitHub"→ 再试复杂任务
3. **保持屏幕整洁**：Agent 看到的东西越少，定位越准确
4. **使用单显示器**：避免多显示器导致的问题
5. **监控执行过程**：首次使用建议盯着看，了解 Agent 的操作习惯，逐步建立信任
6. **本地部署更安全**：涉及敏感操作时，用本地模型而非云端 API

---

## 许可证

Apache License 2.0

## 相关链接

- 官方仓库：https://github.com/bytedance/UI-TARS-desktop
- 论文：https://arxiv.org/abs/2501.12326
- 模型（Hugging Face）：https://huggingface.co/ByteDance-Seed/UI-TARS-1.5-7B
- 模型（ModelScope）：https://www.modelscope.cn/collections/UI-TARS-bccb56fa1ef640
- 官方网站：https://agent-tars.com
- Discord 社区：https://discord.gg/pTXwYVjfcs
- 火山引擎模型：https://console.volcengine.com/ark/region:ark+cn-beijing/model/detail?Id=doubao-1-5-ui-tars
- Midscene（浏览器端）：https://github.com/web-infra-dev/midscene
