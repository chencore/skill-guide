# llm-wiki-manager 安装与使用指南

> 一个 Claude Code Skill —— 构建由 LLM 全权管理的个人知识 Wiki。你来策展信息源、提出问题，LLM 负责所有的阅读、写作、交叉引用和簿记工作。

---

## 目录

- [1. 这是什么？](#1-这是什么)
- [2. 核心架构](#2-核心架构)
- [3. 安装](#3-安装)
- [4. 快速开始（5 分钟）](#4-快速开始5-分钟)
- [5. 8 种工作模式详解](#5-8-种工作模式详解)
  - [5.1 Bootstrap — 初始化 Wiki](#51-bootstrap--初始化-wiki)
  - [5.2 Ingest — 摄入信息源](#52-ingest--摄入信息源)
  - [5.3 Query — 查询 Wiki](#53-query--查询-wiki)
  - [5.4 Update — 更新过时观点](#54-update--更新过时观点)
  - [5.5 Lint — 健康检查](#55-lint--健康检查)
  - [5.6 Schema-evolve — 规则进化](#56-schema-evolve--规则进化)
  - [5.7 Multi-wiki — 多 Wiki 路由](#57-multi-wiki--多-wiki-路由)
  - [5.8 Teach — 教学模式](#58-teach--教学模式)
- [6. 脚本工具参考](#6-脚本工具参考)
- [7. 模板文件](#7-模板文件)
- [8. 兼容平台](#8-兼容平台)
- [9. 最佳实践](#9-最佳实践)
- [10. 常见问题](#10-常见问题)

---

## 1. 这是什么？

**llm-wiki-manager** 实现的是 Andrej Karpathy 提出的 "LLM Wiki" 模式。

### 与 RAG 的本质区别

| | RAG | LLM Wiki |
|---|---|---|
| 每次查询 | 重新检索原始文档 | 读取已编译好的 Wiki 页面 |
| 交叉引用 | 查询时动态发现 | **摄入时即时建立** |
| 矛盾处理 | 通常不处理 | **主动标记矛盾，保留双方来源** |
| 知识积累 | 每次从零开始 | **持续复合增长** |

**核心原则**：LLM 不做"每次重新发现答案"的事情——它在摄入源材料时就一次性完成综合、交叉引用和矛盾标记。Wiki 是一个**持久化的、不断复利的知识产物**。

### 来源

灵感来自 [Andrej Karpathy 的 LLM Wiki Gist](https://gist.github.com/karpathy)。这个项目将其包装为一个完整的 Claude Code Skill，具有 8 种工作模式、4 个幂等 Python 脚本、7 个页面模板和 9 个参考文档。

---

## 2. 核心架构

### 三层模型

```
your-wiki/
├── CLAUDE.md          # 规则层 — 当前 Wiki 的约定和配置
├── raw/               # 你的层 — 不可变的信息源。LLM 只读，绝不修改
└── wiki/              # LLM 层 — 所有页面由 LLM 编写和维护
    ├── index.md       # 内容目录（每次摄入后更新）
    ├── log.md         # 追加式操作日志（可 grep）
    ├── sources/       # 每个信息源一个摘要页
    ├── entities/      # 人物、组织、地点、产品
    ├── concepts/      # 概念、理论、框架、术语
    ├── notes/         # 归档的查询回答和松散页面
    └── reports/       # 自动生成的带日期的健康检查报告
```

### 分工

| 你做的事 | LLM 做的事 |
|---------|-----------|
| 策展信息源（决定读什么） | 端到端阅读信息源 |
| 提出问题，引导方向 | 写摘要、实体页、概念页 |
| 审阅 Wiki，跟踪链接 | 原地更新交叉引用 |
| 决定什么重要 | 维护 index.md 和 log.md |
| 拥有 `raw/` 目录 | 标记矛盾、发现知识空白 |

---

## 3. 安装

### 方式一：git clone（推荐）

```bash
git clone https://github.com/sametbrr/llm-wiki-manager ~/.claude/skills/llm-wiki-manager
```

### 方式二：GitHub CLI

需要 `gh` CLI v2.90+：

```bash
gh skill install sametbrr/llm-wiki-manager
```

### 方式三：.skill 文件

```bash
curl -L -o llm-wiki-manager.skill \
  https://github.com/sametbrr/llm-wiki-manager/releases/latest/download/llm-wiki-manager.skill
unzip llm-wiki-manager.skill -d ~/.claude/skills/llm-wiki-manager
```

### 验证安装

安装后启动一个新的 Claude Code 会话。当对话内容涉及 Wiki 管理时，Skill 会自动加载。

---

## 4. 快速开始（5 分钟）

```bash
# 1. 创建研究目录并启动 Claude Code
mkdir ~/research/my-topic && cd ~/research/my-topic && claude

# 2. 在 Claude Code 中初始化 Wiki
> "Set up an LLM wiki here. Topic: history of nutrition science."

# 3. 放入信息源
cp ~/Downloads/pollan-2008.pdf raw/

# 4. 摄入信息源
> "Ingest Pollan's In Defense of Food"

# 5. 提问
> "What does the wiki say about nutritionism?"

# 6. 健康检查（自动保存带日期的报告到 wiki/reports/）
> "Lint the wiki"
```

---

## 5. 8 种工作模式详解

Skill 会根据自然语言自动识别你需要的模式，**无需使用斜杠命令**。

### 5.1 Bootstrap — 初始化 Wiki

**触发词**：`"Set up a wiki"`, `"start a knowledge base here"`, `"init a wiki"`

**效果**：创建 `raw/`、`wiki/`、`CLAUDE.md` 从模板搭建。

```bash
# 也可以手动调用脚本
python scripts/init_wiki.py --path ~/research/my-topic --name "Nutrition Wiki" --topic "history of nutrition science"
```

> 脚本是幂等的：已存在的文件不会被覆盖，缺少的部分会被添加。

### 5.2 Ingest — 摄入信息源

**触发词**：`"Add this PDF to the wiki"`, `"I just read X, file it"`, 把文件放入 `raw/` 后告诉 LLM

**效果**：
1. 阅读信息源全文
2. 写入 `wiki/sources/<source-slug>.md` 摘要页
3. 更新相关的 entity 和 concept 页面（交叉引用即时建立）
4. 更新 `index.md`
5. 追加 `log.md` 条目

**支持的信息源格式**：PDF、Markdown、纯文本、网页链接、转录稿、截图中的文字。

**关键特性**：如果新信息源与已有观点矛盾，不会覆盖旧观点——两者同时保留并标记来源，以 `> [!warning] Sources disagree` 标注。

### 5.3 Query — 查询 Wiki

**触发词**：`"What does the wiki say about X?"`, `"Compare X and Y"`, `"based on what I've collected…"`

**效果**：
1. 读取 `index.md` 找候选页面
2. 读取相关页面
3. 综合答案并附带引用
4. 询问是否需要将答案归档到 `wiki/notes/`

### 5.4 Update — 更新过时观点

**触发词**：`"Smith 2024 supersedes Keys 1980, update the wiki"`

**适用于**：新信息源的观点影响到了 **3 个以上已有页面**（如果只影响 1 个页面，Ingest 模式就够了）。

**流程**：
1. 语义扫描 —— LLM 找出所有受影响的页面（grep 做不到的语义关联）
2. 展示范围：`"4 pages affected. Proceed?"`
3. 逐页 diff-before-write —— 每页 y/n/skip/edit
4. 配套策略：修订/争议/注释（非一刀切）
5. 一条日志条目关联所有 4 处编辑

### 5.5 Lint — 健康检查

**触发词**：`"Health check the wiki"`, `"anything broken?"`, `"audit"`

**效果**：
- 检查孤儿页面、断链、索引与实际文件不一致、日志空白期
- 默认写入 `wiki/reports/lint-YYYY-MM-DD.md`
- 自动添加 Index 条目和 Log 条目
- 同一天重复运行会覆盖当天的报告
- 发现阻断级问题时 exit code = 1（适用于 CI）

```bash
# 手动运行
python scripts/lint_wiki.py                    # 默认：写入报告 + 自动追踪
python scripts/lint_wiki.py --stdout           # 终端输出，不写文件
python scripts/lint_wiki.py --no-track         # 写文件但不更新 index/log
python scripts/lint_wiki.py --report /tmp/custom.md  # 自定义路径
```

### 5.6 Schema-evolve — 规则进化

**触发词**：`"We should always do X going forward"`, `"update CLAUDE.md"`

**效果**：将有效的约定写入 `CLAUDE.md`，使未来的 LLM 会话自动继承。你不需要每次都重新解释约定。

### 5.7 Multi-wiki — 多 Wiki 路由

**适用场景**：有项目 Wiki + 全局 Wiki（通常是已有的 Obsidian 知识库）

**一次性设置**：在项目 `CLAUDE.md` 中添加：

```markdown
## External Wiki

Global knowledge base: ~/Documents/obsidian/

### Routing rules
- Project-specific code decisions, architecture, bugs, configuration → this project's `wiki/`
- Concepts, frameworks, patterns, ideas applicable beyond this project → global wiki
- When in doubt, ask before writing
```

**四种典型场景**：

| 场景 | 触发词 | 效果 |
|------|--------|------|
| **A. 写入全局** | "Add JWT refresh rotation to my global wiki" | 读取项目 CLAUDE.md → 解析全局路径 → 写入全局 Wiki，项目 Wiki 不变 |
| **B. 从全局拉取** | "What does the global wiki say about rate limiting? Apply it to /api/search" | 读取全局页面 → 综合建议 → 写项目专属页并链接到全局页（绝不复制内容） |
| **C. 提升页面** | "concepts/event-sourcing.md has matured, promote it" | 移动到全局 → 在原位置留一行重定向 → 更新两个 index 和 log |
| **D. 双 Lint** | "Lint both wikis" | 分别对两个 Wiki 运行 lint_wiki.py → 综合报告 |

### 5.8 Teach — 教学模式

**触发词**：`"How does this pattern work?"`, `"explain the LLM wiki idea"`

**效果**：解释 LLM Wiki 模式本身，对比 RAG，用具体例子演示流程。

---

## 6. 脚本工具参考

4 个脚本，全部纯 Python 标准库（零外部依赖），全部幂等：

| 脚本 | 功能 | 用法示例 |
|------|------|---------|
| `scripts/init_wiki.py` | 搭建新 Wiki（幂等） | `python init_wiki.py --path ~/wiki --name "My Wiki"` |
| `scripts/append_log.py` | 追加日志条目 | `python append_log.py --path ~/wiki --action ingest --title "Pollan 2008"` |
| `scripts/update_index.py` | 更新索引目录 | `python update_index.py --path ~/wiki --category entities --title "Michael Pollan" --summary "Author" --link entities/michael-pollan.md` |
| `scripts/lint_wiki.py` | 健康检查 | `python lint_wiki.py --path ~/wiki` |

**日志格式**（标准统一，可 grep）：

```
## [2026-05-08] ingest | In Defense of Food (Pollan)
## [2026-05-08] query  | What is nutritionism?
## [2026-05-08] update | Keys 1980 claim superseded by Smith 2024
## [2026-05-08] lint   | 2 orphans, 1 broken link found
```

查看最近 20 条操作：`grep "^## \[" log.md | tail -20`

### append_log.py 支持的操作类型

`ingest`, `query`, `update`, `lint`, `audit`, `bootstrap`, `schema-evolve`, `note`

### lint_wiki.py 检查项

- **孤儿页面**：在 wiki/ 中存在但未在 index.md 中列出
- **断链**：页面中引用的 .md 链接指向不存在的文件
- **索引漂移**：index.md 中的条目指向已删除的页面
- **日志空白期**：超过阈值天数没有日志记录
- **存根页面**：内容过短且长时间未更新

---

## 7. 模板文件

位于 `assets/templates/`：

| 模板 | 用途 |
|------|------|
| `wiki-CLAUDE.md.tmpl` | 新建 Wiki 时放入的规则文件 |
| `index.md.tmpl` / `log.md.tmpl` | 初始脚手架 |
| `source-summary.md.tmpl` | 摄入的信息源摘要页结构 |
| `entity-page.md.tmpl` | 人物/组织/地点/产品页 |
| `concept-page.md.tmpl` | 概念/框架/术语页 |
| `comparison-page.md.tmpl` | "X vs Y" 对比页（通常是查询产出） |

---

## 8. 兼容平台

| 工具 | Skills 路径 | 备注 |
|------|-------------|------|
| Claude Code | `~/.claude/skills/` 或 `.claude/skills/` | 全局或项目级 |
| OpenAI Codex | `~/.codex/skills/` | 相同 SKILL.md 格式 |
| Cursor | `.cursor/skills/` | 项目级 |
| Gemini CLI | `~/.gemini/skills/` | |
| Windsurf | 项目级 skill 目录 | |

> 遵循 [agentskills.io](https://agentskills.io) 开放标准。

---

## 9. 最佳实践

### ✅ DO

- **每次交互后让 LLM 做簿记**：交叉引用、索引更新、日志条目。跳过这些是 Wiki 腐烂的主要原因。
- **保持 `raw/` 纯净**：只放原始信息源，绝不让 LLM 修改。
- **即时更新 `CLAUDE.md`**：当你和 LLM 发现一个有效的约定后，立刻写进 CLAUDE.md。下次会话自动继承。
- **定期运行 Lint**：至少每周一次。报告会按日期保存，形成纵向质量记录。
- **使用 Git 管理 Wiki**：`raw/` 和 `wiki/` 都在版本控制下，每条变更都有迹可循。
- **多 Wiki 时用绝对路径做跨 Wiki 链接**：`~/...`，不依赖当前工作目录。

### ❌ DON'T

- **不要让 LLM 修改 `raw/`**：这是信息源的审计追踪，一旦被修改就无法追溯。
- **不要等到 Wiki 很大了才建索引**：从第一天就保持 index.md 更新。
- **过早拆分多 Wiki**：单个项目 Wiki 把一切放在一起并不比多 Wiki 差。只有当 (1) 有 2+ 个活跃项目共享概念、(2) 在不断跨项目重复解释模式、或 (3) 已有长期个人知识库时才添加全局 Wiki。
- **不要跨 Wiki 边界使用相对路径**：跨 Wiki 链接必须是绝对路径。

---

## 10. 常见问题

**Q: 和 RAG（检索增强生成）有什么区别？**
A: RAG 每次查询都重新检索原始文档，没有记忆。LLM Wiki 在摄入时就完成所有综合、交叉引用和矛盾标记，Wiki 是持久化产物，知识复合增长。

**Q: 我需要手动写 Wiki 页面吗？**
A: 几乎不需要。所有页面由 LLM 编写和维护。你只负责策展信息源和提出问题。

**Q: 支持哪些信息源格式？**
A: PDF、Markdown、纯文本、网页链接、转录稿。只要放进 `raw/` 目录就行。

**Q: 如果不小心让 LLM 改了 `raw/` 怎么办？**
A: `raw/` 应该在 Git 管理下。用 `git diff` 检查变更，`git checkout` 恢复。

**Q: 多久运行一次 Lint？**
A: 建议每周一次。报告按日期保存在 `wiki/reports/` 下，形成纵向质量记录。

**Q: 可以用于团队协作吗？**
A: 设计为个人使用，但因为整个 Wiki 就是 Git 仓库中的 Markdown 文件，你可以在团队中共享 repo。注意多人同时让 LLM 修改可能产生冲突。

**Q: 已有 Obsidian 知识库，能集成吗？**
A: 可以。使用 Multi-wiki 模式（见 5.7），将 Obsidian 仓库设为全局 Wiki，项目 Wiki 可以链接到全局 Wiki 的页面。

---

## 相关项目

- [Karpathy's LLM Wiki gist](https://gist.github.com/karpathy) — 原始想法
- [kfchou/wiki-skills](https://github.com/kfchou/wiki-skills) — 6 命令实现，包含 audit 模式
- [lewislulu/llm-wiki-skill](https://github.com/lewislulu/llm-wiki-skill) — Obsidian 原生实现，带 Web 预览服务器
- [agentskills.io](https://agentskills.io) — 开放标准规范

---

*文档版本：v1.0 | 基于 llm-wiki-manager v1.1 | MIT License*
