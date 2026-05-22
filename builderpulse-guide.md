# BuilderPulse 安装与使用指南

> AI 驱动的独立开发者每日情报工具

## 项目简介

BuilderPulse 是一个面向独立开发者的 AI 驱动每日情报工具。

每天自动采集 10+ 数据源的 300+ 信号，生成一份"今天值得做什么"的报告。

---

## 安装

### 方式一：直接 Clone

```bash
git clone https://github.com/BuilderPulse/BuilderPulse.git
cd BuilderPulse
```

### 方式二：GitHub Codespaces（免安装）

点击项目首页 **Code** → **Codespaces** → **Create codespace on main**

浏览器里直接打开，无需本地配置。

---

## 使用方法

### 阅读每日报告

报告按语言分目录：

```
BuilderPulse/
├── en/2026/          # 英文报告
│   └── 2026-04-16.md
└── zh/2026/          # 中文报告
    └── 2026-04-16.md
```

直接在 GitHub 上打开对应日期的 `.md` 文件，或 clone 后用任意 Markdown 阅读器打开。

### 报告结构

每份报告包含：

- **今日三大发现** — 当天最重要的趋势
- **💡 今日选题** — "如果你有 2 小时，做这个"
- **Discovery** — 新上线的产品/项目
- **搜索趋势异常** — 暴涨的搜索词
- **快增长开源项目** — GitHub 上快速上升的项目
- **开发者抱怨** — 现有工具的痛点

### 订阅每日更新

- **GitHub Watch**：Star + Watch 该项目，上游更新自动通知
- **RSS 订阅**：项目右上角有 RSS feed 入口

---

## 目录结构

```
BuilderPulse/
├── README.md          # 项目首页
├── en/                # 英文报告
│   └── 2026/
│       ├── 2026-04-16.md
│       ├── 2026-04-15.md
│       └── ...
└── zh/                # 中文报告
    └── 2026/
        └── ...
```

---

## 数据来源

| 数据源 | 内容 |
|--------|------|
| Hacker News | 技术趋势、创始故事、热议话题 |
| GitHub Trending | 快速上升的开源项目 |
| Product Hunt | 新上线产品 |
| HuggingFace | AI/ML 模型热度 |
| Google Trends | 搜索量异常波动 |
| Reddit | 开发者社区真实反馈 |

---

## 今日报告速览（2026-04-16）

**今日三大发现：**
1. Google 数据门引爆自托管浪潮（vaultwarden、Gitea、Joplin 搜索量暴涨）
2. AI Agent 基础设施成新平台战（Claude managed agents 搜索 +950%）
3. Cal.com 闭源引发开源许可论战

**今日选题：**
> 如果你有 2 小时，做一个面向小型代理机构的开源社交媒体多平台排期发布工具（现有方案月付 $400）

---

## 常见问题

**Q: 需要 API Key 吗？**
A: 不需要。报告由项目维护者每日自动生成，你只需要阅读。

**Q: 有中文版吗？**
A: 有。每份报告都有 `en/` 英文版和 `zh/` 中文版两个版本。

**Q: 如何第一时间看到新报告？**
A: GitHub 上 Star + Watch 该项目，或使用 RSS 订阅。

**Q: 可以自定义数据源吗？**
A: 目前不支持。这是维护者统一生成的内容，不支持自定义。

---

## 相关资源

- 项目主页：https://github.com/BuilderPulse/BuilderPulse
- 维护者：[@bourneliu66](https://x.com/bourneliu66)

---

*本指南由 马龙 🏓 整理 | 2026-04-16*
