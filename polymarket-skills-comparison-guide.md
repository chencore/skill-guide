# Polymarket Skills 全面对比分析

> 4 个 Polymarket Skills 的功能对比、安装指南和使用说明

---

## 📋 目录

1. [概述](#概述)
2. [功能对比矩阵](#功能对比矩阵)
3. [详细对比分析](#详细对比分析)
4. [安装指南](#安装指南)
5. [使用场景推荐](#使用场景推荐)
6. [详细使用说明](#详细使用说明)
7. [优缺点总结](#优缺点总结)
8. [最佳实践](#最佳实践)

---

## 🎯 概述

Polymarket 是全球最大的预测市场平台，ClawHub 上有 4 个主要 skills：

| Skill | 类型 | 主要功能 | 难度 |
|-------|------|---------|------|
| **polymarket-odds** | 查询 | 快速赔率查询 | ⭐ 简单 |
| **polymarket-api** | 查询 | 基础数据获取 | ⭐ 简单 |
| **polymarket-trade** | 查询+模拟 | 监控+纸面交易 | ⭐⭐ 中等 |
| **polymarket-auto-trader** | 交易 | 自动化真实交易 | ⭐⭐⭐⭐ 复杂 |

---

## 📊 功能对比矩阵

### 核心功能

| 功能 | odds | api | trade | auto-trader |
|------|------|-----|-------|-------------|
| 市场搜索 | ✅ | ✅ | ✅ | ✅ |
| 赔率查询 | ✅ | ✅ | ✅ | ✅ |
| 事件浏览 | ✅ | ✅ | ✅ | ✅ |
| 分类浏览 | ✅ | ❌ | ✅ | ✅ |
| 监控列表 | ❌ | ❌ | ✅ | ❌ |
| 价格提醒 | ❌ | ❌ | ✅ | ❌ |
| 动量扫描 | ❌ | ❌ | ✅ | ❌ |
| 结算日历 | ❌ | ❌ | ✅ | ❌ |
| 分类摘要 | ❌ | ❌ | ✅ | ❌ |
| 纸面交易 | ❌ | ❌ | ✅ | ❌ |
| 真实交易 | ❌ | ❌ | ❌ | ✅ |
| 自动化交易 | ❌ | ❌ | ❌ | ✅ |
| LLM 评估 | ❌ | ❌ | ❌ | ✅ |
| Kelly 仓位 | ❌ | ❌ | ❌ | ✅ |

### 技术要求

| 要求 | odds | api | trade | auto-trader |
|------|------|-----|-------|-------------|
| Node.js | ✅ | ❌ | ❌ | ❌ |
| Python | ❌ | ✅ | ✅ | ✅ |
| API Key | ❌ | ❌ | ❌ | ✅ |
| 钱包私钥 | ❌ | ❌ | ❌ | ✅ |
| 非 US VPS | ❌ | ❌ | ❌ | ✅ |
| 区块链批准 | ❌ | ❌ | ❌ | ✅ |

### 数据存储

| 存储位置 | odds | api | trade | auto-trader |
|---------|------|-----|-------|-------------|
| 本地文件 | ❌ | ❌ | ✅ | ✅ |
| JSON 数据 | ❌ | ❌ | ✅ | ✅ |
| 交易记录 | ❌ | ❌ | ✅ | ✅ |

---

## 🔍 详细对比分析

### 1. polymarket-odds

**定位**：轻量级赔率查询工具

**特点**：
- ✅ Node.js 实现，运行速度快
- ✅ 无需任何依赖
- ✅ 支持快速搜索
- ✅ 支持按类别浏览
- ✅ 输出格式清晰

**适用场景**：
- 快速查询某个市场的赔率
- 浏览不同类别的热门市场
- 集成到其他工具中

**不足**：
- ❌ 功能相对简单
- ❌ 没有监控和提醒功能
- ❌ 没有交易功能

**命令示例**：
```bash
node polymarket.mjs search "Bitcoin"
node polymarket.mjs events --tag=crypto
```

---

### 2. polymarket-api

**定位**：基础 API 查询工具

**特点**：
- ✅ Python 实现
- ✅ 代码简单易懂
- ✅ 无需认证
- ✅ 适合学习和定制

**适用场景**：
- 学习 Polymarket API
- 简单的数据获取
- 自定义脚本开发

**不足**：
- ❌ 功能最基础
- ❌ 没有高级功能
- ❌ 没有交易功能

**命令示例**：
```bash
python3 polymarket.py --top
python3 polymarket.py --search "trump"
```

---

### 3. polymarket-trade

**定位**：全功能监控和模拟交易工具

**特点**：
- ✅ 功能最全面
- ✅ 监控列表和价格提醒
- ✅ 动量扫描
- ✅ 结算日历
- ✅ 分类摘要
- ✅ 纸面交易（模拟）
- ✅ 本地数据存储

**适用场景**：
- 日常市场监控
- 价格提醒和通知
- 跟踪预测表现
- 学习和测试策略

**不足**：
- ❌ 不能真实交易
- ❌ 需要定期运行（cron）

**命令示例**：
```bash
python3 polymarket.py trending
python3 polymarket.py watch add trump-2028 --alert-at 60
python3 polymarket.py buy bitcoin-100k 100
python3 polymarket.py portfolio
```

---

### 4. polymarket-auto-trader

**定位**：自动化真实交易机器人

**特点**：
- ✅ 完全自动化
- ✅ LLM 评估概率
- ✅ Kelly 准则仓位管理
- ✅ 真实资金交易
- ✅ 风险控制
- ✅ P&L 跟踪

**适用场景**：
- 自动化交易策略
- 批量市场评估
- 长期运行

**不足**：
- ❌ 复杂度最高
- ❌ 需要 VPS 和钱包
- ❌ 有真实资金风险
- ❌ 需要持续监控

**命令示例**：
```bash
python3 run_trade.py
python3 pnl_tracker.py
python3 approve_contracts.py
```

---

## 🔧 安装指南

### 方法一：ClawHub 批量安装

```bash
# 安装所有 4 个 skills
clawhub install polymarket-odds
clawhub install polymarket-api
clawhub install polymarket-trade
clawhub install polymarket-auto-trader --force  # 需要强制安装
```

### 方法二：指定目录安装

```bash
# 安装到工作空间
clawhub install polymarket-odds --dir ~/.openclaw/workspace-self_media/skills
clawhub install polymarket-api --dir ~/.openclaw/workspace-self_media/skills
clawhub install polymarket-trade --dir ~/.openclaw/workspace-self_media/skills
clawhub install polymarket-auto-trader --dir ~/.openclaw/workspace-self_media/skills --force
```

### 验证安装

```bash
# 查看已安装的 skills
openclaw skills list | grep polymarket

# 检查文件
ls ~/.openclaw/workspace-self_media/skills/ | grep polymarket
```

---

## 🎯 使用场景推荐

### 场景 1：快速查询赔率

**推荐**：`polymarket-odds`

**原因**：
- 命令简单
- 输出清晰
- 运行速度快

**示例**：
```bash
node polymarket.mjs search "Bitcoin 100k"
node polymarket.mjs events --tag=crypto
```

---

### 场景 2：学习 Polymarket API

**推荐**：`polymarket-api`

**原因**：
- 代码简单
- 容易理解和修改
- 适合初学者

**示例**：
```bash
python3 polymarket.py --top
python3 polymarket.py --search "trump"
```

---

### 场景 3：日常监控和跟踪

**推荐**：`polymarket-trade`

**原因**：
- 功能全面
- 监控和提醒
- 纸面交易测试

**示例**：
```bash
# 添加监控
python3 polymarket.py watch add trump-2028 --alert-at 60

# 查看动量
python3 polymarket.py movers

# 纸面交易
python3 polymarket.py buy bitcoin-100k 100
python3 polymarket.py portfolio
```

---

### 场景 4：自动化交易

**推荐**：`polymarket-auto-trader`

**原因**：
- 完全自动化
- 真实资金交易
- LLM 评估

**前提条件**：
- 非 US VPS
- Polygon 钱包
- Anthropic API Key
- USDC.e 和 MATIC

**示例**：
```bash
# 配置环境
cat > /opt/trader/app/.env << EOF
PRIVATE_KEY=你的私钥
LLM_API_KEY=你的API密钥
EOF

# 批准合约
python3 approve_contracts.py

# 运行交易
python3 run_trade.py

# 查看 P&L
python3 pnl_tracker.py
```

---

## 📚 详细使用说明

### polymarket-odds

#### 基本命令

```bash
# 进入目录
cd ~/.openclaw/workspace-self_media/skills/polymarket-odds

# 搜索市场
node polymarket.mjs search "Bitcoin"
node polymarket.mjs search "Trump"
node polymarket.mjs search "Arsenal FC"

# 浏览事件
node polymarket.mjs events
node polymarket.mjs events --tag=crypto
node polymarket.mjs events --tag=politics
node polymarket.mjs events --tag=sports

# 查看市场详情
node polymarket.mjs market will-bitcoin-reach-100k

# 查看类别
node polymarket.mjs tags
```

#### 创建别名

```bash
# 添加到 ~/.zshrc 或 ~/.bashrc
echo 'alias pm-odds="node ~/.openclaw/workspace-self_media/skills/polymarket-odds/polymarket.mjs"' >> ~/.zshrc
source ~/.zshrc

# 使用别名
pm-odds search "Bitcoin"
pm-odds events --tag=crypto
```

---

### polymarket-api

#### 基本命令

```bash
# 进入目录
cd ~/.openclaw/workspace-self_media/skills/polymarket-api/scripts

# 查看热门市场
python3 polymarket.py --top

# 搜索市场
python3 polymarket.py --search "trump"
python3 polymarket.py --search "bitcoin"

# 查看特定市场
python3 polymarket.py --slug "will-trump-win-2024-election"

# 列出事件
python3 polymarket.py --events
```

---

### polymarket-trade

#### 核心命令

```bash
# 进入目录
cd ~/.openclaw/workspace-self_media/skills/polymarket-trade/scripts

# 趋势市场
python3 polymarket.py trending

# 特色市场
python3 polymarket.py featured

# 搜索市场
python3 polymarket.py search "trump"

# 分类浏览
python3 polymarket.py category politics
python3 polymarket.py category crypto
python3 polymarket.py category sports
```

#### 监控和提醒

```bash
# 添加到监控列表
python3 polymarket.py watch add trump-2028

# 添加价格提醒（60%时提醒）
python3 polymarket.py watch add trump-2028 --alert-at 60

# 添加变化提醒（±10%时提醒）
python3 polymarket.py watch add bitcoin-100k --alert-change 10

# 查看监控列表
python3 polymarket.py watch list

# 检查提醒（用于 cron）
python3 polymarket.py alerts
python3 polymarket.py alerts --quiet  # 只输出触发的提醒

# 移除监控
python3 polymarket.py watch remove trump-2028
```

#### 动量和日历

```bash
# 最大动量（24小时）
python3 polymarket.py movers

# 周动量
python3 polymarket.py movers --timeframe 1w

# 月动量
python3 polymarket.py movers --timeframe 1m --min-volume 50

# 结算日历（7天内）
python3 polymarket.py calendar

# 结算日历（3天内）
python3 polymarket.py calendar --days 3

# 更多结果
python3 polymarket.py calendar --days 14 --limit 20
```

#### 分类摘要

```bash
# 政治摘要
python3 polymarket.py digest politics

# 加密货币摘要
python3 polymarket.py digest crypto

# 体育摘要
python3 polymarket.py digest sports

# 科技摘要
python3 polymarket.py digest tech

# 商业摘要
python3 polymarket.py digest business
```

#### 纸面交易

```bash
# 买入 $100
python3 polymarket.py buy trump-2028 100

# 买入特定结果
python3 polymarket.py buy giannis-trade 50 --outcome warriors

# 查看投资组合
python3 polymarket.py portfolio

# 卖出
python3 polymarket.py sell trump-2028
```

#### 定时任务

```bash
# 编辑 crontab
crontab -e

# 每小时检查提醒
0 * * * * cd ~/.openclaw/workspace-self_media/skills/polymarket-trade/scripts && python3 polymarket.py alerts --quiet

# 每天早上 7 点简报
0 7 * * * cd ~/.openclaw/workspace-self_media/skills/polymarket-trade/scripts && python3 polymarket.py movers && python3 polymarket.py calendar --days 1

# 每周日上午 10 点摘要
0 10 * * 0 cd ~/.openclaw/workspace-self_media/skills/polymarket-trade/scripts && python3 polymarket.py digest politics
0 10 * * 0 cd ~/.openclaw/workspace-self_media/skills/polymarket-trade/scripts && python3 polymarket.py digest crypto
```

---

### polymarket-auto-trader

#### VPS 环境准备

```bash
# SSH 登录到非 US VPS
ssh user@your-vps-ip

# 安装依赖
apt update && apt install -y python3 python3-venv

# 创建虚拟环境
python3 -m venv /opt/trader

# 安装 Python 包
/opt/trader/bin/pip install py-clob-client python-dotenv web3 requests
```

#### 配置环境

```bash
# 创建配置目录
mkdir -p /opt/trader/app

# 创建 .env 文件
cat > /opt/trader/app/.env << EOF
PRIVATE_KEY=你的Polygon钱包私钥
LLM_API_KEY=你的Anthropic API密钥
EOF

# 设置权限
chmod 600 /opt/trader/app/.env
```

#### 批准合约

```bash
# 复制脚本到 VPS
cd ~/.openclaw/workspace-self_media/skills/polymarket-auto-trader/scripts
scp approve_contracts.py user@your-vps-ip:/opt/trader/app/

# 运行批准脚本
cd /opt/trader/app
/opt/trader/bin/python3 approve_contracts.py
```

#### 部署和运行

```bash
# 复制脚本
cp ~/.openclaw/workspace-self_media/skills/polymarket-auto-trader/scripts/run_trade.py /opt/trader/app/
cp ~/.openclaw/workspace-self_media/skills/polymarket-auto-trader/scripts/pnl_tracker.py /opt/trader/app/

# 测试运行
cd /opt/trader/app
/opt/trader/bin/python3 run_trade.py

# 查看 P&L
/opt/trader/bin/python3 pnl_tracker.py

# 查看日志
tail -50 /opt/trader/app/cron.log
```

#### 设置定时任务

```bash
# 编辑 crontab
crontab -e

# 每 10 分钟运行一次
*/10 * * * * cd /opt/trader/app && /opt/trader/bin/python3 run_trade.py >> cron.log 2>&1
```

---

## ⚖️ 优缺点总结

### polymarket-odds

| 优点 | 缺点 |
|------|------|
| ✅ 轻量快速 | ❌ 功能简单 |
| ✅ 无需依赖 | ❌ 无监控功能 |
| ✅ 输出清晰 | ❌ 无交易功能 |
| ✅ 易于集成 | ❌ |

**适合**：快速查询、集成到其他工具

---

### polymarket-api

| 优点 | 缺点 |
|------|------|
| ✅ 代码简单 | ❌ 功能最基础 |
| ✅ 易于理解 | ❌ 无高级功能 |
| ✅ 适合学习 | ❌ 无交易功能 |
| ✅ | ❌ |

**适合**：学习 API、简单数据获取

---

### polymarket-trade

| 优点 | 缺点 |
|------|------|
| ✅ 功能最全面 | ❌ 不能真实交易 |
| ✅ 监控和提醒 | ❌ 需要定期运行 |
| ✅ 动量扫描 | ❌ |
| ✅ 纸面交易 | ❌ |
| ✅ 本地存储 | ❌ |

**适合**：日常监控、学习测试、跟踪表现

---

### polymarket-auto-trader

| 优点 | 缺点 |
|------|------|
| ✅ 完全自动化 | ❌ 复杂度最高 |
| ✅ 真实资金交易 | ❌ 需要 VPS 和钱包 |
| ✅ LLM 评估 | ❌ 有真实资金风险 |
| ✅ Kelly 仓位管理 | ❌ 需要持续监控 |
| ✅ P&L 跟踪 | ❌ |

**适合**：自动化交易、批量评估、长期运行

---

## 🌟 最佳实践

### 1. 渐进式使用

**步骤 1**：使用 `polymarket-odds` 熟悉市场
```bash
pm-odds search "Bitcoin"
pm-odds events --tag=crypto
```

**步骤 2**：使用 `polymarket-trade` 建立监控
```bash
python3 polymarket.py watch add bitcoin-100k --alert-at 70
python3 polymarket.py alerts
```

**步骤 3**：使用 `polymarket-trade` 纸面交易测试
```bash
python3 polymarket.py buy bitcoin-100k 100
python3 polymarket.py portfolio
```

**步骤 4**：使用 `polymarket-auto-trader` 真实交易（谨慎）
```bash
python3 run_trade.py
python3 pnl_tracker.py
```

---

### 2. 组合使用

**日常监控**：
```bash
# 使用 polymarket-trade 的监控和提醒
python3 polymarket.py watch add trump-2028 --alert-at 60
python3 polymarket.py alerts --quiet

# 使用 polymarket-odds 快速查询
pm-odds search "Trump"
```

**研究分析**：
```bash
# 使用 polymarket-trade 的动量扫描
python3 polymarket.py movers

# 使用 polymarket-api 深入查询
python3 polymarket.py --search "bitcoin"
```

---

### 3. 定时任务组合

```bash
# crontab 示例
# 每小时检查提醒
0 * * * * cd /path/to/polymarket-trade/scripts && python3 polymarket.py alerts --quiet

# 每天早上 7 点简报
0 7 * * * cd /path/to/polymarket-trade/scripts && python3 polymarket.py movers && python3 polymarket.py calendar --days 1

# 每 10 分钟自动交易（VPS 上）
*/10 * * * * cd /opt/trader/app && /opt/trader/bin/python3 run_trade.py >> cron.log 2>&1
```

---

### 4. 安全建议

**polymarket-odds / polymarket-api / polymarket-trade**：
- ✅ 无安全风险
- ✅ 不涉及真实资金
- ✅ 可以随意使用

**polymarket-auto-trader**：
- ⚠️ 使用专用钱包
- ⚠️ 设置严格权限
- ⚠️ 小额测试
- ⚠️ 持续监控
- ⚠️ 理解风险

---

## 📊 决策树

```
需要什么？
│
├─ 快速查询赔率
│  └─ polymarket-odds ⭐
│
├─ 学习 API
│  └─ polymarket-api ⭐
│
├─ 日常监控和跟踪
│  └─ polymarket-trade ⭐⭐
│
├─ 纸面交易测试
│  └─ polymarket-trade ⭐⭐
│
└─ 自动化真实交易
   └─ polymarket-auto-trader ⭐⭐⭐⭐
      （需要 VPS、钱包、API Key）
```

---

## 🎯 快速开始清单

### 新手入门

```bash
# 1. 安装最简单的 skill
clawhub install polymarket-odds

# 2. 创建别名
echo 'alias pm-odds="node ~/.openclaw/skills/polymarket-odds/polymarket.mjs"' >> ~/.zshrc
source ~/.zshrc

# 3. 测试查询
pm-odds search "Bitcoin"
pm-odds events --tag=crypto
```

### 日常使用

```bash
# 1. 安装功能全面的 skill
clawhub install polymarket-trade

# 2. 添加监控
cd ~/.openclaw/skills/polymarket-trade/scripts
python3 polymarket.py watch add bitcoin-100k --alert-at 70

# 3. 设置定时任务
crontab -e
# 添加: 0 * * * * cd /path && python3 polymarket.py alerts --quiet
```

### 高级交易

```bash
# 1. 准备非 US VPS
# 2. 安装 skill
clawhub install polymarket-auto-trader --force

# 3. 配置环境
mkdir -p /opt/trader/app
cat > /opt/trader/app/.env << EOF
PRIVATE_KEY=你的私钥
LLM_API_KEY=你的API密钥
EOF
chmod 600 /opt/trader/app/.env

# 4. 批准合约
python3 approve_contracts.py

# 5. 测试运行
python3 run_trade.py

# 6. 设置定时任务
crontab -e
# 添加: */10 * * * * cd /opt/trader/app && python3 run_trade.py >> cron.log 2>&1
```

---

## 📚 官方资源

- **Polymarket 官网**：https://polymarket.com
- **Gamma API**：https://gamma-api.polymarket.com
- **CLOB API**：https://clob.polymarket.com
- **ClawHub**：https://clawhub.ai
- **文档**：https://docs.polymarket.com

---

## 🆘 常见问题

### Q1: 应该先安装哪个？

**A**: 建议顺序：
1. polymarket-odds（最简单）
2. polymarket-trade（功能全）
3. polymarket-auto-trader（最复杂，需要准备）

### Q2: 哪个适合自动化？

**A**: 
- 监控自动化：`polymarket-trade`（cron）
- 交易自动化：`polymarket-auto-trader`（需要 VPS）

### Q3: 需要真实资金吗？

**A**:
- polymarket-odds/api/trade：不需要
- polymarket-auto-trader：需要（USDC.e + MATIC）

### Q4: 可以同时使用多个吗？

**A**: 可以！推荐组合：
- odds（快速查询）+ trade（监控）+ auto-trader（交易）

### Q5: 哪个最安全？

**A**: 
- 最安全：polymarket-odds/api/trade（无真实资金）
- 有风险：polymarket-auto-trader（真实资金）

---

## 🎯 总结

| Skill | 评分 | 推荐度 |
|-------|------|--------|
| polymarket-odds | ⭐⭐⭐⭐⭐ | 新手必装 |
| polymarket-api | ⭐⭐⭐ | 学习可选 |
| polymarket-trade | ⭐⭐⭐⭐⭐ | 日常必装 |
| polymarket-auto-trader | ⭐⭐⭐⭐ | 高级可选 |

**推荐组合**：
- 新手：`polymarket-odds` + `polymarket-trade`
- 进阶：`polymarket-odds` + `polymarket-trade` + `polymarket-auto-trader`

---

*最后更新：2026-03-29*
*作者：马龙*
*版本：1.0*
