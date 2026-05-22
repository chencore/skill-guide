# mattpocock/skills 安装与使用完全指南

> Matt Pocock 是前 Vercel 工程师、TypeScript 专家，tRPC 和 @total-typescript/shoehorn 作者。这个 Skills 库是他每天真正在用的工程化 Agent 技能集——不是" vibe coding "，是真实工程师的工作流。

**GitHub**: https://github.com/mattpocock/skills
**作者**: @mattpocock (前 Vercel, tRPC 核心维护者)
**收录 Skills**: 20+ 个
**安装量**: ~60,000 开发者订阅

---

## 安装方式

### 方式一：逐个安装指定 Skill

```bash
npx skills@latest add mattpocock/skills/<skill-name>
```

### 方式二：查看所有可用 Skills

```bash
npx skills@latest list mattpocock/skills
```

---

## Skills 分类总览

| 分类 | Skills |
|------|--------|
| 规划与设计 | `to-prd` `to-issues` `grill-me` `design-an-interface` `request-refactor-plan` |
| 开发与代码 | `tdd` `triage-issue` `improve-codebase-architecture` `migrate-to-shoehorn` `scaffold-exercises` |
| 工具链配置 | `setup-pre-commit` `git-guardrails-claude-code` |
| 写作与知识管理 | `write-a-skill` `edit-article` `ubiquitous-language` `obsidian-vault` |
| 通用辅助 | `qa` `caveman` `domain-model` `zoom-out` |

---

## 详细安装与使用说明

---

### 一、规划与设计类

---

#### 1. to-prd — 对话转 PRD

**安装**
```bash
npx skills@latest add mattpocock/skills/to-prd
```

**做什么**
把当前对话上下文直接合成一份 PRD，以 GitHub Issue 形式提交。不需要额外访谈，直接基于已有信息生成。

**何时用**
- 项目开始前，把讨论内容固化成文档
- 需求模糊时，让 Agent 先出个初稿再细化
- 想把对话结论存档到 GitHub Issue

**使用示例**
```
用 to-prd 把我们刚才讨论的支付流程整理成 PRD
```

**输出**
自动创建 GitHub Issue，包含：
- 目标与背景
- 功能模块拆分（Deep Modules 形式）
- 接口设计建议
- 测试优先级

---

#### 2. to-issues — PRD 转独立 Issue

**安装**
```bash
npx skills@latest add mattpocock/skills/to-issues
```

**做什么**
把已有的 PRD、计划或 spec 拆解成独立可领取的 GitHub Issue，采用"纵向切片"（vertical slices）方式。

**何时用**
- PRD 写完后需要拆成具体开发任务
- 需要给团队分配工作时
- 想把大需求拆成独立可测试的小任务

**核心概念：纵向切片**
不是按层拆分（前端/后端/数据库），而是按用户行为拆分：
```
❌ 错误：拆成 "写前端" "写后端" "写数据库"
✅ 正确：拆成 "用户登录流程" "加入购物车" "结算下单"
```

**使用示例**
```
用 to-issues 把 #42 的 PRD 拆成独立任务
```

---

#### 3. grill-me — 强制追问设计审查

**安装**
```bash
npx skills@latest add mattpocock/skills/grill-me
```

**做什么**
对你的计划或设计进行"连珠炮式追问"，直到每个决策树的分支都被覆盖。不放过任何模糊地带。

**何时用**
- 设计初期想全面审视方案
- 感觉计划有漏洞但说不清在哪
- 需要外部视角强制挑刺

**工作方式**
一次只问一个问题，给出推荐答案后等你确认：
```
我：我想用微前端架构
Agent：为什么选微前端而不是模块化单体？
     你们的团队规模和技术栈迁移成本考虑过吗？
     先回答第一个：为什么选微前端？
```

**使用示例**
```
grill me on 这个微前端方案
```

---

#### 4. design-an-interface — 接口多方案设计

**安装**
```bash
npx skills@latest add mattpocock/skills/design-an-interface
```

**做什么**
为一个模块生成多个完全不同的接口设计，用并行 sub-agents 同时探索多条路径。

**何时用**
- 某个核心模块接口还没确定
- 想看不同设计范式的对比
- 需要在几个方案间做技术选型

**使用示例**
```
用 design-an-interface 为购物车模块设计接口
```

---

#### 5. request-refactor-plan — 重构计划生成

**安装**
```bash
npx skills@latest add mattpocock/skills/request-refactor-plan
```

**做什么**
通过用户访谈生成详细的重构计划，拆分到最小提交单元，然后作为 GitHub Issue 提交。

**何时用**
- 需要重构但不知从哪下手
- 想把重构变成可控的小步骤
- 重构前需要团队 review 计划

**使用示例**
```
帮我用 request-refactor-plan 看看这个模块的重构方案
```

---

### 二、开发与代码类

---

#### 6. tdd — 测试驱动开发

**安装**
```bash
npx skills@latest add mattpocock/skills/tdd
```

**做什么**
红-绿-重构循环的 TDD 工作流，每次只做一个纵向切片（一个测试 → 一个实现 → 重构）。

**核心原则**
- 测试验证**行为**而非**实现细节**
- 通过公共接口测试，不 mock 内部组件
- 一次只写一个测试，一个实现

**工作流程**
```
RED:    写一个测试 → 测试失败
GREEN:  写最小代码 → 测试通过
REFACTOR: 测试全通过后重构
```

**❌ 错误方式（横向切片）**
```
RED:   一次性写完所有测试
GREEN: 一次性写完所有代码
→ 结果：测试写的是"想象中的行为"，不是真实需求
```

**✅ 正确方式（纵向切片）**
```
RED→GREEN: test1 → impl1
RED→GREEN: test2 → impl2
RED→GREEN: test3 → impl3
...每次只做一个，根据前一个的结果调整
```

**何时用**
- 新功能开发
- Bug 修复
- 任何需要确保代码可测试、可维护的场景

**使用示例**
```
用 tdd 帮我实现用户认证模块
```

---

#### 7. triage-issue — Bug 调查与Issue生成

**安装**
```bash
npx skills@latest add mattpocock/skills/triage-issue
```

**做什么**
调查 Bug 时会先探索代码库定位根因，然后生成一份基于 TDD 修复计划的 GitHub Issue。

**何时用**
- 收到 Bug 报告不知道从哪查
- 需要给团队写一个可执行的 Bug Issue
- 想在修复前先形成清晰的测试计划

**工作方式**
1. 探索代码库找根因
2. 确定需要测什么
3. 生成带 TDD 修复计划的 Issue

**使用示例**
```
triage-issue #1023 用户无法登出
```

---

#### 8. improve-codebase-architecture — 架构深化

**安装**
```bash
npx skills@latest add mattppock/skills/improve-codebase-architecture
```

**做什么**
在代码库中寻找可以"深化"的模块，参考 `CONTEXT.md` 的领域语言和 `docs/adr/` 中的架构决策。

**核心概念：Deep Module**
- 小接口 + 深实现 = Deep Module（好）
- 大接口 + 浅实现 = Shallow Module（差）

**何时用**
- 代码库混乱，想做系统性优化
- 接口太大太杂，想收窄
- 想让复杂逻辑集中在少数模块中

**使用示例**
```
用 improve-codebase-architecture 看看我们前端目录
```

---

#### 9. migrate-to-shoehorn — TypeScript 类型迁移

**安装**
```bash
npx skills@latest add mattpocock/skills/migrate-to-shoehorn
```

**做什么**
把 TypeScript 测试文件中的 `as` 类型断言迁移到 `@total-typescript/shoehorn`，获得更严格的类型校验。

**何时用**
- 现有项目想从 `as` 升级到更安全的类型方案
- 使用 `@total-typescript/shoehorn` 库的项目

**使用示例**
```
用 migrate-to-shoehorn 迁移 auth 模块的测试文件
```

---

#### 10. scaffold-exercises — 练习题脚手架生成

**安装**
```bash
npx skills@latest add mattpocock/skills/scaffold-exercises
```

**做什么**
创建包含章节、问题、解答和解析的练习目录结构，适合知识库建设和教学场景。

**何时用**
- 建立技术课程体系
- 团队内部知识沉淀
- 需要给学习者提供结构化练习

**使用示例**
```
用 scaffold-exercises 帮我生成 TypeScript 泛型练习
```

---

### 三、工具链配置类

---

#### 11. setup-pre-commit — Pre-commit Hook 配置

**安装**
```bash
npx skills@latest add mattpocock/skills/setup-pre-commit
```

**做什么**
一次性配置好：
- **Husky** pre-commit hook
- **lint-staged** + **Prettier**（自动格式化暂存文件）
- **typecheck** + **test**（提交前类型检查和测试）

**何时用**
- 新项目初始化
- 团队代码规范落地
- 想让每次提交都自动检查格式和类型

**使用示例**
```
setup-pre-commit
```

---

#### 12. git-guardrails-claude-code — 危险 Git 命令拦截

**安装**
```bash
npx skills@latest add mattpocock/skills/git-guardrails-claude-code
```

**做什么**
设置 PreToolUse 钩子，拦截以下危险操作：
- `git push` / `git push --force`
- `git reset --hard`
- `git clean -f` / `-fd`
- `git branch -D`
- `git checkout .` / `git restore .`

**何时用**
- 担心 Agent 误操作破坏代码
- 多团队协作时保护主分支
- 给自己留一层安全网

**使用示例**
```
git-guardrails-claude-code
```

---

### 四、写作与知识管理类

---

#### 13. write-a-skill — 创建新 Skill

**安装**
```bash
npx skills@latest add mattpocock/skills/write-a-skill
```

**做什么**
教你如何从头创建一个结构完整的 Agent Skill，包含 SKILL.md 模板和文件组织规范。

**Skill 标准结构**
```
skill-name/
├── SKILL.md           # 主指令（必需）
├── REFERENCE.md       # 详细文档（可选）
├── EXAMPLES.md        # 使用示例（可选）
└── scripts/           # 工具脚本（可选）
    └── helper.js
```

**SKILL.md 格式要求**
```yaml
---
name: skill-name
description: 简短描述Capability。Use when [触发关键词].
---

# Skill Name

## Quick start
[最小可工作示例]

## Workflows
[复杂任务的步骤清单]

## Advanced features
[链接到其他文件]
```

**何时用**
- 想把自己的工作流封装成可复用的 Skill
- 给团队创建标准化的 Agent 工作流
- 任何需要固化最佳实践的场景

**使用示例**
```
write-a-skill 帮我创建一个代码审查 Skill
```

---

#### 14. edit-article — 文章编辑优化

**安装**
```bash
npx skills@latest add mattpocock/skills/edit-article
```

**做什么**
对文章进行重构：调整段落结构、提升清晰度、精简表达。

**何时用**
- 写完初稿后想进一步打磨
- 觉得文章不够清晰或太长
- 需要给文章减肥

**使用示例**
```
用 edit-article 优化一下我刚写的技术博客
```

---

#### 15. ubiquitous-language — DDD 通用语言提取

**安装**
```bash
npx skills@latest add mattpocock/skills/ubiquitous-language
```

**做什么**
从当前对话中提取 DDD 风格的通用语言词汇表，统一团队对核心概念的术语认知。

**何时用**
- DDD 架构设计时
- 团队对术语理解不一致
- 想建立项目专属的词汇表

**使用示例**
```
ubiquitous-language 提取一下我们讨论中的核心术语
```

---

#### 16. obsidian-vault — Obsidian 笔记管理

**安装**
```bash
npx skills@latest add mattpocock/skills/obsidian-vault
```

**做什么**
搜索、创建和管理 Obsidian 笔记，支持 wikilinks 和索引笔记。

**笔记规范**
- 位置：`/mnt/d/Obsidian Vault/AI Research/`
- 命名：Title Case
- 组织：不用文件夹，用 wikilinks + 索引笔记

**何时用**
- 想把学习研究内容沉淀到 Obsidian
- 需要和已有的 Obsidian 知识库联动

**使用示例**
```
在 obsidian-vault 里搜一下 AI Agent 相关的笔记
```

---

### 五、通用辅助类

---

#### 17. qa — 问答模式

**安装**
```bash
npx skills@latest add mattpocock/skills/qa
```

**做什么**
进入问答模式，专门回答技术问题。

---

#### 18. caveman — 简单优先

**安装**
```bash
npx skills@latest add mattpocock/skills/caveman
```

**做什么**
始终选择最简单的实现方案，不提前优化，直到被数据证明必须复杂化。

---

#### 19. domain-model — 领域模型构建

**安装**
```bash
npx skills@latest add mattpocock/skills/domain-model
```

**做什么**
帮助构建 DDD 风格的领域模型，提取实体、值对象和聚合根。

---

#### 20. zoom-out — 宏观视角

**安装**
```bash
npx skills@latest add mattpocock/skills/zoom-out
```

**做什么**
跳出当前细节，从更高维度审视方案，问"我们为什么要做这个"。

---

## 推荐组合配置

### 日常开发标配（推荐）
```bash
npx skills@latest add mattpocock/skills/tdd
npx skills@latest add mattpocock/skills/grill-me
npx skills@latest add mattpocock/skills/setup-pre-commit
npx skills@latest add mattpocock/skills/git-guardrails-claude-code
```

### 需求与架构类
```bash
npx skills@latest add mattpocock/skills/to-prd
npx skills@latest add mattpocock/skills/to-issues
npx skills@latest add mattpocock/skills/design-an-interface
npx skills@latest add mattpocock/skills/improve-codebase-architecture
```

### 知识管理类
```bash
npx skills@latest add mattpocock/skills/write-a-skill
npx skills@latest add mattpocock/skills/obsidian-vault
npx skills@latest add mattpocock/skills/ubiquitous-language
```

---

## 核心设计理念

### 纵向切片（Vertical Slices）
这是 mattpocock Skills 库最核心的概念——不按技术层层拆分（前端/后端/DB），而是按用户行为纵向切片。每个切片可以独立测试、独立交付。

### Deep Module
好模块 = 小接口 + 深实现。不追求大而全的接口，把复杂度封装在简单的接口后面。

### 不提前设计（You Can't Outrun Your Headlights）
不要一次性把设计定死，根据前一个切片的反馈调整下一个，盲区会随着交付逐步缩小。

### 行为测试，而非实现测试
测试验证的是"用户能用它做什么"，而不是"代码是怎么实现的"。

---

## 总结

这个 Skills 库的核心价值：**把 Matt Pocock 十几年工程经验压缩成可复用的 Agent 工作流**。

不是教你怎么用 AI 聊天，是教你怎么用 AI 做真实的工程输出。

安装量 ~60,000 开发者订阅不是吹的——它是目前最接近"真实工程师 workflow"的 Agent Skills 集合。

**官方链接**: https://github.com/mattpocock/skills
**Newsletter**: https://www.aihero.dev/s/skills-newsletter (~60,000 开发者订阅)
