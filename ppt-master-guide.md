# PPT Master 安装与使用指南

> AI 生成原生可编辑 PPTX — 从任意文档直接生成 PowerPoint，无需设计技能

## 项目简介

PPT Master 是一个开源 AI PPT 生成工具，5,780 Stars，670 Forks。

核心特点：**输出的不是图片，是真实的 PowerPoint 形状**。每个元素都可以直接点击编辑，不是截图，不是网页快照，是真正可以在 PowerPoint 里修改的原生格式。

创始人 Hugo He 是一位金融从业者（CPA · CPV · 咨询工程师），因为每天要审阅和编辑大量 PPT，受不了市面上 AI 工具只能导出图片的问题，做了这个项目。

---

## 核心特性

- **原生可编辑** — 每个元素都是真实的 PowerPoint 形状，可点击修改
- **极低成本** — 工具完全免费，仅需自己的 AI 编辑器费用，低至 **$0.08/份**
- **数据本地化** — 文件无需上传到第三方服务器，整个处理流程在本地运行
- **无平台锁定** — 支持 Claude Code、Cursor、VS Code Copilot 等多种 AI 工具
- **多格式支持** — PDF、DOCX、URL、Markdown、纯文本均可作为输入

---

## 前置要求

| 依赖 | 是否必须 | 说明 |
|------|---------|------|
| **Python 3.10+** | ✅ 必须 | 唯一的必需安装项 |
| Node.js 18+ | ❌ 仅特殊场景 | 仅在导入微信公众号文章且 curl_cffi 无预编译包时才需要 |
| Pandoc | ❌ 仅特殊场景 | 仅在处理 .doc/.odt/.rtf/.tex 等老旧格式时才需要 |

---

## 安装

### 方式一：下载 ZIP（推荐新手）

1. 进入 [GitHub 仓库](https://github.com/hugohe3/ppt-master)
2. 点击 **Code → Download ZIP**
3. 解压到本地任意目录

### 方式二：Git Clone

```bash
git clone https://github.com/hugohe3/ppt-master.git
cd ppt-master
```

### 安装依赖

```bash
pip install -r requirements.txt
```

### Windows 用户注意事项

Windows 需要额外配置 PATH，详见：[Windows 安装指南](./docs/windows-installation.md)

快速步骤：下载 Python 时**务必勾选 "Add to PATH"** → 安装完成后运行 `pip install -r requirements.txt`

### 更新（仅限 Git Clone 方式）

```bash
python3 skills/ppt-master/scripts/update_repo.py
```

---

## AI 编辑器配置

| 工具 | 推荐度 | 说明 |
|------|:------:|------|
| **Claude Code** | ⭐⭐⭐ | 效果最佳，Opus 原生支持，上下文最大 |
| Cursor / VS Code + Copilot | ⭐⭐ | 不错的替代方案 |
| Codebuddy IDE | ⭐⭐ | 对 Kimi、MiniMax 等国产模型支持较好 |

### 配置 AI 图像生成（可选）

```bash
cp .env.example .env
# 然后编辑 .env 文件
```

```env
IMAGE_BACKEND=gemini                        # 必须设置
GEMINI_API_KEY=your-api-key
GEMINI_MODEL=gemini-3.1-flash-image-preview
```

支持的后端：gemini · openai · qwen · zhipu · volcengine · stability · ideogram · siliconflow · fal · replicate

查看所有后端：`python3 skills/ppt-master/scripts/image_gen.py --list-backends`

---

## 使用方法

### 方式一：提供源文件（推荐）

将 PDF、DOCX、图片等源文件放入 `projects/` 目录，然后在 AI 对话中指定：

```
You: Please create a PPT from projects/q3-report/sources/report.pdf
```

AI 会自动分析内容、设计布局、生成 SVG、导出 PPTX。

### 方式二：直接粘贴内容

将文本内容直接粘贴到 AI 对话窗口：

```
You: Please turn the following into a PPT: [在这里粘贴内容...]
```

AI 会直接根据文本内容生成演示文稿。

### 设计确认流程

AI 会先确认设计规范：

```
AI: Let's confirm the design spec:
     [模板] B) Free design
     [格式]   PPT 16:9
     [页数]    8-10 页
     ...
```

确认后 AI 自动完成全部流程：内容分析 → 视觉设计 → SVG 生成 → PPTX 导出。

### 输出文件

生成完成后，两个文件保存在 `exports/` 目录：
- **原生可编辑版** `.pptx` — 可直接在 PowerPoint 中编辑
- **SVG 快照版** `_svg.pptx` — 用于视觉参考（需 Office 2016+）

---

## 目录结构

```
ppt-master/
├── skills/
│   └── ppt-master/
│       ├── SKILL.md          # AI 技能定义文件
│       └── scripts/           # 辅助脚本
│           ├── update_repo.py  # 更新脚本
│           └── image_gen.py   # 图像生成脚本
├── projects/                  # 放置源文件
├── exports/                   # 输出目录
├── examples/                  # 15个项目，229页示例
├── docs/                      # 文档
│   ├── faq.md                 # 常见问题
│   ├── why-ppt-master.md      # 为什么选 PPT Master
│   └── windows-installation.md # Windows 安装指南
├── requirements.txt
└── .env.example
```

---

## 常见问题

**Q: 生成的 PPT 无法编辑？**
A: 确保使用的是原生可编辑版（无 `_svg` 后缀），而非 SVG 快照版。

**Q: AI 丢失上下文怎么办？**
A: 让 AI 读取 `skills/ppt-master/SKILL.md` 文件即可恢复上下文。

**Q: 出现报错？**
A: 先查阅 [FAQ 文档](./docs/faq.md)，涵盖模型选择、布局问题、导出问题等。

**Q: 想用国产大模型生成图片？**
A: 在 `.env` 中设置对应后端（如 qwen、zhipu），并配置相应的 API Key。

---

## 设计风格示例

| 风格 | 说明 |
|------|------|
| Magazine | 温暖自然色调，照片丰富布局 |
| Academic | 结构化研究格式，数据驱动 |
| Dark Art | 电影感深色背景，画廊美学 |
| Nature Documentary | 沉浸式摄影，最小化 UI |
| Tech / SaaS | 简洁白色卡片，定价表布局 |
| Product Launch | 高对比度，醒目规格展示 |

---

## 相关资源

| 资源 | 链接 |
|------|------|
| GitHub 仓库 | https://github.com/hugohe3/ppt-master |
| 官方演示 | https://hugohe3.github.io/ppt-master/ |
| 示例项目 | [`examples/`](./examples/) — 15个项目，229页 |
| Windows 安装指南 | [`docs/windows-installation.md`](./docs/windows-installation.md) |
| 常见问题 | [`docs/faq.md`](./docs/faq.md) |
| 作者官网 | https://www.hehugo.com/ |

---

*本指南由 马龙 🏓 整理 | 2026-04-18*
