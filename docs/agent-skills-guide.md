# Agent Skills — 安装与使用指南

> Production-grade engineering skills for AI coding agents.
> 一个为 AI 编程 Agent 设计的生产级工程技能集合，由 Google Chrome 工程副总裁 Addy Osmani 维护。

## 项目概述

`addyosmani/agent-skills` 将资深工程师在软件开发生命周期中遵循的工作流、质量关卡和最佳实践，编码为 AI Agent 可执行的结构化技能文件。每个 Skill 不仅描述"做什么"，更包含步骤流程、验证标准、反借口表——强制 Agent 像高级工程师一样思考。

### 核心数据
- ⭐ 37.8k Stars | 🍴 4.2k Forks
- License: MIT
- 22 个技能 + 7 个命令 + 3 个 Agent 角色 + 4 个参考清单

### 生命周期全景

```
  DEFINE          PLAN           BUILD          VERIFY         REVIEW          SHIP
 ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐      ┌──────┐
 │ Idea │ ───▶ │ Spec │ ───▶ │ Code │ ───▶ │ Test │ ───▶ │  QA  │ ───▶ │  Go  │
 │Refine│      │  PRD │      │ Impl │      │Debug │      │ Gate │      │ Live │
 └──────┘      └──────┘      └──────┘      └──────┘      └──────┘      └──────┘
  /spec          /plan          /build        /test         /review       /ship
```

### 为什么需要它？

AI 编程 Agent 默认追求"最短路径"——而这往往意味着跳过规格说明、测试、安全审查和那些让软件可靠的生产实践。Agent Skills 给 Agent 注入了结构化的工程纪律，弥补了 AI 辅助开发中最大的质量缺口。

---

## 支持的平台

| 工具 | 安装方式 | 集成深度 |
|------|---------|---------|
| **Claude Code** | 插件市场一键安装 | ⭐⭐⭐ 完整（命令+技能+Persona） |
| **Cursor** | 复制到 .cursorrules | ⭐⭐ 手动加载 Skill |
| **Gemini CLI** | 复制到 .gemini/commands | ⭐⭐ 命令集成 |
| **GitHub Copilot** | 手动加载到上下文 | ⭐⭐ 对话引用 |
| **Windsurf** | 复制到 rules 文件 | ⭐⭐ 手动加载 |
| **OpenCode** | 符号链接到 .opencode | ⭐⭐ 文件级集成 |
| **Codex** | 复制 SKILL.md 到上下文 | ⭐⭐ 对话引用 |
| **通用** | git clone + 手动加载 | ⭐ 任何支持 Markdown 的 Agent |

---

## 安装方式

### 方式一：Claude Code（推荐，体验最完整）

```bash
# 1. 添加插件市场
claude plugin marketplace add addyosmani/agent-skills

# 2. 一键安装全部技能 + 命令 + Agent Persona
claude plugin install agent-skills@agent-skills
```

安装后你立即获得：
- **7 个斜杠命令**：`/spec` `/plan` `/build` `/test` `/review` `/code-simplify` `/ship`
- **22 个技能**：根据你的工作内容自动激活
- **3 个专家 Persona**：`code-reviewer` `test-engineer` `security-auditor`
- **会话钩子**：在对话生命周期关键节点自动注入上下文

> 💡 **推荐首选 Claude Code**，因为插件市场的自动加载机制能根据你当前的操作自动激活对应 Skill，无需手动管理上下文。这也是 Addy Osmani 官方首推的方式。

---

### 方式二：通用安装（适用所有 Agent）

```bash
# 1. 克隆仓库
git clone https://github.com/addyosmani/agent-skills.git
cd agent-skills
```

**2. 选择集成方式：**

**A. 系统提示词法**（一次性加载大量 Skill）
将需要的 SKILL.md 内容粘贴到 Agent 的系统提示词或规则文件中。

**B. 规则文件法**（推荐）
将核心技能添加到项目的规则文件：

- Claude Code: 添加到 `CLAUDE.md`
- Cursor: 添加到 `.cursorrules`
- Windsurf: 添加到 `.windsurfrules`
- Gemini CLI: 添加到 `.gemini/commands/`

**C. 对话引用法**（最灵活）
在每次任务开始时指定要使用的 Skill：

```
请使用 test-driven-development 流程来实现这个功能。
```

**D. Shell 别名法**（快捷引用）
```bash
# 在 ~/.zshrc 或 ~/.bashrc 中添加
alias ask-spec='cat ~/agent-skills/skills/spec-driven-development/SKILL.md'
alias ask-tdd='cat ~/agent-skills/skills/test-driven-development/SKILL.md'

# 使用时：
ask-spec | claude --pipe
```

---

### 方式三：Cursor 专用

```bash
git clone https://github.com/addyosmani/agent-skills.git ~/agent-skills

# 将核心技能追加到项目 .cursorrules
cat ~/agent-skills/skills/spec-driven-development/SKILL.md >> .cursorrules
cat ~/agent-skills/skills/test-driven-development/SKILL.md >> .cursorrules
cat ~/agent-skills/skills/code-review-and-quality/SKILL.md >> .cursorrules
```

---

### 方式四：Gemini CLI 专用

```bash
git clone https://github.com/addyosmani/agent-skills.git ~/agent-skills

# 安装命令
cp -r ~/agent-skills/.gemini/commands/* .gemini/commands/

# 此后在 Gemini CLI 中可以直接使用
# /spec、/plan、/build、/test、/review、/code-simplify、/ship
```

---

## 推荐配置

### 最小化配置（入门首选）

只加载 3 个技能，覆盖最核心的质量缺口：

| 技能 | 覆盖阶段 | 为什么必须装 |
|------|---------|------------|
| `spec-driven-development` | Define | 先定义再编码，避免 Agent 无头苍蝇 |
| `test-driven-development` | Verify | 测试先行的纪律，AI 最容易跳过的环节 |
| `code-review-and-quality` | Review | 合并前的质量检查，防止低质量代码入库 |

### 完整生命周期配置

```bash
# Define 阶段：定义要构建什么
cat skills/spec-driven-development/SKILL.md >> CLAUDE.md
cat skills/idea-refine/SKILL.md >> CLAUDE.md

# Plan 阶段：拆解为可执行任务
cat skills/planning-and-task-breakdown/SKILL.md >> CLAUDE.md

# Build 阶段：编写代码
cat skills/test-driven-development/SKILL.md >> CLAUDE.md
cat skills/incremental-implementation/SKILL.md >> CLAUDE.md

# Review 阶段：合并前的质量关卡
cat skills/code-review-and-quality/SKILL.md >> CLAUDE.md
cat skills/security-and-hardening/SKILL.md >> CLAUDE.md

# Ship 阶段：部署上线
cat skills/shipping-and-launch/SKILL.md >> CLAUDE.md
cat skills/git-workflow-and-versioning/SKILL.md >> CLAUDE.md
```

### 按场景加载（最省 Token）

**不要一次性加载所有 Skill——浪费上下文。** 根据当前任务选择性加载：

| 当前任务 | 加载 Skill |
|---------|-----------|
| 新项目/新功能 | `spec-driven-development` |
| 写代码 | `test-driven-development` + `incremental-implementation` |
| 做 UI | `frontend-ui-engineering` |
| 设计 API | `api-and-interface-design` |
| 调试 Bug | `debugging-and-error-recovery` |
| 合并前 | `code-review-and-quality` + `security-and-hardening` |
| 性能优化 | `performance-optimization` |
| 部署上线 | `shipping-and-launch` |
| 不确定用哪个 | `using-agent-skills`（元技能，帮你判断） |

---

## 使用指南

### 7 个斜杠命令（Claude Code / Gemini CLI）

| 命令 | 触发的 Skill | 用途 |
|------|-------------|------|
| `/spec` | spec-driven-development | 定义要构建什么：写 PRD，覆盖目标、架构、代码风格、测试策略 |
| `/plan` | planning-and-task-breakdown | 将 Spec 拆解为小粒度的可验证任务 |
| `/build` | incremental-implementation + TDD | 逐步构建：实现→测试→验证→提交 |
| `/test` | test-driven-development | 证明代码有效：Red-Green-Refactor |
| `/review` | code-review-and-quality | 五轴审查：正确性、安全性、性能、可维护性、一致性 |
| `/code-simplify` | code-simplification | 简化代码：Chesterton's Fence、Rule of 500 |
| `/ship` | shipping-and-launch | 部署检查清单、特性开关、灰度发布、回滚预案 |

### 对话中使用 Skill

Skill 也会根据你当前的操作**自动激活**。例如：
- 设计 API → 自动加载 `api-and-interface-design`
- 构建 UI → 自动加载 `frontend-ui-engineering`
- 遇到 Bug → 自动加载 `debugging-and-error-recovery`

你也可以显式引用：
```
使用 context-engineering 技能帮我准备这次会话的上下文
```

### 使用 Agent Persona（专项审查）

3 个预配置的专家角色：

```bash
# 代码审查视角
"用 code-reviewer persona 审查这次修改，重点看安全性"

# 测试策略视角
"用 test-engineer persona 评估当前测试覆盖"

# 安全审计视角
"用 security-auditor persona 检查认证流程"
```

| Persona | 角色定位 | 审查标准 |
|---------|---------|---------|
| **code-reviewer** | 高级 Staff Engineer | 五轴审查，"would a staff engineer approve this?" |
| **test-engineer** | QA Specialist | 测试策略、覆盖率分析、Prove-It 模式 |
| **security-auditor** | Security Engineer | 漏洞检测、威胁建模、OWASP 评估 |

### 使用参考清单

`references/` 目录包含 4 个补充清单，Skill 会在需要时自动拉取：

| 参考清单 | 配合 Skill | 覆盖内容 |
|---------|-----------|---------|
| `testing-patterns.md` | test-driven-development | 测试结构、命名、Mock、React/API/E2E 用例 |
| `performance-checklist.md` | performance-optimization | Core Web Vitals 指标、前后端检查清单 |
| `security-checklist.md` | security-and-hardening | Pre-commit 检查、认证、输入校验、OWASP Top 10 |
| `accessibility-checklist.md` | frontend-ui-engineering | 键盘导航、屏幕阅读器、ARIA、WCAG 2.1 AA |

---

## 完整 Skill 列表

### Meta — 元技能（1个）
| Skill | 用途 |
|-------|------|
| `using-agent-skills` | 将任务映射到合适的 Skill 流程，定义共享操作规则 |

### Define — 定义阶段（2个）
| Skill | 用途 | 触发条件 |
|-------|------|---------|
| `idea-refine` | 结构化发散/收敛思维，将模糊想法变为具体提案 | 有粗略概念需要探索 |
| `spec-driven-development` | 编写 PRD，覆盖目标、架构、代码风格、测试策略 | 新项目、新功能、重大变更 |

### Plan — 规划阶段（1个）
| Skill | 用途 | 触发条件 |
|-------|------|---------|
| `planning-and-task-breakdown` | 将 Spec 拆解为小粒度的可验证任务 | 已有 Spec 需要可执行单元 |

### Build — 构建阶段（8个）
| Skill | 用途 | 触发条件 |
|-------|------|---------|
| `incremental-implementation` | 薄垂直切片：实现→测试→验证→提交 | 修改涉及多个文件 |
| `test-driven-development` | Red-Green-Refactor，测试金字塔，Beyonce Rule | 实现逻辑、修复 Bug |
| `context-engineering` | 给 Agent 喂正确上下文，规则文件、上下文打包 | 开始会话、切换任务 |
| `source-driven-development` | 基于官方文档做决策，验证来源，标注未验证信息 | 需要权威引用 |
| `doubt-driven-development` | 对抗性审查每个非平凡决策 | 高风险（生产/安全/不可逆） |
| `frontend-ui-engineering` | 组件架构、设计系统、状态管理、WCAG 2.1 AA | 构建/修改 UI |
| `api-and-interface-design` | 契约优先设计、Hyrum's Law、错误语义 | 设计 API、模块边界 |
| `browser-testing-with-devtools` | Chrome DevTools MCP：DOM、控制台、网络、性能 | 浏览器相关调试 |

### Verify — 验证阶段（1个）
| Skill | 用途 | 触发条件 |
|-------|------|---------|
| `debugging-and-error-recovery` | 五步排错：重现→定位→缩小→修复→防御 | 测试失败、构建中断 |

### Review — 审查阶段（4个）
| Skill | 用途 | 触发条件 |
|-------|------|---------|
| `code-review-and-quality` | 五轴审查，变更大小（~100行），严重级别 | 合并任何变更前 |
| `code-simplification` | Chesterton's Fence、Rule of 500 | 代码运行但过于复杂 |
| `security-and-hardening` | OWASP Top 10、认证模式、密钥管理 | 处理用户输入/认证/外部集成 |
| `performance-optimization` | 先测量再优化，Core Web Vitals、Bundle 分析 | 存在性能要求 |

### Ship — 发布阶段（5个）
| Skill | 用途 | 触发条件 |
|-------|------|---------|
| `git-workflow-and-versioning` | Trunk-based development、原子提交 | 任何代码变更（始终激活） |
| `ci-cd-and-automation` | Shift Left、特性开关、质量关卡流水线 | 设置/修改构建部署流水线 |
| `deprecation-and-migration` | 代码即负债、弃用与迁移模式 | 移除旧系统、迁移用户 |
| `documentation-and-adrs` | ADR、API 文档、内联文档标准 | 架构决策、API 变更 |
| `shipping-and-launch` | 预发布检查清单、灰度发布、回滚预案 | 准备部署到生产 |

---

## Skill 解剖学

每个 Skill 有统一的结构，不是参考文档，而是**可执行的工作流**：

```
SKILL.md
├── Frontmatter (name, description)    # 名称 + "何时使用"触发条件
├── Overview                            # 这个 Skill 做什么
├── When to Use                         # 触发条件（何时自动激活）
├── Process                             # 逐步工作流（Agent 跟着走）
├── Examples                            # 代码示例和模式
├── Common Rationalizations             # "反借口表" — Agent 常见的偷懒理由 + 反驳
├── Red Flags                           # 信号灯 — 出问题的征兆
└── Verification                        # 验证清单 — 完成的硬性证据
```

### 核心设计原则

**流程，不是散文。** Skill 是 Agent 遵循的工作流，每个步骤有检查点和退出标准。

**反借口机制。** 每个 Skill 都有一张"常见借口表"——Agent 可能用来跳过步骤的理由（如"测试以后再补"）及记录在案的反驳论据。

**验证不可协商。** 每个 Skill 结尾都有硬性证据要求——测试通过、构建输出、运行时数据。"看起来没问题"永远不够。

**渐进式加载。** SKILL.md 是入口，补充参考文件只在需要时加载，最小化 Token 消耗。

---

## 项目结构

```
agent-skills/
├── skills/                            # 22 个技能（21 个生命周期 + 1 个元技能）
│   ├── spec-driven-development/
│   ├── test-driven-development/
│   ├── code-review-and-quality/
│   ├── ...（其余 19 个）
│   └── using-agent-skills/            # 元技能：如何使用本技能包
├── agents/                            # 3 个专家审查 Persona
│   ├── code-reviewer.md
│   ├── test-engineer.md
│   └── security-auditor.md
├── references/                        # 4 个补充检查清单
│   ├── testing-patterns.md
│   ├── performance-checklist.md
│   ├── security-checklist.md
│   └── accessibility-checklist.md
├── hooks/                             # 会话生命周期钩子
├── .claude/commands/                  # 7 个斜杠命令（Claude Code）
├── .gemini/commands/                  # 7 个斜杠命令（Gemini CLI）
├── .claude-plugin/                    # Claude Code 插件市场配置
├── docs/                              # 各工具安装指南
│   ├── getting-started.md
│   ├── skill-anatomy.md
│   ├── cursor-setup.md
│   ├── gemini-cli-setup.md
│   ├── opencode-setup.md
│   ├── windsurf-setup.md
│   └── copilot-setup.md
├── CLAUDE.md                          # 项目本身的 Agent 指令
├── AGENTS.md
└── CONTRIBUTING.md
```

---

## 实战工作流示例

### 场景：开发一个 Payload CMS 的用户认证系统

```bash
# Step 1: 定义需求
/spec
# Agent 按照 spec-driven-development 流程生成 SPEC.md
# 输出：目标、架构、代码风格、测试策略、边界条件

# Step 2: 拆解任务
/plan
# Agent 生成 tasks/plan.md 和 tasks/todo.md
# 输出：小粒度、可验证、有依赖关系的任务列表

# Step 3: 编码（TDD）
/build
# Agent 按 incremental-implementation + TDD 流程
# Red → Green → Refactor，每步验证

# Step 4: 测试
/test
# Agent 按 TDD 流程全面验证
# 输出：测试结果 + 覆盖率

# Step 5: 代码审查
/review
# Agent 五轴审查（正确性、安全性、性能、可维护性、一致性）

# Step 6: 上线
/ship
# Agent 按 shipping-and-launch 流程
# 输出：上线检查清单、灰度策略、回滚预案
```

---

## 自定义与扩展

### 添加自定义 Skill

```bash
# 1. 创建 Skill 目录
mkdir -p my-skills/custom-deployment/SKILL.md

# 2. 按 Skill Anatomy 格式编写
# 3. 加载到 Agent 上下文
cat my-skills/custom-deployment/SKILL.md >> CLAUDE.md
```

### Skill 格式规范

```markdown
---
name: custom-skill-name
description: Brief description of what this does. Use when [trigger conditions].
---

# Skill Title

## Overview
What this skill accomplishes.

## When to Use
- Trigger condition 1
- Trigger condition 2

## Process
### Step 1: ...
### Step 2: ...

## Common Rationalizations
| Excuse | Rebuttal |
|--------|----------|
| "I'll add this later" | ... |

## Red Flags
- Sign 1
- Sign 2

## Verification
- [ ] Evidence 1
- [ ] Evidence 2
```

---

## 最佳实践

1. **从 spec-driven-development 开始** — 任何非平凡工作都应该先写 Spec
2. **永远加载 test-driven-development** — 写代码时始终带着测试纪律
3. **不要跳过验证步骤** — 这才是 Skill 的价值所在
4. **按需加载 Skill** — 上下文越多不一定越好
5. **使用 Agent Persona 做审查** — 不同视角捕获不同问题
6. **将 SPEC.md 和 tasks/ 纳入版本控制** — 让人和 Agent 共享同一个真相来源
7. **不要在生产仓库长期保留 artifacts** — 合并前删除或将目录加入 .gitignore

---

## 常见问题

### Q: Claude Code 安装后命令不生效？

```bash
# 检查插件安装状态
claude plugin list

# 应该看到 agent-skills@agent-skills 处于激活状态
```

### Q: 如何只安装部分 Skill？

Claude Code 目前是一键安装全部。其他 Agent 可以手动选择需要的 SKILL.md 加载。

### Q: Skill 占用多少上下文？

每个 SKILL.md 约为 500-2000 字（视 Skill 复杂度）。建议同一次会话最多加载 3-5 个相关 Skill。

### Q: 如何贡献新 Skill？

1. Fork 仓库
2. 按 `docs/skill-anatomy.md` 格式编写
3. 确保 Skill 具体（可操作步骤）、可验证（明确退出标准）、实战检验（基于真实工作流）
4. 提交 PR

---

## 许可证

MIT — 可在项目、团队和工具中自由使用。

## 相关链接

- 官方仓库：https://github.com/addyosmani/agent-skills
- Addy Osmani：https://github.com/addyosmani
- Claude Code 文档：https://docs.anthropic.com/en/docs/claude-code
- Google 工程实践：https://google.github.io/eng-practices/
