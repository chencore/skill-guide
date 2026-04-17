# BMad Method 安装与使用指南

> AI 驱动的敏捷开发框架 — 让人类和 AI 协作达到最佳状态

## 项目简介

BMad Method 是一个 100% 免费开源的 AI 驱动敏捷开发框架，44,907 Stars，5,326 Forks。

核心思想：**Human Amplification, Not Replacement** — 不是让 AI 替代你思考，而是让 AI 放大人和 AI 的协作产出。

它不是简单地把任务丢给 AI，而是通过结构化的工作流和专业化 Agent，引导你完成从头脑风暴到部署的完整开发流程。

---

## 核心特性

- **34+ 工作流**：覆盖分析、计划、架构、开发、测试全流程
- **12+ 专业化 Agent**：PM、架构师、开发工程师、UX 等角色
- **Party Mode**：多 Agent 角色同时参与一个会话，协作讨论
- **Scale-Domain-Adaptive**：根据项目复杂度自动调整规划深度
- **bmad-help 技能**：随时调用，获得"下一步该做什么"的实时指导
- **完全免费**：无付费墙、无 gated Discord、无付费课程

---

## 前置要求

- **Node.js** v20+
- **Python** 3.10+
- **uv**（Python 包管理器）
- AI IDE（推荐 Claude Code、Cursor 等）

### 安装 uv（如果没有）

```bash
# macOS
brew install uv

# 或通过 pip
pip install uv
```

---

## 安装

### 方式一：交互式安装（推荐新手）

```bash
npx bmad-method install
```

安装向导会提示你选择：
- 安装目录
- 需要加载的模块
- 使用的 AI 工具（支持 Claude Code 等）

### 方式二：非交互式安装（CI/CD 用）

```bash
npx bmad-method install --directory /path/to/project --modules bmm --tools claude-code --yes
```

### 方式三：最新预发布版

```bash
npx bmad-method@next install
```

> ⚠️ 预发布版更新频繁，可能不稳定

---

## 快速开始

### 第一步：初始化项目

```bash
# 在你的项目目录下执行
npx bmad-method install
```

### 第二步：启动 AI IDE

安装完成后，在项目文件夹中打开你的 AI IDE（Claude Code、Cursor 等）。

### 第三步：开始工作

不知道下一步该做什么？随时输入：

```
bmad-help
```

它会告诉你接下来该做什么，以及哪些步骤是可选的。

也可以问具体问题：
```
bmad-help I just finished the architecture, what do I do next?
```

---

## 模块系统

BMad Method 支持扩展模块，可在安装时或安装后随时添加：

| 模块 | 说明 |
|------|------|
| **BMM (BMad Method)** | 核心框架，34+ 工作流 |
| **BMB (BMad Builder)** | 创建自定义 Agent 和工作流 |
| **TEA (Test Architect)** | 风险驱动测试策略与自动化 |
| **BMGD (Game Dev Studio)** | 游戏开发工作流（Unity/Unreal/Godot） |
| **CIS (Creative Intelligence Suite)** | 创新头脑风暴、设计思维 |

### 安装时添加模块

```bash
npx bmad-method install
# 按提示选择需要的模块
```

### 事后添加模块

```bash
npx bmad-method install --modules bmb,tea
```

---

## 工作流程示例

BMad 的标准开发流程：

```
1. 头脑风暴 → 定义问题与目标
2. 分析 → 需求分析与可行性评估
3. 计划 → 制定迭代计划
4. 架构 → 设计系统架构
5. 开发 → 编写代码
6. 测试 → 风险驱动测试
7. 部署 → 自动化部署
```

每个阶段都有对应的 Agent 角色和专业工作流，bmad-help 会根据你当前所处的阶段指导你进入下一步。

---

## 常用命令

```bash
# 安装
npx bmad-method install

# 更新
npx bmad-method update

# 查看版本
npx bmad-method --version

# 获取帮助
npx bmad-method help
```

---

## 文档与资源

| 资源 | 链接 |
|------|------|
| 官方文档 | https://docs.bmad-method.org |
| 新手教程 | https://docs.bmad-method.org/tutorials/getting-started/ |
| 升级指南（V5→V6）| https://docs.bmad-method.org/how-to/upgrade-to-v6/ |
| Discord 社区 | https://discord.gg/gk8jAdXWmj |
| YouTube 教程 | https://youtube.com/@BMadCode |
| X / Twitter | https://x.com/BMadCode |
| 官方网站 | https://bmadcode.com |

---

## 常见问题

**Q: 安装时报错"Node.js 版本不够"**
A: 确保 Node.js ≥ v20。运行 `node -v` 检查版本，如需升级：https://nodejs.org

**Q: 不知道该选哪些模块？**
A: 新手建议只装 BMM 核心模块，其他模块可以之后需要时再添加。

**Q: 安装后 AI IDE 没有响应？**
A: 确认安装过程没有报错，然后在 AI IDE 中重新加载项目或重启 IDE。

**Q: 遇到 bug 怎么反馈？**
A: 通过 GitHub Issues 报告：https://github.com/bmad-code-org/BMAD-METHOD/issues

---

## 目录结构

安装后的项目结构大致如下：

```
your-project/
├── .bmad/              # BMad 配置文件
│   ├── agents/         # Agent 角色定义
│   ├── workflows/      # 工作流配置
│   └── skills/         # 技能文件
├── bmad-workspace/     # 工作区目录
└── [your code files]
```

---

## 社区与贡献

BMad 是完全免费开源的项目，欢迎贡献：
- GitHub Issues：报告 bug 和功能请求
- GitHub Discussions：社区讨论
- Pull Request：提交代码（先在 Discord 与维护者确认）

---

*本指南由 马龙 🏓 整理 | 2026-04-17*
