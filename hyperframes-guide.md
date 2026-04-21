# HyperFrames 完整安装与使用指南

> Write HTML. Render video. Built for agents.
>
> HyperFrames 是一个开源视频渲染框架，让你可以用 HTML 创建、预览和渲染视频，同时提供一流的 AI Agent 支持。

---

## 目录

- [核心概念](#核心概念)
- [安装前准备](#安装前准备)
- [安装方式一：AI Agent（推荐）](#安装方式一ai-agent推荐)
- [安装方式二：手动安装](#安装方式二手动安装)
- [快速开始](#快速开始)
- [HTML 组成结构](#html-组成结构)
- [核心技能详解](#核心技能详解)
- [Block 目录使用](#block-目录使用)
- [CLI 命令详解](#cli-命令详解)
- [与 Remotion 对比](#与-remotion-对比)
- [常见问题](#常见问题)
- [许可协议](#许可协议)

---

## 核心概念

HyperFrames 的核心理念是：**用 HTML 写视频，用 AI 驱动创作**。

### 什么是 Composition？

Composition（组合）是 HyperFrames 中的基本单位，指一个完整的视频项目。每个 Composition 就是一个 HTML 文件，通过 `data-*` 属性描述视频的结构和时序。

```html
<div id="stage"
     data-composition-id="my-video"
     data-start="0"
     data-width="1920"
     data-height="1080">
  <!-- 视频轨道 -->
  <video data-start="0" data-duration="5" data-track-index="0" src="intro.mp4"></video>
  <!-- 图片轨道 -->
  <img data-start="2" data-duration="3" data-track-index="1" src="logo.png" />
  <!-- 音频轨道 -->
  <audio data-start="0" data-duration="9" data-track-index="2" data-volume="0.5" src="music.wav"></audio>
</div>
```

### 核心属性说明

| 属性 | 说明 |
|------|------|
| `data-composition-id` | 组合的唯一标识符 |
| `data-start` | 开始时间（秒） |
| `data-duration` | 持续时间（秒） |
| `data-track-index` | 轨道索引，数字越小越靠前 |
| `data-width` / `data-height` | 输出分辨率 |
| `data-volume` | 音量（0-1） |

---

## 安装前准备

### 环境要求

- **Node.js >= 22**
- **FFmpeg**（用于音视频编码）

### 检查环境

```bash
# 检查 Node.js 版本
node --version
# 需要 >= 22.0.0

# 检查 FFmpeg
ffmpeg -version
# 如果没有安装，使用以下方式：

# macOS
brew install ffmpeg

# Ubuntu/Debian
sudo apt update && sudo apt install ffmpeg

# Windows (使用 winget)
winget install ffmpeg
```

---

## 安装方式一：AI Agent（推荐）

HyperFrames 专为 AI Agent 设计，支持 Claude Code、Cursor、Gemini CLI、Codex 等主流 Agent。

### 安装 Skills

```bash
npx skills add heygen-com/hyperframes
```

这会自动安装以下技能：

| Skill | 功能 |
|-------|------|
| `hyperframes` | HTML 组合创作、字幕、TTS、音频反应动画、转场 |
| `hyperframes-cli` | CLI 命令：init、lint、preview、render、transcribe、tts、doctor |
| `hyperframes-registry` | 通过 `hyperframes add` 安装 Block 和组件 |
| `website-to-hyperframes` | 捕获 URL 并转换为视频 |
| `gsap` | GSAP 动画 API、时间线、缓动、ScrollTrigger、插件 |

### 在 Claude Code 中使用

安装后，Claude Code 会自动注册以下斜杠命令：

- `/hyperframes` - 创建视频组合
- `/hyperframes-cli` - 执行 CLI 命令
- `/gsap` - 获取动画帮助

### 使用示例

#### 冷启动：描述你想要的内容

```
Using /hyperframes, create a 10-second product intro with a fade-in title, a background video, and background music.
```

#### 温启动：基于现有内容创建视频

```
Take a look at this GitHub repo https://github.com/heygen-com/hyperframes and explain its uses and architecture to me using /hyperframes.
```

#### 格式特定：创建短视频

```
Make a 9:16 TikTok-style hook video about [topic] using /hyperframes, with bouncy captions synced to a TTS narration.
```

#### 迭代修改：像视频编辑一样对话

```
Make the title 2x bigger, swap to dark mode, and add a fade-out at the end.
Add a lower third at 0:03 with my name and title.
```

---

## 安装方式二：手动安装

### 初始化项目

```bash
npx hyperframes init my-video
cd my-video
```

### 预览视频

```bash
npx hyperframes preview
```

这会在浏览器中打开实时预览，支持热重载。

### 渲染视频

```bash
npx hyperframes render
```

输出 MP4 文件。

> **提示**：`hyperframes init` 会自动安装技能，之后你可以随时交给 AI Agent 处理。

---

## 快速开始

### 方式一：使用 AI Agent（推荐）

```bash
# 1. 安装技能
npx skills add heygen-com/hyperframes

# 2. 启动 Claude Code
claude

# 3. 输入指令
Using /hyperframes, create a 10-second product intro with a fade-in title.
```

### 方式二：手动创建

```bash
# 1. 初始化项目
npx hyperframes init my-video
cd my-video

# 2. 查看目录结构
ls -la

# 3. 编辑 index.html
# 添加你的视频组合

# 4. 预览
npx hyperframes preview

# 5. 渲染
npx hyperframes render
```

---

## HTML 组成结构

### 基础模板

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Video</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      background: #000;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }
  </style>
</head>
<body>
  <div id="stage"
       data-composition-id="my-video"
       data-start="0"
       data-width="1920"
       data-height="1080">
    
    <!-- 视频轨道 -->
    <video
      id="clip-1"
      data-start="0"
      data-duration="5"
      data-track-index="0"
      src="intro.mp4"
      muted
      playsinline>
    </video>
    
    <!-- 图片/文字轨道 -->
    <div
      id="overlay"
      data-start="2"
      data-duration="3"
      data-track-index="1"
      style="position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);">
      <h1 style="color: white; font-size: 72px;">Hello World</h1>
    </div>
    
    <!-- 音频轨道 -->
    <audio
      id="bg-music"
      data-start="0"
      data-duration="9"
      data-track-index="2"
      data-volume="0.5"
      src="music.wav">
    </audio>
    
  </div>
</body>
</html>
```

### 使用 GSAP 添加动画

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js"></script>
<script>
  // 在 video 开始时触发动画
  const overlay = document.getElementById('overlay');
  
  // GSAP 会自动与 HyperFrames 的时间轴同步
  gsap.fromTo(overlay, 
    { opacity: 0, y: 50 },
    { opacity: 1, y: 0, duration: 1, ease: 'power2.out' }
  );
</script>
```

---

## 核心技能详解

### hyperframes 技能

教 AI Agent 如何：
- 编写正确的 HTML 组合
- 使用 GSAP 动画
- 添加字幕和 TTS
- 音频反应动画
- 转场效果

### hyperframes-cli 技能

| 命令 | 说明 |
|------|------|
| `init <name>` | 初始化新项目 |
| `lint` | 检查组合语法 |
| `preview` | 浏览器预览 |
| `render` | 渲染为 MP4 |
| `transcribe` | 生成字幕 |
| `tts` | 文字转语音 |
| `doctor` | 检查环境依赖 |

### hyperframes-registry 技能

用于安装预制的 Blocks 和组件：

```bash
npx hyperframes add flash-through-white   # Shader 转场
npx hyperframes add instagram-follow      # 社交媒体覆盖层
npx hyperframes add data-chart             # 数据图表动画
```

浏览完整目录：https://hyperframes.heygen.com/catalog

### website-to-hyperframes 技能

将任意网页转换为视频：

```bash
npx hyperframes capture https://example.com --output video.mp4
```

---

## Block 目录使用

HyperFrames 提供 50+ 预制 Blocks，涵盖：

- **社交覆盖层**：Instagram 关注、Twitter 分享、YouTube 订阅
- **Shader 转场**：闪光转白、模糊溶解、像素化
- **数据可视化**：动态图表、柱状图赛跑、折线图
- **电影效果**：Kinetic Typography、动态背景、粒子效果

### 安装示例

```bash
# 添加 Shader 转场
npx hyperframes add flash-through-white

# 添加社交媒体覆盖层
npx hyperframes add instagram-follow
npx hyperframes add tiktok-username

# 添加数据图表
npx hyperframes add data-chart
npx hyperframes add bar-chart-race
```

---

## CLI 命令详解

### npx hyperframes init

初始化新项目：

```bash
npx hyperframes init my-video
cd my-video
```

这会创建：
- `index.html` - 主组合文件
- `package.json` - 项目配置
- 自动安装必要的技能

### npx hyperframes preview

启动浏览器预览：

```bash
npx hyperframes preview
# 输出：Server running at http://localhost:3000
```

支持热重载，修改 HTML 文件会自动刷新。

### npx hyperframes render

渲染为 MP4：

```bash
npx hyperframes render
# 输出：Rendering complete: output.mp4
```

可选参数：
- `--output <path>` - 指定输出路径
- `--quality <1-100>` - 输出质量
- `--fps <number>` - 帧率（默认 30）

### npx hyperframes lint

检查 HTML 语法：

```bash
npx hyperframes lint
# 检查组合结构、属性完整性、文件引用
```

### npx hyperframes doctor

检查环境：

```bash
npx hyperframes doctor
# 检查 Node.js 版本、FFmpeg 安装、依赖完整性
```

---

## 与 Remotion 对比

HyperFrames 受 Remotion 启发，但在核心设计上有所不同：

| 特性 | HyperFrames | Remotion |
|------|-------------|----------|
| 创作方式 | HTML + CSS + GSAP | React 组件 (TSX) |
| 构建步骤 | 无；index.html 直接运行 | 需要打包器 |
| 库时钟动画 | 可seek、帧精确 | 渲染时按真实时间播放 |
| 任意 HTML/CSS | 直接粘贴并动画 | 需要重写为 JSX |
| 分布式渲染 | 当前仅单机 | Lambda、生产就绪 |
| 许可 | Apache 2.0（完全开源） | 源码可用（有使用限制） |

### 为什么选择 HyperFrames？

1. **AI 原生**：AI Agent 已经理解 HTML，无需学习新的 DSL
2. **零构建**：无需配置打包器，HTML 文件直接可用
3. **确定性渲染**：相同输入 = 相同输出，适合自动化流水线
4. **完全开源**：Apache 2.0，无渲染费用、无席位限制

---

## 常见问题

### Q: Node.js 版本不满足要求

```bash
# 使用 nvm 升级
nvm install 22
nvm use 22
```

### Q: FFmpeg 未找到

```bash
# macOS
brew install ffmpeg

# Ubuntu
sudo apt install ffmpeg

# 验证安装
ffmpeg -version
```

### Q: 渲染失败

```bash
# 检查环境
npx hyperframes doctor

# 查看详细日志
npx hyperframes render --verbose
```

### Q: 如何添加字幕？

```bash
# 方式一：使用 transcribe 命令
npx hyperframes transcribe input.mp4 --output subtitles.vtt

# 方式二：在 HTML 中直接引用
<track kind="subtitles" src="subtitles.vtt" srclang="en" />
```

### Q: 如何使用 TTS？

```bash
npx hyperframes tts "Hello, this is a test." --output audio.mp3
```

### Q: 如何与 AI Agent 协作？

```bash
# 1. 先用 CLI 初始化项目
npx hyperframes init my-video
cd my-video

# 2. 启动 AI Agent
claude

# 3. 让 Agent 完善视频
Using /hyperframes, add a fade-in animation to the title and background music.
```

---

## 许可协议

HyperFrames 采用 **Apache 2.0** 许可证：

- ✅ 可商业使用
- ✅ 可在任何规模使用
- ✅ 无每次渲染费用
- ✅ 无席位上限
- ✅ 无公司规模门槛
- ✅ 可再分发
- ✅ 可修改源码

---

## 更多资源

- **官方文档**：https://hyperframes.heygen.com/introduction
- **Block 目录**：https://hyperframes.heygen.com/catalog
- **GitHub 仓库**：https://github.com/heygen-com/hyperframes
- **演示 Demo**：https://hyperframes.heygen.com

---

## 总结

HyperFrames 是一个革命性的视频创作工具，它：

1. **降低门槛**：用 HTML 写视频，无需学习新语言
2. **AI 优先**：专为 AI Agent 设计，Agent 已经理解 HTML
3. **开源透明**：Apache 2.0，无隐藏费用
4. **确定性渲染**：适合自动化和生产流水线

无论你是开发者、内容创作者还是 AI Agent 用户，HyperFrames 都提供了一个简洁而强大的解决方案。

**开始使用**：

```bash
npx skills add heygen-com/hyperframes
# 然后用 /hyperframes 指令描述你的视频
```
