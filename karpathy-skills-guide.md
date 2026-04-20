# Andrej Karpathy Skills — 安装与使用指南

> 一个 CLAUDE.md 文件，来自 Andrej Karpathy 的 LLM 编程教训，显著提升 Claude Code 表现。

## 项目简介

Andrej Karpathy Skills 是一个针对 Claude Code 的行为指南，61,358 Stars，5,337 Forks。

源自 Andrej Karpathy 关于 LLM 编程陷阱的观察总结。LLM 在编程时容易：做错误假设却不检查、不管理自己的困惑、不主动澄清、不呈现权衡、不在应该反驳时反驳、过度设计代码和 API、随意修改不理解的内容。

这个项目把这些问题凝练成四个原则，写成一个 CLAUDE.md 文件，装进 Claude Code，显著减少无效代码和返工。

---

## 四大原则

| 原则 | 解决的问题 |
|------|---------|
| **Think Before Coding** | 错误假设、隐藏困惑、缺失权衡 |
| **Simplicity First** | 过度设计、臃肿抽象 |
| **Surgical Changes** | 正交编辑、碰了不该碰的代码 |
| **Goal-Driven Execution** | 测试优先、可验证的成功标准 |

### 原则一：Think Before Coding

**不假想，不隐藏困惑，主动呈现权衡。**

LLM 经常默默选择一个解读就开干。这个原则强制显式推理：

- **明确陈述假设** — 有不确定的地方，主动问而不是猜
- **呈现多个解读** — 存在歧义时不默默选一个
- **该反驳时主动反驳** — 如果存在更简单的方案，说出来
- **困惑时停下来** — 说出哪里不清楚，主动要求澄清

### 原则二：Simplicity First

**最小代码解决问题，不做 speculative 的东西。**

对抗过度工程的倾向：

- 不加入需求之外的功能
- 单次使用的代码不抽象
- 不加没被要求"灵活性"或"可配置性"
- 不处理不可能发生的异常场景
- 如果 200 行可以写成 50 行，重写

**检验标准：** 一个高级工程师会不会说这过度设计了？如果是，简化。

### 原则三：Surgical Changes

**只碰必须碰的，只清理自己造成的乱。**

编辑现有代码时：

- 不要"改进"相邻代码、注释或格式
- 不要重构没坏的东西
- 匹配现有风格，哪怕你会用不同方式写
- 发现无关死代码，提出来就行，不要删除

**检验标准：** 每个被修改的代码行都应该能直接追溯到用户的需求。

### 原则四：Goal-Driven Execution

**定义成功标准，循环直到验证通过。**

把指令性任务转化为可验证的目标：

| 不要说... | 转化为... |
|----------|----------|
| "加个验证" | "写无效输入的测试，然后让它们通过" |
| "修掉这个 bug" | "写一个复现它的测试，然后让测试通过" |
| "重构 X" | "确保重构前后测试都通过" |

多步骤任务中，陈述简要计划：

```
1. [步骤] → 验证：[检查点]
2. [步骤] → 验证：[检查点]
3. [步骤] → 验证：[检查点]
```

强成功标准让 LLM 能独立循环；弱标准（"让它能工作"）需要不断澄清。

---

## 安装

### 方式一：Claude Code 插件（推荐）

在 Claude Code 中添加市场：

```
/plugin marketplace add forrestchang/andrej-karpathy-skills
```

然后安装插件：

```
/plugin install andrej-karpathy-skills@karpathy-skills
```

这样指南会以插件形式安装，在所有项目中生效。

### 方式二：CLAUDE.md（按项目）

新项目：

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

已有项目（追加到现有 CLAUDE.md）：

```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

---

## 配合 Cursor 使用

项目里已经包含了一个 Cursor 项目规则（`.cursor/rules/karpathy-guidelines.mdc`），在 Cursor 里打开同一个项目时也能用。

详见 [CURSOR.md](https://github.com/forrestchang/andrej-karpathy-skills/blob/main/CURSOR.md)

---

## 判断是否在起作用

装上之后，如果看到以下变化，说明在起效：

- **Diff 里减少了不必要的改动** — 只出现被要求改的内容
- **减少因过度设计而返工** — 代码第一次就简洁
- **问题出现在实现之前** — 不是出错之后
- **PR 干净、少量改动** — 没有顺带重构或"改进"

---

## 自定义

这些指南设计为可以与项目具体指令合并。在现有的 CLAUDE.md 里添加，或创建新的。

项目专属规则示例：

```markdown
## 项目专属规则

- 使用 TypeScript strict 模式
- 所有 API 端点必须有测试
- 遵循 `src/utils/errors.ts` 中已有的错误处理模式
```

---

## 权衡说明

这些指南偏向**谨慎而非速度**。对于简单任务（改个 typo、明显的一行代码），自己判断——不是每个改动都需要完整流程。

目标是减少非平凡工作中的高成本错误，不是拖慢简单任务。

---

## 相关资源

| 资源 | 链接 |
|------|------|
| GitHub 仓库 | https://github.com/forrestchang/andrej-karpathy-skills |
| Andrej Karpathy 原文 | https://x.com/karpathy/status/2015883857489522876 |
| 作者 X（@jiayuan_jy） | https://x.com/jiayuan_jy |
| Multica（作者新项目） | https://github.com/multica-ai/multica |

---

*本指南由 马龙 🏓 整理 | 2026-04-20*
