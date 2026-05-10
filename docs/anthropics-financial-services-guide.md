# Anthropic Financial Services — 安装与使用指南

> Claude for Financial Services：面向投资银行、股票研究、私募股权和财富管理的 AI Agent 工作流集合。

## 项目概述

`anthropics/financial-services` 是 Anthropic 官方开源的金融服务 AI 工作流库。它提供了一套预构建的 Claude Agent、领域 Skills 和数据连接器，覆盖金融行业最常见的业务场景。

### 核心特性

- **9 个命名 Agent**：Pitch Agent（投行路演）、Market Researcher（市场研究）、GL Reconciler（总账对账）等，端到端覆盖完整工作流
- **8 个垂直领域插件**：投行、股票研究、私募股权、财富管理、基金行政、合规运营等
- **11 个数据连接器**：预集成 Bloomberg 替代品（Morningstar、FactSet、S&P Global、LSEG、PitchBook 等）的 MCP 接口
- **双模式部署**：既可作为 Claude Cowork 插件使用，也可通过 Managed Agents API 在自有基础设施上部署
- **纯文件驱动**：所有内容均为 Markdown + JSON，无构建步骤，可直接 fork 修改

### 适用人群

- 投行分析师：快速生成路演材料、可比公司分析
- 股票研究员：自动化财报分析、模型更新
- 私募股权从业者：项目筛选、尽调清单、IC 备忘录
- 基金运营：总账对账、月末结账、LP 报告
- 合规团队：KYC 文档解析与规则引擎

---

## 前置条件

### Claude Cowork 方式
- Claude Cowork 订阅（Pro/Team/Enterprise）
- 网络可访问此 GitHub 仓库

### Claude Code 方式
- Claude Code CLI 已安装
- Anthropic API Key（如需使用 API）

### Managed Agents API 方式
- Anthropic API Key（`sk-ant-...`）
- Python 3.10+
- Bash 环境

---

## 安装方式

### 方式一：Claude Cowork（推荐入门）

#### 1. 添加插件市场

在 Claude Cowork 中，打开 **Settings → Plugins → Add plugin**：

**选项 A：直接粘贴仓库 URL**
```
https://github.com/anthropics/financial-services
```
然后从市场列表中选择你需要的 Agent 和垂直插件。

**选项 B：上传 ZIP 包**
将 `plugins/` 下的任意子目录（如 `plugins/agent-plugins/pitch-agent/`）打包为 ZIP，直接拖入。

#### 2. 安装推荐组合

| 你的角色 | 推荐安装 |
|---------|---------|
| 投行分析师 | `financial-analysis` + `investment-banking` + `pitch-agent` + `meeting-prep-agent` |
| 股票研究员 | `financial-analysis` + `equity-research` + `market-researcher` + `earnings-reviewer` |
| 私募股权 | `financial-analysis` + `private-equity` + `model-builder` |
| 基金运营 | `financial-analysis` + `fund-admin` + `gl-reconciler` + `month-end-closer` |

---

### 方式二：Claude Code CLI

```bash
# 第一步：添加插件市场
claude plugin marketplace add anthropics/financial-services

# 第二步：安装核心技能 + 数据连接器（必须先装）
claude plugin install financial-analysis@financial-services

# 第三步：安装你需要的 Agent
claude plugin install pitch-agent@financial-services
claude plugin install market-researcher@financial-services
claude plugin install gl-reconciler@financial-services
claude plugin install earnings-reviewer@financial-services
claude plugin install model-builder@financial-services
claude plugin install meeting-prep-agent@financial-services
claude plugin install kyc-screener@financial-services
claude plugin install month-end-closer@financial-services
claude plugin install valuation-reviewer@financial-services
claude plugin install statement-auditor@financial-services

# 可选：安装垂直领域技能包
claude plugin install investment-banking@financial-services
claude plugin install equity-research@financial-services
claude plugin install private-equity@financial-services
claude plugin install wealth-management@financial-services
claude plugin install fund-admin@financial-services
claude plugin install operations@financial-services
```

安装完成后，Agent 会在 Cowork dispatch 中自动出现，Skills 在相关场景自动触发，Slash Commands 可直接调用。

---

### 方式三：Claude Managed Agents API（生产部署）

适用于需要在自己的工作流引擎上运行 Agent 的场景。

```bash
# 1. 克隆仓库
git clone https://github.com/anthropics/financial-services.git
cd financial-services

# 2. 设置 API Key
export ANTHROPIC_API_KEY=sk-ant-...

# 3. 部署 Agent（以 gl-reconciler 为例）
scripts/deploy-managed-agent.sh gl-reconciler
```

部署脚本会自动：
1. 解析 Agent 模板中的文件引用
2. 上传 Skills 到 Managed Agent
3. 创建 leaf-worker 子 Agent
4. 通过 `POST /v1/agents` 注册 orchestrator Agent

每个 Agent 模板位于 `managed-agent-cookbooks/<slug>/`，包含 `agent.yaml` 配置文件、子 Agent 定义和安全指南。

---

## 配置

### 1. 配置数据连接器（MCP）

所有数据连接器集中在 `financial-analysis` 核心插件中。编辑 `.mcp.json`：

```json
{
  "mcpServers": {
    "morningstar": {
      "url": "https://mcp.morningstar.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_MORNINGSTAR_API_KEY"
      }
    },
    "factset": {
      "url": "https://mcp.factset.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_FACTSET_API_KEY"
      }
    },
    "pitchbook": {
      "url": "https://premium.mcp.pitchbook.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_PITCHBOOK_API_KEY"
      }
    }
  }
}
```

**可用的数据连接器：**

| 提供商 | 数据类型 | MCP URL |
|--------|---------|---------|
| Daloopa | 财务数据 | `https://mcp.daloopa.com/server/mcp` |
| Morningstar | 基金/股票数据 | `https://mcp.morningstar.com/mcp` |
| S&P Global (Capital IQ) | 公司基本面 | `https://kfinance.kensho.com/integrations/mcp` |
| FactSet | 多资产数据 | `https://mcp.factset.com/mcp` |
| Moody's | 评级/信用数据 | `https://api.moodys.com/genai-ready-data/m1/mcp` |
| MT Newswires | 实时新闻 | `https://vast-mcp.blueskyapi.com/mtnewswires` |
| Aiera | 事件/路演 | `https://mcp-pub.aiera.com` |
| LSEG | 债券/FX/衍生品 | `https://api.analytics.lseg.com/lfa/mcp` |
| PitchBook | PE/VC/并购数据 | `https://premium.mcp.pitchbook.com/mcp` |
| Chronograph | 基金数据 | `https://ai.chronograph.pe/mcp` |
| Egnyte | 文档存储 | `https://mcp-server.egnyte.com/mcp` |

> ⚠️ MCP 连接可能需要各数据提供商的订阅或 API Key。

### 2. 添加公司上下文

在安装的 Skills 文件中补充你公司的术语、流程和格式标准：

```markdown
# 公司上下文
- 估值方法偏好：DCF 为主，可比公司法为辅助验证
- 财务模型模板路径：\\internal-server\templates\model_v3.xlsx
- 路演材料品牌色：主色 #003366，辅色 #CC9900
```

### 3. 配置 PowerPoint 模板

```bash
# 教 Claude 使用你的品牌 PPT 模板
/ppt-template "\\internal-server\templates\pitch_deck_template.pptx"
```

---

## 使用指南

### Agent 列表与触发方式

| Agent | 功能 | 触发场景 |
|-------|------|---------|
| **Pitch Agent** | 可比分析→先例交易→LBO→品牌路演材料 | 准备客户路演时 |
| **Meeting Prep Agent** | 生成客户会议前的简报包 | 每次客户会议前 |
| **Market Researcher** | 行业概览→竞争格局→对标分析→标的筛选 | 研究新行业或赛道时 |
| **Earnings Reviewer** | 财报电话会+公告→模型更新→研究笔记 | 财报季 |
| **Model Builder** | DCF/LBO/三表模型→Excel 实时建模 | 财务建模 |
| **Valuation Reviewer** | 读取 GP 报告→估值模板→LP 报告 | 基金估值周期 |
| **GL Reconciler** | 发现差异→追溯原因→路由审批 | 月末对账 |
| **Month-End Closer** | 应计项目→滚动前推→差异说明 | 月末结账 |
| **Statement Auditor** | 审计 LP 对账单 | 分发前检查 |
| **KYC Screener** | 解析入驻文档→规则引擎→标记缺失 | 客户入驻 |

### Cowork 中使用

安装插件后，在 Cowork 中直接 @Agent 名称即可调度：

```
@pitch-agent 帮我准备一个针对某SaaS公司的路演材料，包括可比公司分析和LBO模型
```

```
@market-researcher 研究一下东南亚金融科技赛道，我需要行业概览、竞争格局和对标公司
```

### Claude Code 中使用

已安装的 Slash Commands 可在对话中直接调用：

```
/comps AAPL MSFT GOOGL  # 可比公司分析
/dcf TSLA               # DCF 估值
/earnings JPM            # 财报分析
/ic-memo                 # 投资委员会备忘录
```

完整命令列表：
- `/comps` — 可比公司分析
- `/dcf` — 现金流折现估值
- `/earnings` — 财报回顾
- `/ic-memo` — IC 备忘录
- `/lbo` — LBO 模型
- `/cim` — 机密信息备忘录
- `/teaser` — 交易简介
- `/buyer-list` — 买家清单
- `/merger-model` — 合并模型
- `/client-review` — 客户回顾
- `/portfolio-monitor` — 组合监控
- `/nav-tie-out` — 净值核对
- `/kyc-check` — KYC 检查

### Managed Agents API 中使用

部署后通过 API 调用：

```python
import requests

headers = {
    "x-api-key": "sk-ant-...",
    "anthropic-version": "2023-06-01"
}

# 调用已部署的 gl-reconciler
response = requests.post(
    "https://api.anthropic.com/v1/agents/gl-reconciler/run",
    headers=headers,
    json={
        "input": "对账 Q1 总账科目 1001-1099 与银行对账单",
        "context_files": ["gl_extract_q1.csv", "bank_statement_q1.pdf"]
    }
)
```

---

## 自定义与扩展

### 添加新 Skill

1. 在 `plugins/vertical-plugins/<vertical>/skills/` 下创建新 Markdown 文件
2. 运行同步脚本：
```bash
python3 scripts/sync-agent-skills.py
```
此脚本会自动将 Skill 同步到所有引用该垂直领域的 Agent。

### 创建新 Agent

1. 复制最接近的 Agent 目录：
```bash
cp -r plugins/agent-plugins/pitch-agent plugins/agent-plugins/my-new-agent
```
2. 编辑 `agents/<slug>.md`（系统提示词）和 `skills/`（引用需要的 Skills）
3. 创建对应的 Managed Agent 模板：
```bash
cp -r managed-agent-cookbooks/pitch-agent managed-agent-cookbooks/my-new-agent
```
4. 运行检查脚本验证：
```bash
python3 scripts/check.py
```

### 提交前检查

```bash
# 运行完整检查：lint 所有 manifest，验证跨文件引用，检查 skill 同步状态
python3 scripts/check.py
```

---

## Microsoft 365 集成

如果你的公司通过 Microsoft 365 插件在 Excel、PowerPoint、Word 和 Outlook 内使用 Claude：

```bash
# 1. 安装 M365 配置工具
claude plugin install claude-for-msft-365-install@financial-services

# 2. 运行设置向导
/claude-for-msft-365-install:setup
```

向导会引导 IT 管理员：
1. 生成自定义插件 Manifest
2. 授予 Azure Admin Consent
3. 通过 Microsoft Graph 配置每用户路由

---

## 架构理解

```
financial-services/
├── plugins/
│   ├── agent-plugins/           # 命名 Agent（自包含）
│   │   ├── pitch-agent/         #   ├── agents/pitch-agent.md (系统提示词)
│   │   ├── market-researcher/   #   └── skills/ (打包的 Skill 副本)
│   │   ├── gl-reconciler/
│   │   └── ...
│   ├── vertical-plugins/        # 垂直领域 Skill + 命令
│   │   ├── financial-analysis/  #   核心技能 + 所有数据连接器
│   │   ├── investment-banking/
│   │   ├── equity-research/
│   │   └── ...
│   └── partner-built/           # 合作伙伴构建的插件
│       ├── lseg/
│       └── sp-global/
├── managed-agent-cookbooks/     # Managed Agent 部署模板
│   ├── pitch-agent/
│   │   ├── agent.yaml
│   │   └── subagents/
│   └── ...
├── claude-for-msft-365-install/ # M365 集成工具
├── scripts/                     # 部署与运维脚本
│   ├── deploy-managed-agent.sh
│   ├── check.py
│   ├── validate.py
│   ├── orchestrate.py
│   └── sync-agent-skills.py
└── .claude-plugin/              # Cowork 插件市场配置
```

**关键设计原则：**
- Agent = 系统提示词 + 打包的 Skills，自包含，安装即用
- Skill 在垂直插件中"一次编写"，通过 `sync-agent-skills.py` 同步到各 Agent
- Managed Agent 模板与 Cowork 插件共享相同的系统提示词和 Skills
- 所有数据连接器集中在 `financial-analysis` 核心插件，统一管理

---

## 常见问题

### Q: 安装后 Agent 不生效？

运行 `claude plugin list` 检查插件状态。确保先安装了 `financial-analysis` 核心插件，再安装 Agent。

### Q: 数据连接器报认证错误？

检查 `.mcp.json` 中的 API Key 是否正确。MCP 连接需要各数据提供商的独立订阅——Morningstar、FactSet 等不是 Anthropic 提供的。

### Q: 如何在无外网环境中使用？

1. Fork 本仓库到内网 Git
2. 修改 `claude plugin marketplace add` 指向你的内网地址
3. 替换 `.mcp.json` 中的数据连接器指向内网系统

### Q: Agent 输出不符合预期？

- 检查是否添加了公司上下文（术语、流程、格式）
- 使用 `/ppt-template` 教 Claude 你的品牌模板
- 编辑 `agents/<slug>.md` 调整系统提示词

---

## 许可证

Apache License 2.0

## 相关链接

- 官方仓库：https://github.com/anthropics/financial-services
- Claude Cowork 文档：https://docs.anthropic.com/en/docs/claude-cowork
- Managed Agents API 文档：https://docs.anthropic.com/en/api/agents
- MCP 协议规范：https://modelcontextprotocol.io
