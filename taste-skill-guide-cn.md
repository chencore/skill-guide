# Taste Skill · 中文使用文档

> 让 AI 停止生成无聊平响内容的「品味提升」技能包
> GitHub: [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) · ⭐ 21K+ Stars

---

## 目录

- [这是什么](#这是什么)
- [为什么需要它](#为什么需要它)
- [支持的工具](#支持的工具)
- [安装](#安装)
- [Skills 一览](#skills-一览)
- [我该用哪个](#我该用哪个)
- [核心概念：三档旋钮](#核心概念三档旋钮)
- [实战示例](#实战示例)
- [常见问题](#常见问题)
- [更新日志](#更新日志)

---

## 这是什么

Taste Skill 是一个**AI 前端品味调教包**。

它提供 10+ 个可移植的技能文件（SKILL.md），安装到 AI 编程工具后，让 AI 生成的界面不再千篇一律「模板脸」，而是具备真正的：视觉层次、动效节奏、版面密度。

**解决的问题**：AI 编程工具写代码功能没问题，但全是一个味道——Hero 大图→Feature 三栏→Testimonial 轮播→CTA 按钮，每家公司都一样。

---

## 为什么需要它

AI 编程工具爆发之后，大家发现新问题：

```
AI 写界面 → 全员撞脸 → 每个落地页都是同一套模板
```

Taste Skill 踩中了这个痛点——从「让 AI 会做事」进化到「让 AI 做得好看」。

核心思路：不给 AI 喂审美理论，而是给 AI **具体的代码约束**——GSAP 动效参数、字号梯度表、间距比例、em-dash 禁用清单。AI 不需要理解什么是好品味，它只需要在约束下生成。

---

## 支持的工具

| 工具 | 支持状态 |
|------|----------|
| Claude Code | ✅ 支持 |
| Cursor | ✅ 支持 |
| GitHub Copilot (Codex) | ✅ 支持 |
| ChatGPT / GPT-4o | ✅ 支持（通过粘贴 SKILL.md） |

---

## 安装

### 方式一：npx skills（推荐）

Vercel Agent Skills CLI 会自动扫描 `skills/` 目录，一次安装所有技能：

```bash
npx skills add https://github.com/Leonxlnx/taste-skill
```

安装单个技能（通过 install name）：

```bash
# 安装默认的 taste-skill（design-taste-frontend v2）
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"

# 安装 GPT/Codex 专用严格版
npx skills add https://github.com/Leonxlnx/taste-skill --skill "gpt-taste"

# 安装极简风格
npx skills add https://github.com/Leonxlnx/taste-skill --skill "minimalist-ui"

# 安装工业风
npx skills add https://github.com/Leonxlnx/taste-skill --skill "industrial-brutalist-ui"
```

### 方式二：复制 SKILL.md

直接复制 `skills/` 目录下任意 `SKILL.md` 文件内容，粘贴到 AI 工具的对话中即可。

### 方式三：图片转代码工作流

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "image-to-code"
```

然后在提示词中说明：`follow the skill: generate images, then analyze, then code`

---

## Skills 一览

### 代码类技能

| Skill（文件夹） | Install Name | 说明 |
|----------------|--------------|------|
| **taste-skill** | `design-taste-frontend` | 🆕 **v2（实验）** 默认技能包substantially rewrite，从 brief 推断设计语言，调整三个旋钮（VARIANCE / MOTION / DENSITY） |
| **taste-skill-v1** | `design-taste-frontend-v1` | 原始 v1，保留给依赖精确行为的项目 |
| **gpt-tasteskill** | `gpt-taste` | GPT/Codex 专用严格版，更高版面变化，更强 GSAP 指导，强力 anti-slop |
| **image-to-code-skill** | `image-to-code` | 图片优先流程：生成参考图→分析→实现前端 |
| **redesign-skill** | `redesign-existing-projects` | 改进现有项目：先审计 UI，再修复布局/间距/层级/样式 |
| **soft-skill** | `high-end-visual-design` | 精致、安静、高端感 UI，柔和对比度、大量留白、高档字体、spring 动效 |
| **minimalist-skill** | `minimalist-ui` | 编辑产品 UI 风格（Notion/Linear 感），克制色板、清晰结构 |
| **brutalist-skill** | `industrial-brutalist-ui` | 工业瑞士风格：Swiss 字体、锐利对比度、实验性布局 |
| **output-skill** | `full-output-enforcement` | 防止 AI 输出半成品：强制完整输出，禁止占位注释 |
| **stitch-skill** | `stitch-design-taste` | Google Stitch 兼容规则，可选导出 `DESIGN.md` 格式 |

### 图片生成类技能

| Skill（文件夹） | Install Name | 说明 |
|----------------|--------------|------|
| **imagegen-frontend-web** | `imagegen-frontend-web` | 网站设计稿：hero、landing、多区块强字体设计 |
| **imagegen-frontend-mobile** | `imagegen-frontend-mobile` | 移动端屏幕和流程：iOS/Android、mockup、高可读性字体 |
| **brandkit** | `brandkit` | 品牌工具包：logo 方向、色彩板、字体、跨品类身份应用 |

---

## 我该用哪个

| 场景 | 推荐 |
|------|------|
| 不知道用什么，先试试 | `design-taste-frontend`（默认 v2） |
| 需要更严格的 GPT/Codex 规则 | `gpt-taste` |
| 想从一张图片生成完整网站 | `image-to-code` |
| 想改进已有的项目 | `redesign-existing-projects` |
| 已经确定视觉方向（极简/高端/工业） | `soft-skill` / `minimalist-skill` / `brutalist-skill` |
| AI 总输出半成品/截断 | `output-skill` |
| 需要生成设计图再转代码 | `imagegen-frontend-web` / `imagegen-frontend-mobile` |

---

## 核心概念：三档旋钮

taste-skill（v2）的核心是三个可调节的参数，位于文件顶部，数值范围 1-10：

### DESIGN_VARIANCE · 版面变化度

- **低（1-3）**：居中、干净、传统
- **高（7-10）**：不对称、现代、实验性

### MOTION_INTENSITY · 动效强度

- **低（1-3）**：悬停效果为主
- **高（7-10）**：滚动动效、磁吸效果

### VISUAL_DENSITY · 视觉密度

- **低（1-3）**：大量留白，信息稀疏
- **高（7-10）**：密集仪表盘风格

---

## 实战示例

### 示例一：用 soft-skill 做高端感官网

```
在 Cursor 中安装 high-end-visual-design skill，
然后输入提示词：

"用 Next.js + Tailwind 做我们公司的首页，
用 soft-skill 的设计语言：柔和对比度、大量留白、
spring 动效、高档字体（建议 Inter 或 Plus Jakarta Sans）"
```

### 示例二：用 image-to-code 从设计稿生成页面

1. 用 `imagegen-frontend-web` skill 生成一张首页设计图
2. 将设计图交给 AI 分析（`image-to-code-skill`）
3. AI 提取设计要素（颜色、字体、布局）
4. AI 用提取的要素实现完整前端代码

### 示例三：用 redesign-skill 修复现有项目

```
安装 redesign-existing-projects skill，然后：

"审计当前项目的 UI，先指出布局/间距/层级/样式问题，
然后逐一修复，重点提升视觉层次和动效设计"
```

---

## 常见问题

**Q: 这个和普通的设计规范有什么不同？**

A: 普通设计规范是给人看的文档。Taste Skill 是给 AI 看的指令集——直接告诉 AI 代码该怎么写，而不是「请遵循设计原则」。

**Q: 支持 React / Vue / Svelte 吗？**

A: 支持。规则针对的是设计意图，不绑定特定框架 API。

**Q: 安装后 AI 一定会遵循吗？**

A: Skill 文件会被 AI 作为上下文输入，遵守程度取决于底层模型的指令遵循能力。在 Claude Code / Cursor/Codex 上效果最好。

**Q: v1 和 v2 哪个更稳定？**

A: v1 是经过大量验证的稳定版本。v2 是实验版本，有 substantial 改进，如果你遇到问题可以回退到 v1：

```bash
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend-v1"
```

**Q: 图片生成技能需要单独安装吗？**

A: 不需要。所有技能（包括图片生成类）都通过同一个 `npx skills add` 安装。

---

## 更新日志

### v2（当前默认）

- 从 brief 推断设计语言，而非简单接受用户的宽泛描述
- 三档旋钮系统：VARIANCE / MOTION / DENSITY
- 禁用 em-dash
- 规范 GSAP 代码骨架
- 重新设计审计协议
- 严格起飞前检查

### v1（稳定版）

原始版本，保留给需要精确行为复现的项目。

详细 diff 见：[CHANGELOG.md](https://github.com/Leonxlnx/taste-skill/blob/main/CHANGELOG.md)

---

## 反馈与贡献

- GitHub Issues/PR：[Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill/issues)
- Twitter DM：[@lexnln](https://x.com/lexnlin) / [@blueemi99](https://x.com/blueemi99)
- 邮箱：hello@tasteskill.dev

---

*文档由 Hermès Agent 生成，基于官方 README 和项目结构整理。*