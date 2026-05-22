# CAI (Cybersecurity AI) 完全使用指南

> 原文项目：[aliasrobotics/cai](https://github.com/aliasrobotics/cai)  
> 文档版本：v1.0  
> 更新日期：2026-05-22

---

## 一、项目概述

### 1.1 什么是 CAI？

**CAI（Cybersecurity AI）** 是由 Alias Robotics 开发的开源网络安全 AI 框架，旨在帮助安全研究人员、道德黑客和组织构建和部署 AI 驱动的自动化攻防安全工具。

CAI 是目前 AI 安全领域的事实标准框架，已被数千名个人用户和数百个组织采用。无论你是安全研究员、CTF 选手、Bug Bounty 猎人，还是企业安全团队，CAI 都能提供模块化、可扩展的 AI Agent 构建能力。

### 1.2 核心特性

| 特性 | 说明 |
|------|------|
| 🤖 **300+ AI 模型支持** | 支持 OpenAI、Anthropic、DeepSeek、Ollama 等主流 LLM |
| 🔧 **内置安全工具集** | 提供侦察、漏洞利用、权限提升等现成工具 |
| 🏆 **实战验证** | 在 HackTheBox CTF、Bug Bounty 和真实案例中验证有效 |
| 🎯 **Agent 架构** | 模块化设计，可为不同安全任务构建专用 Agent |
| 🛡️ **护栏保护** | 内置防御提示注入和危险命令执行的 Guardrails |
| 📚 **研究导向** |  democratize 网络安全 AI，降低技术门槛 |

### 1.3 项目数据

- **GitHub Stars**：⭐ 8,579+
- **Forks**：1,256+
- **主要语言**：Python
- **协议**：自定义协议（研究用途免费）
- **活跃状态**：持续更新

### 1.4 实战案例

| 领域 | 案例 |
|------|------|
| **机器人安全** | Unitree G1 人形机器人漏洞发现（未授权遥测传输、暴露 RSA 密钥） |
| **OT 安全** | Dragos OT CTF 2025 排名 Top-10，完成 32/34 挑战 |
| **Bug Bounty** | HackerOne 平台漏洞研究，启发 AI 去重 Agent |
| **物联网** | Ecoforest 热泵关键漏洞（未授权远程访问、DES 加密弱点） |
| **Web 安全** | Mercado Libre API 漏洞、PortSwigger Web Security Academy |

---

## 二、安装指南

### 2.1 前置要求

- **Python 3.12**（推荐）
- **虚拟环境**（强烈建议使用 venv）
- **API 密钥**（可选，取决于使用的模型）

### 2.2 快速安装（通用）

```bash
# 创建虚拟环境
python3.12 -m venv cai_env

# 激活环境
source cai_env/bin/activate  # Linux/macOS
# 或
# cai_env\Scripts\activate  # Windows

# 安装 CAI
pip install cai-framework

# 创建环境配置文件
cat > .env << 'EOF'
OPENAI_API_KEY="sk-your-key"
ANTHROPIC_API_KEY=""
OLLAMA=""
PROMPT_TOOLKIT_NO_CPR=1
CAI_STREAM=false
EOF

# 启动 CAI
cai  # 首次启动可能需要 30 秒
```

### 2.3 各平台详细安装

#### macOS

```bash
# 更新 Homebrew 并安装依赖
brew update && brew install git python@3.12

# 创建虚拟环境
python3.12 -m venv cai_env

# 激活并安装
source cai_env/bin/activate
pip install cai-framework

# 配置环境变量
echo -e 'OPENAI_API_KEY="sk-1234"\nANTHROPIC_API_KEY=""\nOLLAMA=""\nPROMPT_TOOLKIT_NO_CPR=1\nCAI_STREAM=false' > .env

# 启动
cai
```

#### Ubuntu 24.04

```bash
sudo apt-get update
sudo apt-get install -y git python3-pip python3.12-venv

python3.12 -m venv cai_env
source cai_env/bin/activate
pip install cai-framework

# 配置 .env
echo -e 'OPENAI_API_KEY="sk-1234"\nANTHROPIC_API_KEY=""\nOLLAMA=""\nPROMPT_TOOLKIT_NO_CPR=1\nCAI_STREAM=false' > .env

cai
```

#### Ubuntu 20.04

```bash
sudo apt-get update
sudo apt-get install -y software-properties-common

# 添加 Python 3.12 PPA
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.12 python3.12-venv python3.12-dev -y

# 后续步骤同上
python3.12 -m venv cai_env
source cai_env/bin/activate
pip install cai-framework

cai
```

#### Windows (WSL)

1. 在 PowerShell 中安装 WSL：
   ```powershell
   wsl --install
   ```
2. 进入 WSL Ubuntu 环境，按照 Ubuntu 步骤安装

#### Android (Termux)

CAI 支持在 Android 设备上运行（通过 Termux），具体步骤参考官方文档。

### 2.4 环境变量配置

复制项目中的 `.env.example` 为 `.env`，填入对应 API 密钥：

```bash
cp .env.example .env
```

常用环境变量：

| 变量 | 说明 | 示例 |
|------|------|------|
| `OPENAI_API_KEY` | OpenAI API 密钥 | `sk-...` |
| `ANTHROPIC_API_KEY` | Anthropic API 密钥 | `sk-ant-...` |
| `OLLAMA` | Ollama 本地服务地址 | `http://localhost:11434` |
| `CAI_MODEL` | 指定默认模型 | `gpt-4o`、`claude-3-7-sonnet` |
| `CAI_DEBUG` | 调试级别 | `0`=仅工具输出，`1`=详细调试 |
| `CAI_MAX_TURNS` | 最大交互轮数 | `10` |
| `CAI_GUARDRAILS` | 启用护栏保护 | `true`（默认） |
| `CAI_TRACING` | 启用追踪 | `true` |

### 2.5 自定义 OpenAI Base URL

如果使用第三方 API 代理或本地服务，可以设置自定义 base URL：

```bash
# 在 .env 中添加
OPENAI_API_BASE=https://your-custom-endpoint.com/v1
```

---

## 三、使用手册

### 3.1 首次启动

安装完成后，在终端输入：

```bash
cai
```

你将看到 CAI 的 ASCII Logo 和提示符：

```
          CCCCCCCCCCCCC      ++++++++   ++++++++      IIIIIIIIII
       CCC::::::::::::C  ++++++++++       ++++++++++  I::::::::I
     CC:::::::::::::::C ++++++++++         ++++++++++ I::::::::I
    C:::::CCCCCCCC::::C +++++++++    ++     +++++++++ II::::::II
   ...

                      Cybersecurity AI (CAI), vX.Y.Z
                          Bug bounty-ready AI

CAI>
```

底部导航栏会显示当前的系统信息（模型、状态等）。

### 3.2 基础命令

在 `CAI>` 提示符下，你可以直接输入自然语言命令让 AI 执行安全任务：

```bash
CAI> 扫描目标 192.168.1.1 的开放端口
CAI> 对 example.com 进行 Web 漏洞扫描
CAI> 分析这个 JWT 令牌的安全性
```

### 3.3 内置 CLI 命令

| 命令 | 说明 |
|------|------|
| `/history` | 查看交互历史 |
| `/compact` | 压缩历史记录 |
| `/graph` | 显示 Agent 交互图 |
| `/memory` | 管理记忆模式 |
| `/agent <id>` | 切换到指定 Agent |
| `/mcp load <url> <name>` | 加载 MCP 服务器 |
| `/mcp list` | 列出 MCP 连接 |

### 3.4 集成 OpenRouter

OpenRouter 提供统一的多模型访问接口：

```bash
# 在 .env 中配置
CAI_AGENT_TYPE=redteam_agent
CAI_MODEL=openrouter/meta-llama/llama-4-maverick
OPENROUTER_API_KEY=<your-key>
OPENROUTER_API_BASE=https://openrouter.ai/api/v1
```

### 3.5 集成 Azure OpenAI

企业环境可使用 Azure 托管模型：

```bash
CAI_AGENT_TYPE=redteam_agent
CAI_MODEL=azure/<deployment-name>
OPENAI_API_KEY=dummy  # 保持非空
AZURE_API_KEY=<your-azure-key>
AZURE_API_BASE=https://<resource>.openai.azure.com/openai/deployments/<deployment>/chat/completions?api-version=2025-01-01-preview
```

### 3.6 MCP（Model Context Protocol）集成

CAI 支持通过 MCP 连接外部工具（如 Burp Suite）：

```bash
# 通过 SSE 加载 MCP 服务器
CAI>/mcp load http://localhost:9876/sse burp

# 通过 STDIO 加载
CAI>/mcp load stdio myserver python mcp_server.py

# 将 MCP 工具添加到 Agent
CAI>/mcp add burp redteam_agent

# 查看所有 MCP 连接
CAI>/mcp list
```

### 3.7 Agent 模式与架构

#### Agent 类型

CAI 的核心是 **Agent**，每个 Agent 是具有特定角色的 AI 实体：

```python
from cai.sdk.agents import Agent, OpenAIChatCompletionsModel
from cai.tools.common import run_command
from cai.sdk.agents import function_tool

@function_tool
def execute_cli_command(command: str) -> str:
    return run_command(command)

# 创建 CTF Agent
ctf_agent = Agent(
    name="CTF Agent",
    description="专注于攻克安全挑战的 Agent",
    instructions="你是一位网络安全专家，面对 CTF 挑战",
    tools=[execute_cli_command],
    model=OpenAIChatCompletionsModel(
        model="qwen2.5:14b",
        openai_client=AsyncOpenAI(),
    )
)
```

#### Handoffs（任务移交）

Handoffs 允许 Agent 将任务委托给其他 Agent：

```python
flag_discriminator = Agent(
    name="Flag 验证器",
    description="专注于从输出中提取 flag",
    instructions="你是一个专门从给定输出中提取 flag 的 Agent。",
    model=OpenAIChatCompletionsModel(model="qwen2.5:14b", openai_client=AsyncOpenAI())
)

ctf_agent = Agent(
    name="CTF Agent",
    # ...
    handoffs=[flag_discriminator]  # 可以移交任务
)
```

#### Agentic Patterns（Agent 模式）

| 模式 | 说明 | 适用场景 |
|------|------|----------|
| **Swarm** | 去中心化，Agent 自主分配任务 | CTF 团队协作 |
| **Hierarchical** | 顶层 Agent 分配任务给子 Agent | 复杂渗透测试 |
| **Chain-of-Thought** | 线性流水线，A→B→C | 推理型任务 |
| **Auction-Based** | Agent 竞价获取任务 | 资源优化 |
| **Recursive** | 自迭代优化 | 代码生成与验证 |

### 3.8 Guardrails（安全护栏）

CAI 内置多层安全防护：

- **输入护栏**：检测并阻断提示注入攻击（模式匹配、Unicode 同形字符检测、AI 分析）
- **输出护栏**：验证 Agent 输出，阻止危险命令（反向 shell、fork bomb、数据外泄）
- **多层防御**：输入、处理、执行三阶段保护
- **编码感知**：自动解码 Base64/Base32 检测隐藏恶意载荷

启用/禁用：
```bash
export CAI_GUARDRAILS=true  # 或 false
```

### 3.9 Human-In-The-Loop（人机协作）

CAI 强调**半自主**操作，人类始终保有控制权：

- 按 `Ctrl+C` 随时中断 Agent 执行
- 审查 Agent 的每一步操作
- 提供专家判断和干预

```
┌─────────────────────────────────┐
│      Cybersecurity AI (CAI)     │
│       ┌─────────────────┐       │
│       │  Autonomous AI  │       │
│       └────────┬────────┘       │
│       ┌────────▼─────────┐      │
│       │ HITL Interaction │      │
│       └────────┬─────────┘      │
└────────────────┼────────────────┘
                 │ Ctrl+C
     ┌───────────▼───────────┐
     │   Human Operator(s)   │
     │  Expertise | Judgment │
     └───────────────────────┘
```

### 3.10 追踪与可观测性

CAI 集成 [Phoenix](https://github.com/Arize-ai/phoenix) 提供 OpenTelemetry 标准追踪：

```bash
export CAI_TRACING=true
```

可监控：Agent 交互、工具使用、攻击路径、漏洞发现过程。

---

## 四、典型使用场景

### 4.1 CTF 竞赛

```bash
export CTF_NAME="picoctf_static_flag"
export CTF_IP="10.10.10.10"
export CAI_AGENT_TYPE="ctf_agent"

cai
CAI> 开始攻克这个 CTF 挑战
```

### 4.2 Bug Bounty 侦察

```bash
CAI> 对 example.com 进行子域名枚举和端口扫描
CAI> 检查 api.example.com 的认证绕过可能性
```

### 4.3 代码安全审计

```bash
CAI> 分析这段代码的 SQL 注入风险
CAI> 检查这个 JWT 实现的安全性
```

### 4.4 红队演练

```bash
export CAI_AGENT_TYPE="redteam_agent"
cai
CAI> 模拟 APT 攻击路径，从初始访问到权限提升
```

---

## 五、开发与扩展

### 5.1 开发环境

使用 VS Code Dev Container：

```bash
git clone https://github.com/aliasrobotics/cai.git
cd cai
# 在 VS Code 中打开并进入 Dev Container 模式
```

### 5.2 自定义工具集成

```python
from cai.sdk.agents import function_tool

@function_tool
def my_custom_tool(param: str) -> str:
    """自定义工具的描述"""
    # 实现你的逻辑
    return result
```

### 5.3 扩展包

CAI 支持扩展包（`caiextensions`）增加额外功能：

```bash
pip install caiextensions  # 可选扩展
```

---

## 六、注意事项与伦理

### 6.1 法律合规

⚠️ **警告**：
- CAI 仅用于授权的安全测试、研究和教育目的
- **禁止**用于未经授权的系统入侵或网络犯罪
- 使用 CAI 即表示同意 LICENSE 和 DISCLAIMER 中的条款
- 遵守当地法律法规和道德准则

### 6.2 版本说明

- **Community Edition**：免费，适合研究和学习，300+ 模型支持
- **Professional Edition**（€350/月）：企业级，无限 `alias1` tokens，零拒绝率，专业支持

### 6.3 已知限制

- CAI 处于活跃开发中，可能存在 bug
- 复杂任务仍需人类专家监督
- 完全自主的安全系统尚不成熟

---

## 七、相关资源

| 资源 | 链接 |
|------|------|
| 官方仓库 | https://github.com/aliasrobotics/cai |
| 技术论文 | [arXiv:2504.06017](https://arxiv.org/pdf/2504.06017) |
| 案例研究 | https://aliasrobotics.com/case-studies-robot-cybersecurity.php |
| 视频教程 | [YouTube 播放列表](https://www.youtube.com/playlist?list=PLLc16OUiZWd4RuFdN5_Wx9xwjCVVbopzr) |
| Discord 社区 | https://discord.gg/fnUFcTaQAC |
| PyPI 包 | https://pypi.org/project/cai-framework/ |

---

## 八、引用

如果你在工作中使用了 CAI，请引用：

```bibtex
@article{cai2025,
  title={CAI: An Open, Bug Bounty-Ready Cybersecurity AI},
  author={Alias Robotics},
  journal={arXiv preprint arXiv:2504.06017},
  year={2025}
}
```

---

*本文档由 CAI 开源社区整理，遵循与原项目相同的伦理准则。Pentest for good!*
