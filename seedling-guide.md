# 🌱 Seedling — 将 Superpowers 计划文件精确映射为 GitHub Issue 的命令行工具

> **AI 驱动的 GitHub Issue 生成与同步工具** — 把实施计划自动拆解为可追踪的 Issue，像管理代码一样管理 Issue。

---

## 为什么需要 Seedling？

使用 Superpowers 框架开发时，`writing-plans` 技能会生成一份详细的实施计划文件（markdown），里面包含了按 chunk 拆解的任务、文件路径、验证步骤。但问题来了：

- **计划文件是死的** — 没有人会每天打开计划文件追踪进度
- **手动创建 Issue 太痛苦** — 一个中等项目可能有 15-30 个任务，逐一手动建 Issue 耗时且容易遗漏
- **计划会变** — 需求调整后，计划更新了但 Issue 还在原地，信息断层
- **缺乏可追溯性** — Issue 和原始规范文档之间没有链接，出了问题不知道从哪里查

**Seedling 就是为解决这个问题而生的。** 它读入你的计划文件 + 规范文档，用 LLM 智能拆解成结构化 Issue，然后同步到 GitHub。更妙的是，Issue 以 markdown 文件形式保存在本地仓库中，可以像代码一样被 review、编辑和版本控制。

---

## 核心特性

- **🤖 AI 智能拆解** — 输入实施计划和规范文档，LLM 自动拆解为标题清晰、描述详尽、标签准确的结构化 Issue
- **📝 Issue 即代码** — 每个 Issue 都是一个 YAML frontmatter + markdown 文件，随项目代码一起版本控制
- **🔄 幂等同 Sync** — `sync` 命令基于映射文件（mapping）判断增删改，创建/更新/关闭 GitHub Issue，不会重复创建
- **🏷️ 智能标签和分配** — LLM 根据任务领域自动打标签（`backend`/`frontend`/`must`/`should`），如果配置了团队名单，还能自动分配负责人
- **🔗 可追溯** — 生成的 Issue 包含指向原始规范文档章节的链接，随时回溯需求来源
- **🛡️ 容错生成** — Issue 逐批增量保存。网络超时等临时错误自动重试，限流等永久错误立即终止保留已生成部分

---

## 安装

Seedling 是一个 Node.js CLI 工具，发布在 npm。需要 Node.js ≥ 18。

### 全局安装（推荐）

```bash
npm install -g @elcoosp-ai/seedling
```

或使用 pnpm：

```bash
pnpm add -g @elcoosp-ai/seedling
```

安装后可以直接使用 `seedling` 命令：

```bash
seedling --help
```

### 免安装运行

如果你不想全局安装，也可以用 `npx` 或 `pnpx` 直接运行：

```bash
npx @elcoosp-ai/seedling gen --plan ./plan.md
pnpx @elcoosp-ai/seedling sync --repo owner/repo
```

> **提示**：下文示例都使用 `seedling` 命令，如果你选择免安装方式，只需把 `seedling` 替换为 `npx @elcoosp-ai/seedling`。

---

## 配置

在项目根目录创建 `seedling.config.json`：

```json
{
  "planPath": "./docs/plan.md",
  "specsDir": "./docs/specs",
  "issuesDir": "./docs/issues",
  "mappingFile": "./.issues-mapping.json",
  "github": {
    "owner": "myorg",
    "repo": "myrepo"
  },
  "llm": {
    "model": "gpt-4-turbo",
    "temperature": 0.2
  },
  "assignees": [
    {
      "username": "alice-dev",
      "role": "backend",
      "expertise": ["rust", "api", "database"]
    },
    {
      "username": "bob-ui",
      "role": "frontend",
      "expertise": ["react", "typescript", "css"]
    }
  ]
}
```

**配置项说明**：

| 字段 | 说明 | 默认值 |
|------|------|--------|
| `planPath` | 实施计划 markdown 文件路径 | — |
| `specsDir` | 规范文档目录 | `./specs` |
| `issuesDir` | 生成的 Issue 文件输出目录 | `./issues` |
| `mappingFile` | Issue ID 到 GitHub Issue Number 的映射文件 | `./issues-mapping.json` |
| `github.owner` | GitHub 仓库所有者 | — |
| `github.repo` | GitHub 仓库名 | — |
| `llm.model` | 使用的 LLM 模型 | `llama3.2` |
| `llm.temperature` | LLM 温度参数 | `0.15` |
| `assignees` | 团队成员名单（用于智能分配） | `[]` |

命令行参数优先级高于配置文件，方便一次性覆盖。

---

## 使用方式

Seedling 提供两个核心命令：**`gen`**（生成 Issue 文件）和 **`sync`**（同步到 GitHub）。

### 1. `seedling gen` — 从计划生成 Issue 文件

```bash
seedling gen [options]
```

**选项**：

| 选项 | 说明 |
|------|------|
| `-p, --plan <path>` | 实施计划文件路径 |
| `-c, --config <path>` | 配置文件路径（默认 `seedling.config.json`） |
| `-s, --specs <dir>` | 规范文档目录 |
| `-o, --out <dir>` | Issue 输出目录 |
| `-m, --model <model>` | LLM 模型 |
| `-t, --temperature <n>` | LLM 温度 |

**示例**：

```bash
# 使用配置文件中的所有默认值
seedling gen

# 指定所有参数
seedling gen --plan ./docs/plan.md --specs ./docs/specs --out ./generated-issues --model gpt-4-turbo

# 只覆盖部分参数
seedling gen --plan ./my-feature-plan.md --out ./feature-issues
```

**执行过程**：

1. 读取计划文件和规范文档
2. 构建系统提示（包含团队名单、链接格式等信息）
3. 以迭代方式调用 LLM，逐批生成 Issue（每批一个 JSON chunk）
4. 每个 Issue 写入一个独立的 `.md` 文件到输出目录
5. 生成完成后输出汇总报告（`_report.json`）

**容错机制**：如果某一步生成失败（网络超时、LLM 限流等），已成功写入的 Issue 不会丢失。瞬态错误（网络）会自动重试 3 次（指数退避），永久错误（API 配额耗尽）会立即中止。

### 2. `seedling sync` — 同步 Issue 到 GitHub

```bash
seedling sync --repo owner/repo [options]
```

**选项**：

| 选项 | 说明 |
|------|------|
| `-r, --repo <owner/repo>` | **（必填）** GitHub 仓库 |
| `-t, --token <token>` | GitHub Token（也可通过 `GITHUB_TOKEN` 环境变量设置） |
| `-d, --dir <dir>` | Issue 文件目录（默认 `./issues`） |
| `-m, --mapping <file>` | 映射文件路径（默认 `./issues-mapping.json`） |
| `--dry-run` | 预览模式，只显示将要执行的操作，不实际调用 GitHub API |

**Token 设置**：

```bash
# 方式一：环境变量
export GITHUB_TOKEN=ghp_abc123

# 方式二：.env 文件
echo "GITHUB_TOKEN=ghp_abc123" > .env

# 方式三：命令行参数（不推荐，会留在 shell 历史中）
seedling sync --repo owner/repo -t ghp_abc123
```

**示例**：

```bash
# 基本用法
seedling sync --repo elcoosp/my-project

# 指定目录
seedling sync --repo elcoosp/my-project --dir ./docs/issues

# 预览模式 — 先看看会改什么，再真正执行
seedling sync --repo elcoosp/my-project --dry-run
```

**Sync 的行为逻辑**：

1. 读取映射文件，建立 `issue-id → GitHub Issue Number` 的对应关系
2. 扫描 Issue 目录中的所有 `.md` 文件
3. 对每个 Issue 文件：
   - **新 Issue**（映射中无记录）→ 在 GitHub 创建新 Issue
   - **已有 Issue 但文件有变更**（mtime 不同）→ 更新标题、描述、标签、状态
   - **已有 Issue 且无变更** → 跳过
4. 对已删除的文件（映射中有但目录中无）→ 关闭对应的 GitHub Issue
5. 更新映射文件

---

## Issue 文件格式

每个生成的 Issue 是一个包含 YAML frontmatter 的 markdown 文件：

```markdown
---
id: implement-agent-loop-with-ring-buffer
title: Implement agent loop with ring buffer and debounce
labels:
  - core
  - performance
  - must
assignees:
  - alice-dev
references:
  - ../specs/archi.md#tiered-streaming
  - ../specs/srs.md#req-func-012
state: open
priority: must
effort: 3d
dependencies:
  - setup-project-foundation
createdAt: '2026-05-09T10:30:00.000Z'
---

## Context

The agent loop is the core execution engine...

**Related Plan Section:**
- [Chunk 5: Skill System](../plan.md#chunk-5-skill-system) — defines the agent execution model

**Related Requirements:**
- [REQ-FUNC-012](../specs/srs.md#req-func-012) — The system shall support an autonomous agent loop

**Related Architecture:**
- [ADR-007: Tiered Streaming](../specs/archi.md#adr-007-tiered-streaming) — streaming architecture for agent output

## Problem Statement

Currently there is no agent execution loop...

## Solution Approach

### Implementation Details

**Files to create/modify:**
- `src/core/agent-loop.ts` — main agent loop implementation
- `src/core/ring-buffer.ts` — ring buffer for streaming output
- `src/core/debounce.ts` — debounce utility

**Key interfaces:**
- `AgentLoop` — orchestrates the execute → observe → decide cycle
- `RingBuffer<T>` — fixed-size circular buffer with drain semantics

## Acceptance Criteria

- [ ] Agent loop processes inputs in order
- [ ] Ring buffer correctly handles overflow
- [ ] Debounce prevents redundant re-renders

## Testing Requirements

**BDD scenarios:**
- [SC-FUNC-010](../specs/test-verification.md#sc-func-010) — Agent loop processes single input

## Dependencies

- **Blocked by:** setup-project-foundation
- **Blocks:** implement-skill-registry

## Effort Estimate

- **Complexity:** High
- **Effort:** 3d
```

**Frontmatter 字段**：

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | string | 唯一标识符（默认由标题自动生成） |
| `title` | string | Issue 标题（必须） |
| `labels` | string[] | 标签列表 |
| `assignees` | string[] | 负责人列表 |
| `references` | string[] | 指向规范文档的链接 |
| `state` | `open` \| `closed` | Issue 状态 |
| `priority` | `must` \| `should` \| `could` | 优先级（MoSCoW） |
| `effort` | string | 预估工时（如 `0.5d` / `1d` / `3d`） |
| `dependencies` | string[] | 依赖的其他 Issue ID |
| `createdAt` | ISO 8601 | 创建时间 |

---

## 与 Superpowers 工作流的集成

Seedling 天然适配 Superpowers 的工作流。典型的使用流程：

```
Superpowers Brainstorming → Writing Plans → plan.md 生成
                    ↓
            seedling gen → 生成 Issue markdown 文件
                    ↓
            seedling sync → 同步到 GitHub Issues
                    ↓
    Subagent-Driven Development → 按 Issue 执行开发
                    ↓
            计划更新 → seedling gen → seedling sync → Issue 自动更新
```

### 推荐的项目目录结构

```
my-project/
├── docs/
│   ├── plan.md              ← Superpowers 生成的实施计划
│   ├── specs/               ← 规范文档
│   │   ├── srs.md
│   │   ├── archi.md
│   │   └── test-verification.md
│   └── issues/              ← seedling 生成的 Issue 文件
│       ├── implement-agent-loop.md
│       ├── setup-project-foundation.md
│       └── _report.json     ← 生成报告
├── src/
├── seedling.config.json     ← seedling 配置
├── .issues-mapping.json     ← 映射文件
└── .env                     ← GitHub Token 等环境变量
```

### 工作流命令速查

```bash
# Step 1: Superpowers 生成计划后，用 seedling 拆解 Issue
seedling gen --plan ./docs/plan.md --specs ./docs/specs --out ./docs/issues

# Step 2: 检查生成的 Issue 文件质量
ls ./docs/issues/
cat ./docs/issues/_report.json  # 查看汇总报告

# Step 3: 预览同步（确认无误）
seedling sync --repo chencore/my-project --dry-run

# Step 4: 正式同步
seedling sync --repo chencore/my-project

# Step 5: 计划更新后，重新生成并同步
seedling gen --plan ./docs/plan.md --out ./docs/issues
seedling sync --repo chencore/my-project
```

---

## 实战示例

以 Superpowers 的 `writing-plans` 技能生成的典型计划为例，假设有以下计划结构：

```markdown
# Implementation Plan: User Authentication System

## Chunk 1: Foundation (must)
- Set up project structure with proper layering
- Configure database connection and migration framework
- Implement base middleware chain

## Chunk 2: User Model (must)
- Define User schema with email, password_hash, timestamps
- Implement UserRepository with CRUD operations
- Write unit tests for User model validation

## Chunk 3: Auth Endpoints (must)
- POST /api/auth/register with validation
- POST /api/auth/login with JWT issuance
- POST /api/auth/refresh token rotation

## Chunk 4: Middleware (should)
- JWT verification middleware
- Role-based access control middleware
- Rate limiting on auth endpoints
```

运行 `seedling gen` 后，输出目录会包含：

```
docs/issues/
├── set-up-project-structure.md       (effort: 2d, labels: [infrastructure, must])
├── configure-database-connection.md  (effort: 1d, labels: [database, must])
├── implement-base-middleware.md      (effort: 1d, labels: [backend, must])
├── define-user-schema.md             (effort: 1d, labels: [database, must])
├── implement-user-repository.md      (effort: 2d, labels: [backend, must])
├── write-user-model-tests.md         (effort: 1d, labels: [testing, must])
├── create-auth-register-endpoint.md  (effort: 2d, labels: [backend, must])
├── create-auth-login-endpoint.md     (effort: 2d, labels: [backend, must])
├── implement-token-refresh.md        (effort: 1d, labels: [backend, must])
├── implement-jwt-middleware.md       (effort: 2d, labels: [backend, should])
├── implement-rbac-middleware.md      (effort: 2d, labels: [backend, should])
├── implement-rate-limiting.md        (effort: 1d, labels: [backend, security, should])
└── _report.json
```

运行 `seedling sync --repo myorg/auth-service` 后，12 个 Issue 会出现在 GitHub 上，每个 Issue 都带有：
- 结构化的描述（Context → Problem → Solution → Acceptance Criteria）
- 指向原始计划和规范文档的可点击链接
- 合适的标签和优先级
- 依赖关系标注

---

## LLM 模型选择

Seedling 默认使用 `llama3.2`（本地 Ollama 模型）。你可以根据需求切换：

```bash
# 使用 OpenAI GPT-4
seedling gen --model gpt-4-turbo

# 使用智谱 GLM（云端）
export OLLAMA_API_KEY=your-api-key
seedling gen --model glm-5:cloud

# 在配置文件中指定
# seedling.config.json → "llm": { "model": "claude-3-opus" }
```

对于中文项目，推荐使用对中文支持更好的模型（如 `gpt-4-turbo`、`glm-5:cloud`），Issue 标题和描述会更自然。

---

## 最佳实践

1. **`.issues-mapping.json` 要纳入版本控制** — 这是连接本地 Issue 文件和 GitHub Issue 的桥梁，丢了会导致重复创建
2. **先 dry-run 再 sync** — 尤其是在 CI 中自动同步时，先用 `--dry-run` 确认变更符合预期
3. **`_report.json` 用于 CI 检查** — 可以在 CI 中解析报告，检查是否有 `must` 优先级的 Issue 被遗漏
4. **团队名单及时更新** — `assignees` 配置决定了 AI 分配 Issue 的准确性，人员变动后记得更新
5. **计划文件和 Issue 文件在同一仓库** — 这样 `references` 中的相对链接在 GitHub Issue 中可以直接点击跳转
6. **定期 re-gen 保持同步** — 当实施计划有重大调整时，重新运行 `seedling gen`，sync 会自动处理新增、更新和关闭

---

## 常见问题

**Q: sync 会删除我手动在 GitHub 上编辑的内容吗？**

A: 会的。sync 以本地 Issue 文件为准。如果你在 GitHub 上手动修改了 Issue 描述，下次 sync 会覆盖。建议所有修改都在本地 `.md` 文件中进行，然后 sync 上去。这也是 "Issue 即代码" 理念的核心。

**Q: 可以在 CI/CD 中自动运行吗？**

A: 完全可以。在 GitHub Actions 中：

```yaml
- name: Generate and Sync Issues
  run: |
    npx @elcoosp-ai/seedling gen
    npx @elcoosp-ai/seedling sync --repo ${{ github.repository }}
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    OLLAMA_API_KEY: ${{ secrets.LLM_API_KEY }}
```

**Q: 能指定 Issue 的 milestone 吗？**

A: 当前版本支持在 Issue frontmatter 中设置 `milestone` 字段，sync 时会被同步到 GitHub。

---

## 总结

Seedling 填补了从「AI 生成的实施计划」到「可执行的 GitHub Issue」之间的最后一公里。它让 Superpowers 工作流中的 `writing-plans` 产物不再是一份静态文档，而是成为项目管理的事实来源（source of truth）。

核心理念简单但强大：**把 Issue 当作代码管理**。可 review、可 diff、可版本控制、可自动化。

> GitHub: [elcoosp/elcoosp-ai](https://github.com/elcoosp/elcoosp-ai)  
> npm: [@elcoosp-ai/seedling](https://www.npmjs.com/package/@elcoosp-ai/seedling)  
> License: MIT
