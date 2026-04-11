# 女娲 (Nuwa Skill) 安装与使用指南

> 「你想蒸馏的下一个员工，何必是同事。」
> —— 蒸馏任何人的思维方式：心智模型、决策启发式、表达DNA。

## 什么是女娲？

女娲（Nuwa Skill）是一个 Claude Code Skill，由花叔（@AlchainHust）开发。它的核心能力是**从公开信息中提取一个人的认知操作系统**——不是复述他说过的话，而是提炼他**怎么想**、**怎么判断**、**怎么表达**。

简单说：输入一个名字，女娲自动完成调研、提炼、验证全流程，生成一个可运行的人物 Skill。

### 它蒸馏了什么？

女娲提取五层认知架构：

| 层次 | 说明 |
|------|------|
| 怎么说话 | 表达DNA——语气、节奏、用词偏好 |
| 怎么想 | 心智模型、认知框架 |
| 怎么判断 | 决策启发式 |
| 什么不做 | 反模式、价值观底线 |
| 知道局限 | 诚实边界 |

### 它不是什么

- **不是角色扮演**：不是让 AI 模仿名人的语气说话，而是用名人的认知框架分析问题
- **不是语录合集**：不是复读名人说过的话，而是用他们的思维模型推断对新问题的立场
- **不是万能的**：框架能提取，灵感不能；只基于公开信息；是某个时间点的快照

---

## 安装

### 前置条件

- [Claude Code](https://claude.ai/code) 已安装
- Node.js 环境（用于 `npx` 命令）

### 一键安装

```bash
npx skills add alchaincyf/nuwa-skill
```

安装完成后，Skill 文件会出现在 `.claude/skills/` 目录下。

### 安装单个预置人物 Skill

女娲已经预蒸馏了 13 位人物 + 1 个主题，每个都可以独立安装：

```bash
# 创业/产品/思维类
npx skills add alchaincyf/paul-graham-skill       # Paul Graham
npx skills add alchaincyf/zhang-yiming-skill       # 张一鸣
npx skills add alchaincyf/steve-jobs-skill         # 乔布斯
npx skills add alchaincyf/elon-musk-skill          # 马斯克
npx skills add alchaincyf/naval-skill              # 纳瓦尔

# 投资与思维类
npx skills add alchaincyf/munger-skill             # 芒格
npx skills add alchaincyf/taleb-skill              # 塔勒布

# 学习与科学类
npx skills add alchaincyf/feynman-skill            # 费曼

# AI/工程类
npx skills add alchaincyf/karpathy-skill           # Karpathy
npx skills add alchaincyf/ilya-sutskever-skill     # Ilya Sutskever

# 内容创作类
npx skills add alchaincyf/mrbeast-skill            # MrBeast
npx skills add alchaincyf/x-mentor-skill           # X导师（主题Skill）

# 其他
npx skills add alchaincyf/trump-skill              # 特朗普
npx skills add alchaincyf/zhangxuefeng-skill       # 张雪峰
```

---

## 使用方法

### 方式一：蒸馏新人物

在 Claude Code 中直接输入：

```
蒸馏一个保罗·格雷厄姆
```

或：

```
造一个张小龙的视角Skill
```

女娲会自动执行四步流程：

1. **六路并行采集**：著作、播客/访谈、社交媒体、批评者视角、决策记录、人生时间线
2. **三重验证提炼**：每个心智模型必须通过跨域复现 + 生成力 + 排他性验证
3. **构建 Skill**：3-7 个心智模型 + 5-10 条决策启发式 + 表达DNA + 反模式 + 诚实边界
4. **质量验证**：用已知的公开回答测试，再用未知问题测试

### 方式二：模糊需求（不知道蒸馏谁）

如果你不知道该蒸馏谁，可以描述你的困惑：

```
我想提升决策质量
```

```
有没有一种思维方式能帮我看透商业本质
```

女娲会诊断你的需求，推荐 2-3 个最适合的人物或主题。

### 方式三：调用已安装的 Skill

蒸馏完成后，直接用该人物的视角分析问题：

```
用芒格的视角帮我分析这个投资决策
```

```
费曼会怎么解释量子计算？
```

```
切换到 Naval，我在纠结三件事
```

---

## 使用示例

### 示例1：用 Naval 视角分析精力分配

**用户输入：**
> 我同时想做自媒体、写书、做独立开发，精力完全不够

**Naval Skill 回复：**
> 你列了三个欲望。每个欲望都是你跟不快乐签的一份合同。
> 不是精力不够，是合同太多。
>
> 问你自己：哪一个做起来你会忘记时间？
> 那个就是你的特定知识所在。
>
> 不是三选一。是先一，再一，再一。
> 串行复利，不是并行内耗。

**背后的心智模型：** Naval 的「欲望即合同」和「特定知识 + 杠杆 = 复利」

---

### 示例2：用马斯克视角分析获客成本

**用户输入：**
> 我们的SaaS产品获客成本太高了

**马斯克 Skill 回复：**
> 先别想怎么降。先算物理极限。
> 获客的最小必要动作是什么？用户从知道你到付费，
> 信息传递的理论最短路径是什么？
>
> 现在你的实际路径是理论路径的几倍？
> 如果超过3倍，中间一定有可以砍掉的步骤。
> 不是优化漏斗，是质疑漏斗本身该不该存在。

**背后的心智模型：** 马斯克的「渐近极限法」

---

### 示例3：用张雪峰视角做职业规划

**用户输入：**
> 家里条件一般，孩子想学金融，去不了顶尖学校，该不该报？

**张雪峰 Skill 回复：**
> 千万别报。金融不是你学出来的，金融是你爹妈给你带出来的。
> 顶尖学校的金融，人家同学是行长的儿子、基金经理的女儿，人脉就是资源。
> 你普通家庭去个普通学校学金融，毕业出来干什么？卖保险、跑银行柜台、做电销。
> 先谋生再谋爱，家里没矿就选技术类专业，学个硬本事，靠自己吃饭。

**背后的心智模型：** 张雪峰的「ROI教育观」和「阶层流动现实主义」

---

## 工作原理详解

### 心智模型的三重验证

一个观点要被收录为心智模型，必须同时满足：

1. **跨域复现**：同一思维框架出现在此人讨论的至少 2 个不同领域
2. **生成力**：用这个模型可以推断此人对新问题的可能立场
3. **排他性**：不是所有聪明人都会这样想，体现此人的独特视角

只通过 1 重 → 降级为「决策启发式」
0 重都没过 → 不纳入

### 表达DNA的量化

从原始素材中提取：

| 维度 | 测量方式 |
|------|---------|
| 平均句长 | 字数/句数 |
| 疑问句比例 | 疑问句数/总句数 |
| 类比密度 | 类比数/千字 |
| 确定性语气 | 「一定」「显然」vs「也许」「可能」 |
| 转折频率 | 「但是」「然而」/千字 |

风格标签从以下维度标注：正式↔口语、抽象↔具体、谨慎↔断言、学术↔通俗

---

## 本地语料模式

如果你手上有此人的一手素材（书籍 PDF、演讲 transcript、视频字幕等），可以直接提供给女娲：

```
蒸馏一个段永平，我手上有他的投资访谈合集
```

本地语料比网络搜索的质量更高，会标记为「本地语料模式」，采集策略会优先使用你提供的材料。

---

## 项目结构

```
nuwa-skill/
├── SKILL.md                          # 女娲本体（核心Skill文件）
├── references/
│   ├── extraction-framework.md       # 提炼方法论详解
│   └── skill-template.md             # 生成Skill的模板
└── examples/                         # 13个人物 + 1个主题
    ├── steve-jobs-perspective/       # 乔布斯（含实战对话记录）
    ├── paul-graham-perspective/      # Paul Graham
    ├── zhang-yiming-perspective/     # 张一鸣
    ├── andrej-karpathy-perspective/  # Karpathy
    ├── ilya-sutskever-perspective/   # Ilya Sutskever
    ├── trump-perspective/            # 特朗普
    ├── mrbeast-perspective/          # MrBeast
    ├── elon-musk-perspective/        # 马斯克
    ├── munger-perspective/           # 查理·芒格
    ├── feynman-perspective/          # 费曼
    ├── naval-perspective/            # Naval Ravikant
    ├── taleb-perspective/            # 塔勒布
    ├── zhangxuefeng-perspective/     # 张雪峰
    └── x-mastery-mentor/             # X导师（主题Skill）
```

每个 example 都包含完整的调研文件，可以看到信息怎么被收集、筛选、变成心智模型。

---

## 注意事项

1. **蒸馏需要时间**：六路并行采集 + 三重验证，完整流程可能需要几分钟
2. **基于公开信息**：只能提炼此人公开表达过的思维模式，不等于真实想法
3. **时间快照**：捕捉的是调研时间点的认知，不包含后续变化
4. **直觉不可蒸馏**：框架能提取，灵感和直觉不能
5. **不是万能顾问**：每个 Skill 都标注了做不到什么，使用前请阅读诚实边界

---

## 链接

- **GitHub 仓库**：https://github.com/alchaincyf/nuwa-skill
- **作者 Twitter**：https://x.com/AlchainHust
- **官网**：https://bookai.top
- **许可证**：MIT（随便用，随便改，随便造）

---

*文档生成时间：2026-04-11*
