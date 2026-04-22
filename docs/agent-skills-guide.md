# Agent Skills 安装与使用指南

> Google 工程师 Addy Osmani 出品的 AI 编程 Agent 工程技能集。
> 把资深工程师的开发流程打包成 AI 可执行的技能，让 AI 编程工具遵循生产级工程标准。

**⭐ 18785 | Python/Shell | [GitHub 仓库](https://github.com/addyosmani/agent-skills)**

---

## 核心能力

Agent Skills 把软件开发流程拆解成 7 个命令，覆盖完整开发周期：

```
DEFINE → PLAN → BUILD → VERIFY → REVIEW → SHIP
```

每个命令对应一套结构化技能，包含步骤、验证关卡和反过度设计表。

---

## 支持的工具

| 工具 | 安装方式 |
|------|---------|
| **Claude Code**（推荐）| Marketplace / 本地 |
| **Cursor** | .cursor/rules/ |
| **Gemini CLI** | gemini skills install |
| **Windsurf** | rules 配置 |
| **OpenCode** | AGENTS.md + skill 工具 |
| **GitHub Copilot** | .github/copilot-instructions.md |
| **Kiro IDE/CLI** | .kiro/skills/ |
| **其他 Agent** | Markdown 文件引用 |

---

## 安装步骤

### Claude Code（推荐）

**方式一：Marketplace 安装**

```bash
# 添加 Marketplace 插件
/plugin marketplace add addyosmani/agent-skills

# 安装 agent-skills
/plugin install agent-skills@addy-agent-skills
```

> ⚠️ 如果遇到 SSH 错误（Marketplace 通过 SSH 克隆），执行：
> ```bash
> git config --global url."https://github.com/".insteadOf "git@github.com:"
> ```

**方式二：本地安装**

```bash
# 克隆仓库
git clone https://github.com/addyosmani/agent-skills.git

# 启动 Claude Code 并指定插件目录
claude --plugin-dir /path/to/agent-skills
```

---

### Gemini CLI

```bash
# 从仓库安装
gemini skills install https://github.com/addyosmani/agent-skills.git --path skills

# 从本地克隆安装
git clone https://github.com/addyosmani/agent-skills.git
gemini skills install ./agent-skills/skills/
```

---

### Cursor

将 `skills/` 目录下的 `SKILL.md` 文件复制到 `.cursor/rules/` 目录，或直接在规则配置中引用 `skills/` 目录。

详细说明见 [docs/cursor-setup.md](docs/cursor-setup.md)。

---

### Windsurf

将技能内容添加到 Windsurf rules 配置中。详细说明见 [docs/windsurf-setup.md](docs/windsurf-setup.md)。

---

### OpenCode

通过 AGENTS.md 和 skill 工具使用。详细说明见 [docs/opencode-setup.md](docs/opencode-setup.md)。

---

### GitHub Copilot

使用 `agents/` 目录下的 Agent 定义作为 Copilot persona，技能内容放在 `.github/copilot-instructions.md`。详细说明见 [docs/copilot-setup.md](docs/copilot-setup.md)。

---

## 7 个核心命令

| 你在做什么 | 命令 | 核心原则 |
|-----------|------|---------|
| 定义要做什么 | `/spec` | 先写规格再写代码 |
| 规划如何实现 | `/plan` | 小而原子化的任务 |
| 增量构建 | `/build` | 每次只做一个切片 |
| 证明它能工作 | `/test` | 测试即证明 |
| 合并前 Review | `/review` | 提升代码健康度 |
| 简化代码 | `/code-simplify` | 清晰优于巧妙 |
| 发布到生产 | `/ship` | 更快即更安全 |

---

## 20 个技能详解

### Define — 明确要做什么

| 技能 | 功能 | 使用场景 |
|------|------|---------|
| idea-refine | 结构化思维，将模糊想法转化为具体方案 | 有 rough concept 需要探索 |
| spec-driven-development | 写 PRD，包含目标、命令、结构、代码风格、测试和边界 | 启动新项目、功能或重大变更 |

### Plan — 拆解任务

| 技能 | 功能 | 使用场景 |
|------|------|---------|
| planning-and-task-breakdown | 将规格拆解为小、可验证的任务，含验收标准和依赖排序 | 有规格但需要可执行单元 |

### Build — 写代码

| 技能 | 功能 | 使用场景 |
|------|------|---------|
| incremental-implementation | 薄垂直切片：实现→测试→验证→提交，含特性开关、安全默认值、回滚友好变更 | 任何涉及多文件的变更 |
| test-driven-development | 红-绿-重构，测试金字塔（80/15/5），测试大小，DAMP 原则 | 实现逻辑、修复 bug 或变更行为 |
| context-engineering | 在正确时间给 Agent 正确信息：规则文件、上下文打包、MCP 集成 | 启动会话、切换任务或输出质量下降 |
| source-driven-development | 基于官方文档验证每个框架决策，引用来源，标注未验证项 | 需要权威的、来源可查的代码 |
| frontend-ui-engineering | 组件架构、设计系统、状态管理、响应式设计、WCAG 2.1 AA 无障碍 | 构建或修改用户界面 |
| api-and-interface-design | 契约优先设计、Hyrum 法则、单版本规则、错误语义、边界验证 | 设计 API、模块边界或公共接口 |

### Verify — 证明它能工作

| 技能 | 功能 | 使用场景 |
|------|------|---------|
| browser-testing-with-devtools | Chrome DevTools MCP 获取实时运行数据：DOM 检查、控制台日志、网络追踪、性能分析 | 构建或调试任何在浏览器中运行的内容 |
| debugging-and-error-recovery | 五步排查：复现→定位→简化→修复→防护。停止线规则，安全降级 | 测试失败、构建中断或行为异常 |

### Review — 合并前的质量关卡

| 技能 | 功能 | 使用场景 |
|------|------|---------|
| code-review-and-quality | 五轴 Review，变更规模（~100行），严重级别（Nit/Optional/FYI），Review 速度规范 | 合并任何变更之前 |
| code-simplification | Chesterton's Fence，500 规则，在保持行为的前提下降低复杂度 | 代码能工作但比应有的更难读或维护 |
| security-and-hardening | OWASP Top 10 防护、认证模式、密钥管理、依赖审计、三层边界系统 | 处理用户输入、认证、数据存储或外部集成 |
| performance-optimization | 测量优先方法：Core Web Vitals 目标、性能分析工作流、包体积分析、反模式检测 | 存在性能要求或怀疑性能退化 |

### Ship — 部署上线

| 技能 | 功能 | 使用场景 |
|------|------|---------|
| git-workflow-and-versioning | 基于主干的开发，原子提交，变更规模（~100行），提交作为保存点模式 | 进行任何代码变更（始终使用） |
| ci-cd-and-automation | 左移，更快即更安全，特性开关，质量关卡流水线，失败反馈循环 | 设置或修改构建和部署流水线 |
| deprecation-and-migration | 代码即负债思维，强制性 vs 建议性废弃，迁移模式，僵尸代码清理 | 移除旧系统、迁移用户或停用功能 |
| documentation-and-adrs | 架构决策记录，API 文档，行内文档标准——记录"为什么" | 进行架构决策、变更 API 或发布功能 |
| shipping-and-launch | 发布前检查清单，特性开关生命周期，分阶段发布，回滚程序，监控设置 | 准备部署到生产环境 |

---

## 使用示例

### 场景 1：用 Claude Code 启动新项目

```bash
# 启动 Claude Code
claude

# 输入命令开始规划
/spec

# 输入你的项目想法，AI 会引导你写 PRD
```

### 场景 2：实现一个新功能

```bash
# 用 /plan 分解任务
/plan

# 用 /build 增量实现
/build

# 用 /test 写测试
/test

# 用 /review 做质量检查
/review

# 用 /ship 发布
/ship
```

### 场景 3：修复 Bug

```bash
# 用 /test 复现问题
/test

# AI 会自动激活 debugging-and-error-recovery 技能
```

---

## 技能如何工作

每个技能遵循统一的结构：

```
┌─────────────────────────────────────────┐
│  SKILL.md                               │
│                                         │
│  ┌─ Frontmatter ─────────────────────┐  │
│  │ name: lowercase-hyphen-name       │  │
│  │ description: Guide agents through │  │
│  │             [task]. Use when…    │  │
│  └───────────────────────────────────┘  │
│                                         │
│  # What                                 │
│  # Why                                  │
│  # When                                 │
│                                         │
│  ## Steps                               │
│  ## Verification                        │
│  ## Anti-rationalization table          │
└─────────────────────────────────────────┘
```

---

## Agent Personas（Agent 角色）

预设的专家角色，用于针对性 Review：

| Agent | 角色 | 视角 |
|-------|------|------|
| code-reviewer | 高级 Staff 工程师 | 五轴代码 Review，"Staff 工程师会批准这个吗？"标准 |
| test-engineer | QA 专家 | 测试策略、覆盖率分析、Prove-It 模式 |
| security-auditor | 安全工程师 | 漏洞检测、威胁建模、OWASP 评估 |

---

## 参考检查清单

| 参考文档 | 内容 |
|---------|------|
| testing-patterns.md | 测试结构、命名规范、Mock、React/API/E2E 示例、反模式 |
| security-checklist.md | 提交前检查、认证、输入验证、Headers、CORS、OWASP Top 10 |
| performance-checklist.md | Core Web Vitals 目标、前端/后端检查清单、测量命令 |
| accessibility-checklist.md | 键盘导航、屏幕阅读器、视觉设计、ARIA、测试工具 |

---

## 适用人群

- **AI 编程工具重度用户**：用 Claude Code、Cursor、Windsurf 等工具写代码的开发者
- **追求代码质量的团队**：希望 AI 输出工程级代码，而不是难以维护的代码
- **希望规范 AI 行为的企业**：把资深工程师的经验转化为可复用的 AI 技能

---

## 总结

Agent Skills 本质上是给 AI 编程 Agent 装上"工程规范层"。

它不改变 AI 的能力，而是让 AI 在每个开发阶段都遵循同样的高标准：先写规格、测试驱动、Code Review 后合并、小步提交……

让 AI 编程从"能写代码"升级到"能写好代码"。

---

**项目地址**：https://github.com/addyosmani/agent-skills
**Star 数**：18785
**维护者**：Addy Osmani（Google 工程师）
**许可证**：MIT
