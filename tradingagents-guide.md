# TradingAgents 安装与使用指南

> 多代理 LLM 金融交易框架 — 51,442 Stars

## 项目简介

TradingAgents 是一个多代理量化交易框架，模拟真实交易公司的运作模式。

通过部署专业 LLM 驱动的 Agent 团队：基本面分析师、情绪分析师、新闻分析师、技术分析师、交易员、风险管理团队，协同评估市场状况并给出交易决策。Agent 之间还会进行动态讨论来确定最优策略。

核心团队构成：

- **分析师团队**：基本面 / 情绪 / 新闻 / 技术分析
- **研究员团队**：多头 + 空头研究员，评估分析师观点并辩论
- **交易员**：综合报告做出交易决策
- **风控 + 组合经理**：评估风险，批准或否决交易提案

---

## 系统要求

| 配置项 | 最低要求 | 推荐 |
|--------|---------|------|
| Python | 3.10+ | 3.13 |
| 内存 | 8GB | 16GB+ |
| 磁盘 | 10GB | 20GB+ |
| 网络 | 稳定 | 专线 |

---

## 安装

### 方式一：Git Clone（推荐）

```bash
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents
```

### 方式二：pip 直接安装

```bash
pip install tradingagents
```

---

## 环境配置

### 创建虚拟环境

```bash
# 使用 conda
conda create -n tradingagents python=3.13
conda activate tradingagents

# 或使用 venv
python -m venv tradingagents
source tradingagents/bin/activate  # Linux/macOS
# Windows: tradingagents\Scripts\activate
```

### 安装依赖

```bash
pip install .
```

### Docker 方式（可选）

```bash
cp .env.example .env  # 填入你的 API Key
docker compose run --rm tradingagents
```

如需本地模型（Ollama）：

```bash
docker compose --profile ollama run --rm tradingagents-ollama
```

---

## API 配置

TradingAgents 支持多种 LLM 提供商，只需配置其中一种：

```bash
# OpenAI（GPT 系列）
export OPENAI_API_KEY=sk-...

# Google（Gemini 系列）
export GOOGLE_API_KEY=...

# Anthropic（Claude 系列）
export ANTHROPIC_API_KEY=...

# xAI（Grok 系列）
export XAI_API_KEY=...

# DeepSeek
export DEEPSEEK_API_KEY=...

# 阿里 Qwen（DashScope）
export DASHSCOPE_API_KEY=...

# 智谱 GLM
export ZHIPU_API_KEY=...

# OpenRouter
export OPENROUTER_API_KEY=...

# Alpha Vantage（市场数据）
export ALPHA_VANTAGE_API_KEY=...
```

或复制环境文件后编辑：

```bash
cp .env.example .env
# 然后用文本编辑器打开 .env 填入 API Key
```

### 企业级配置

如使用 Azure OpenAI 或 AWS Bedrock：

```bash
cp .env.enterprise.example .env.enterprise
# 编辑 .env.enterprise 填入凭证
```

### 本地模型（Ollama）

在配置文件中设置：

```python
config["llm_provider"] = "ollama"
```

---

## 快速开始

### CLI 交互模式

```bash
tradingagents
# 或直接运行源码
python -m cli.main
```

启动后会显示交互界面，可选择：

- 股票代码（如 NVDA、AAPL）
- 分析日期
- LLM 提供商
- 研究深度
- 辩论轮次

Agent 运行时会实时展示分析进度：

1. **分析师阶段** — 各分析师并行工作
2. **研究员阶段** — 多空辩论
3. **交易员阶段** — 综合决策
4. **风控阶段** — 风险评估 + 组合经理审批

### Python 代码调用

```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

ta = TradingAgentsGraph(debug=True, config=DEFAULT_CONFIG.copy())

# 传入股票代码和分析日期
_, decision = ta.propagate("NVDA", "2026-01-15")
print(decision)
```

### 自定义配置

```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

config = DEFAULT_CONFIG.copy()
config["llm_provider"] = "openai"              # LLM 提供商
config["deep_think_llm"] = "gpt-5.4"           # 复杂推理模型
config["quick_think_llm"] = "gpt-5.4-mini"    # 快速任务模型
config["max_debate_rounds"] = 2                 # 辩论轮次

ta = TradingAgentsGraph(debug=True, config=config)
_, decision = ta.propagate("NVDA", "2026-01-15")
print(decision)
```

完整配置选项请查看 `tradingagents/default_config.py`

---

## 支持的模型

| 提供商 | 模型示例 |
|--------|---------|
| OpenAI | GPT-5.4, GPT-5.4-mini |
| Google | Gemini 3.1, Gemini 3.5 |
| Anthropic | Claude 4.6, Claude 4 |
| xAI | Grok 4 |
| DeepSeek | DeepSeek 系列 |
| Qwen | 通义千问系列 |
| GLM | 智谱 GLM-4 |
| OpenRouter | 聚合多模型 |
| Ollama | 本地模型 |

---

## 版本历史

| 版本 | 时间 | 更新内容 |
|------|------|---------|
| v0.2.3 | 2026-03 | 多语言支持、GPT-5.4 系列、统一模型目录、回测日期保真、代理支持 |
| v0.2.2 | 2026-03 | GPT-5.4/Gemini 3.1/Claude 4.6、五级评分、OpenAI Responses API、Anthropic 努力控制 |
| v0.2.0 | 2026-02 | 多提供商 LLM 支持（GPT-5.x, Gemini 3.x, Claude 4.x, Grok 4.x） |
| v0.1.0 | 2024-12 | 首次开源发布 |

---

## 框架架构

```
输入股票代码 + 日期
       ↓
  ┌─────────────────┐
  │   分析团队       │
  │ 基本面 / 情绪    │
  │ 新闻 / 技术分析  │
  └────────┬────────┘
           ↓
  ┌─────────────────┐
  │   研究员团队      │
  │  多头 + 空头辩论  │
  └────────┬────────┘
           ↓
  ┌─────────────────┐
  │   交易员 Agent   │
  │  综合决策 + 提案 │
  └────────┬────────┘
           ↓
  ┌─────────────────┐
  │  风控 + 组合经理  │
  │  审批 / 执行订单  │
  └─────────────────┘
           ↓
       输出决策
```

---

## 注意事项

⚠️ **重要声明**：TradingAgents 框架仅用于研究目的。交易表现会因语言模型选择、温度参数、交易周期、数据质量等因素而存在差异。本项目**不构成金融、投资或交易建议**。

---

## 相关资源

| 资源 | 链接 |
|------|------|
| GitHub 仓库 | https://github.com/TauricResearch/TradingAgents |
| 论文（arXiv） | https://arxiv.org/abs/2412.20138 |
| 官方 Discord | https://discord.com/invite/hk9PGKShPK |
| X（Twitter） | https://x.com/TauricResearch |
| 官方社区 | https://tauric.ai/ |
| 演示视频 | https://www.youtube.com/watch?v=90gr5lwjIho |

---

## 引用

如果你使用了本项目，请引用：

```
@misc{xiao2025tradingagentsmultiagentsllmfinancial,
      title={TradingAgents: Multi-Agents LLM Financial Trading Framework},
      author={Yijia Xiao and Edward Sun and Di Luo and Wei Wang},
      year={2025},
      eprint={2412.20138},
      archivePrefix={arXiv},
      primaryClass={q-fin.TR},
      url={https://arxiv.org/abs/2412.20138},
}
```

---

*本指南由 马龙 🏓 整理 | 2026-04-19*
