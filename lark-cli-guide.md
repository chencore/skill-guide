# Lark-CLI 安装与使用指南

> 本文档基于 [larksuite/cli](https://github.com/larksuite/cli) 官方仓库编写  
> 项目地址：https://github.com/larksuite/cli  
> 许可证：MIT  
> 适用版本：v1.0+

---

## 目录

1. [项目简介](#项目简介)
2. [功能特性](#功能特性)
3. [安装指南](#安装指南)
4. [快速配置](#快速配置)
5. [认证登录](#认证登录)
6. [基础使用](#基础使用)
7. [三层命令体系](#三层命令体系)
8. [AI Agent 技能](#ai-agent-技能)
9. [进阶用法](#进阶用法)
10. [安全警告](#安全警告)
11. [常见问题](#常见问题)

---

## 项目简介

**lark-cli** 是 [Lark（飞书）](https://www.larksuite.com/) 官方推出的命令行工具，由 larksuite 团队维护。它同时面向**人类用户**和 **AI Agent** 设计，覆盖飞书核心业务能力域：

- 即时通讯 (Messenger)
- 文档 (Docs)
- 表格 (Base / Sheets)
- 日历 (Calendar)
- 邮件 (Mail)
- 任务 (Tasks)
- 会议 (Meetings)
- 幻灯片 (Slides)
- 知识库 (Wiki)
- 审批 (Approval)
- OKR
- 考勤 (Attendance)
- 云盘 (Drive)

包含 **200+ 命令** 和 **26 个 AI Agent Skills**，可直接被 Claude Code、Codex、OpenClaw 等 AI 工具调用。

---

## 功能特性

| 特性 | 说明 |
|------|------|
| 🤖 **Agent 原生设计** | 24 个结构化 Skill 开箱即用，兼容主流 AI 工具 |
| 📦 **广泛覆盖** | 18 个业务域，200+ 精选命令 |
| 🎯 **AI 优化** | 每个命令都经过真实 Agent 测试，参数精简、结构化输出 |
| 🔓 **开源零门槛** | MIT 许可证，`npm install` 即用 |
| ⚡ **3 分钟上手** | 一键创建应用、交互式登录、3 步完成首次 API 调用 |
| 🔐 **安全可控** | 输入注入防护、终端输出脱敏、OS 原生密钥链凭证存储 |
| 🏗️ **三层架构** | 快捷命令 → API 命令 → 原始 API，按需选择粒度 |

---

## 安装指南

### 前置要求

安装前请确认系统已安装：

- **Node.js**（附带 `npm` / `npx`）
- **Go v1.23+** 和 **Python 3**（仅源码编译时需要）

### 安装方式

选择以下任一方式安装：

#### 方式一：npm 安装（推荐）

```bash
npx @larksuite/cli@latest install
```

#### 方式二：从源码编译

```bash
git clone https://github.com/larksuite/cli.git
cd cli
make install

# 安装 CLI SKILL（必需）
npx skills add larksuite/cli -y -g
```

#### 方式三：直接调用（无需全局安装）

```bash
npx @larksuite/cli@latest <command>
```

> **提示**：如果 `lark-cli` 命令不可用，请确保 `npx` 缓存已刷新，或使用 `npx @larksuite/cli@latest` 前缀执行。

---

## 快速配置

### 步骤 1：初始化应用配置

```bash
lark-cli config init
```

执行后会启动交互式向导，引导你完成：

1. **选择平台**：Lark（国际版）或 Feishu（国内版）
2. **创建/选择应用**：如果没有应用，会自动创建一个新的开发者应用
3. **获取凭证**：自动配置 App ID 和 App Secret
4. **配置域名和权限**：根据需求选择业务域

> **AI Agent 模式**：加上 `--new` 参数可在后台运行，输出生成授权 URL 供用户浏览器中完成。
>
> ```bash
> lark-cli config init --new
> ```

### 步骤 2：登录认证

```bash
# 交互式登录（TUI 引导选择权限域和级别）
lark-cli auth login

# 推荐常用权限（自动选择常用范围）
lark-cli auth login --recommend

# 指定权限域
lark-cli auth login --domain calendar,task,im

# 精确权限
lark-cli auth login --scope "calendar:calendar:read"
```

登录采用 **OAuth 2.0** 流程，需要在浏览器中确认授权。授权成功后，凭证会安全存储在系统密钥链中。

### 步骤 3：验证状态

```bash
lark-cli auth status
```

显示当前登录状态、已授权权限域、身份信息等。

---

## 认证登录

### 认证命令一览

| 命令 | 说明 |
|------|------|
| `auth login` | OAuth 登录（交互式或 CLI 参数指定权限） |
| `auth logout` | 登出并删除存储的凭证 |
| `auth status` | 显示当前登录状态和已授权权限 |
| `auth check` | 验证特定权限（退出码 0 = 正常，1 = 缺失） |
| `auth scopes` | 列出应用可用的所有权限 |
| `auth list` | 列出所有已认证的用户 |

### 身份切换

支持以**用户身份**或**机器人身份**执行命令：

```bash
# 以用户身份执行
lark-cli calendar +agenda --as user

# 以机器人身份执行
lark-cli im +messages-send --as bot --chat-id "oc_xxx" --text "Hello"
```

### 无等待模式（Agent 友好）

适合 AI Agent 自动化场景，立即返回验证 URL，不阻塞：

```bash
lark-cli auth login --domain calendar --no-wait

# 稍后恢复轮询
lark-cli auth login --device-code <DEVICE_CODE>
```

---

## 基础使用

### 快捷命令（Shortcuts）

以 `+` 为前缀，面向人类和 AI 友好设计，带智能默认值和表格输出：

```bash
# 查看今日日程
lark-cli calendar +agenda

# 发送消息
lark-cli im +messages-send --chat-id "oc_xxx" --text "Hello"

# 创建文档
lark-cli docs +create --api-version v2 --doc-format markdown --content $'<title>周报</title>\n# 进展\n- 完成特性 X'

# 创建表格
lark-cli sheets +create --title "销售数据"

# 查看任务列表
lark-cli task +list
```

运行 `lark-cli <service> --help` 查看所有快捷命令。

### 常用业务域命令示例

#### 📅 日历 (Calendar)

```bash
# 查看日程
lark-cli calendar +agenda

# 创建会议
lark-cli calendar +create \
  --title "项目评审" \
  --start-time "2026-05-22T10:00:00+08:00" \
  --end-time "2026-05-22T11:00:00+08:00" \
  --attendees "user1@company.com,user2@company.com"

# 查找会议室
lark-cli calendar +rooms --capacity 10

# 回复邀请
lark-cli calendar +rsvp --event-id "xxx" --status accepted
```

#### 💬 即时通讯 (Messenger)

```bash
# 发送消息到群聊
lark-cli im +messages-send --chat-id "oc_xxx" --text "Hello"

# 回复消息
lark-cli im +reply --message-id "om_xxx" --text "收到"

# 上传图片
lark-cli im +upload --file "./screenshot.png" --chat-id "oc_xxx"

# 搜索消息
lark-cli im +search --query "项目进度"
```

#### 📄 文档 (Docs)

```bash
# 创建文档
lark-cli docs +create --title "技术方案" --content "# 概述\n本文档描述..."

# 读取文档
lark-cli docs +read --doc-token "doccnxxx"

# 更新文档
lark-cli docs +update --doc-token "doccnxxx" --content "# 更新后的内容"

# 搜索文档
lark-cli docs +search --query "API 设计"
```

#### 📊 多维表格 (Base)

```bash
# 列出表格
lark-cli base +tables --app-token "basexxx"

# 添加记录
lark-cli base +record-create \
  --app-token "basexxx" \
  --table-id "tblxxx" \
  --fields '{"姓名": "张三", "状态": "已完成"}'

# 查询记录
lark-cli base +records --app-token "basexxx" --table-id "tblxxx"
```

#### 📈 电子表格 (Sheets)

```bash
# 创建表格
lark-cli sheets +create --title "财务数据"

# 读取单元格
lark-cli sheets +read --spreadsheet-token "shtcnxxx" --range "Sheet1!A1:D10"

# 写入数据
lark-cli sheets +write \
  --spreadsheet-token "shtcnxxx" \
  --range "Sheet1!A1" \
  --values '[["产品", "销量", "收入"], ["A", 100, 5000]]'
```

#### ✅ 任务 (Tasks)

```bash
# 创建任务
lark-cli task +create --title "完成代码审查" --due-time "2026-05-23T18:00:00"

# 查看任务列表
lark-cli task +list

# 完成任务
lark-cli task +complete --task-id "xxx"
```

#### 📧 邮件 (Mail)

```bash
# 查看收件箱
lark-cli mail +inbox

# 发送邮件
lark-cli mail +send \
  --to "recipient@company.com" \
  --subject "周报" \
  --body "请查收本周工作报告"
```

---

## 三层命令体系

lark-cli 提供三个粒度层级，覆盖从快捷操作到完全自定义 API 调用：

### 第一层：快捷命令 (Shortcuts)

```bash
lark-cli calendar +agenda
lark-cli im +messages-send --chat-id "oc_xxx" --text "Hello"
lark-cli docs +create --title "周报"
```

- 以 `+` 前缀
- 智能默认值、表格输出、试运行预览
- 适合日常高频操作

### 第二层：API 命令

```bash
lark-cli calendar calendars list
lark-cli calendar events instance_view --params '{"calendar_id":"primary","start_time":"1700000000","end_time":"1700086400"}'
```

- 从 Lark OAPI 元数据自动生成
- 100+ 命令，1:1 映射平台端点
- 适合精确控制参数

### 第三层：原始 API 调用

```bash
lark-cli api GET /open-apis/calendar/v4/calendars

lark-cli api POST /open-apis/im/v1/messages \
  --params '{"receive_id_type":"chat_id"}' \
  --data '{"receive_id":"oc_xxx","msg_type":"text","content":"{\"text\":\"Hello\"}"}'
```

- 直接调用任何 Lark 开放平台端点
- 覆盖 2500+ API
- 适合高级定制和调试

---

## AI Agent 技能

lark-cli 内置 **26 个 AI Agent Skills**，可直接被 Claude Code、Codex、OpenClaw 等 AI 工具调用：

| Skill | 描述 |
|-------|------|
| `lark-shared` | 应用配置、认证登录、身份切换、权限管理（所有其他 Skill 自动加载） |
| `lark-calendar` | 日历事件（创建/更新）、日程视图、空闲查询、时间建议、会议室查找 |
| `lark-im` | 发送/回复消息、群聊管理、消息搜索、上传/下载文件、表情回应 |
| `lark-doc` | 创建、读取、更新、搜索文档（基于 Markdown） |
| `lark-drive` | 上传、下载文件，管理权限和评论 |
| `lark-markdown` | 创建、获取、补丁、覆盖云盘原生 Markdown 文件 |
| `lark-sheets` | 创建、读取、写入、追加、查找、导出电子表格 |
| `lark-slides` | 创建和管理演示文稿，读取内容，添加/删除幻灯片 |
| `lark-base` | 多维表格：表、字段、记录、视图、仪表盘、数据分析 |
| `lark-task` | 任务、任务列表、子任务、提醒、成员分配 |
| `lark-mail` | 浏览、搜索、阅读邮件，发送、回复、转发、草稿管理 |
| `lark-contact` | 按姓名/邮箱/电话搜索用户，获取用户资料 |
| `lark-wiki` | 知识空间、节点、文档管理 |
| `lark-event` | 实时事件订阅（WebSocket），正则路由和 Agent 友好格式 |
| `lark-vc` | 搜索会议记录，查询会议纪要（摘要、待办、转录） |
| `lark-whiteboard` | 白板/图表 DSL 渲染 |
| `lark-minutes` | 会议纪要元数据和 AI 产物（摘要、待办、章节） |
| `lark-openapi-explorer` | 从官方文档探索底层 API |
| `lark-skill-maker` | 自定义 Skill 创建框架 |
| `lark-attendance` | 查询个人考勤打卡记录 |
| `lark-approval` | 查询审批任务，批准/驳回/转交 |
| `lark-workflow-meeting-summary` | 工作流：会议纪要聚合和结构化报告 |
| `lark-workflow-standup-report` | 工作流：日程和待办摘要 |
| `lark-okr` | 查询、创建、更新 OKR |

### AI Agent 快速接入

**OpenClaw 示例：**

```bash
# 安装 Skill
npx skills add larksuite/cli -y -g

# 安装特定业务域 Skill
npx skills add larksuite/lark-calendar -y
npx skills add larksuite/lark-im -y
npx skills add larksuite/lark-doc -y
```

**在 AI 会话中使用：**

```
用户：帮我查看今天的会议安排
AI: [调用 lark-calendar +agenda]

用户：给项目群发一条进度更新
AI: [调用 lark-im +messages-send]

用户：创建一个周报文档
AI: [调用 lark-doc +create]
```

---

## 进阶用法

### 输出格式控制

```bash
--format json      # 完整 JSON 响应（默认）
--format pretty    # 人类友好的格式化输出
--format table     # 可读表格
--format ndjson    # 换行分隔 JSON（管道处理）
--format csv       # CSV 格式
```

### 分页处理

```bash
--page-all                  # 自动分页获取所有页面
--page-limit 5              # 最多 5 页
--page-delay 500            # 页面请求间隔 500ms
```

### 试运行 (Dry Run)

对于可能有副作用的命令，先预览请求：

```bash
lark-cli im +messages-send --chat-id oc_xxx --text "hello" --dry-run
```

### Schema 内省

查看任意 API 方法的参数、请求体、响应结构、支持的身份和权限：

```bash
# 列出所有可用 schema
lark-cli schema

# 查看特定方法
lark-cli schema calendar.events.instance_view
lark-cli schema im.messages.delete
```

### 工作流组合示例

**每日早报生成：**

```bash
#!/bin/bash
# morning-report.sh

# 1. 获取今日日程
AGENDA=$(lark-cli calendar +agenda --format json)

# 2. 获取待办任务
TASKS=$(lark-cli task +list --format json)

# 3. 生成报告并发送到群聊
REPORT="📅 今日安排\n$AGENDA\n\n✅ 待办任务\n$TASKS"

lark-cli im +messages-send \
  --chat-id "oc_xxx" \
  --text "$REPORT"
```

**会议纪要自动归档：**

```bash
# 1. 获取会议元数据和 AI 摘要
MINUTES=$(lark-cli lark-minutes +get --minutes-id "xxx")

# 2. 创建文档归档
lark-cli docs +create \
  --title "会议纪要 - $(date +%Y-%m-%d)" \
  --content "$MINUTES"
```

---

## 安全警告

⚠️ **使用必读**

此工具可被 AI Agent 调用来自动化操作 Lark/Feishu 开放平台，存在以下固有风险：

| 风险类型 | 说明 | 防护措施 |
|---------|------|---------|
| **模型幻觉** | AI 可能误解指令，执行错误操作 | 输入注入防护、命令确认机制 |
| **不可预测执行** | Agent 可能连锁触发未预期行为 | 终端输出脱敏、沙箱限制 |
| **提示注入** | 恶意输入可能劫持 Agent 行为 | 默认安全策略、权限最小化 |
| **数据泄露** | 授权后 Agent 可在权限范围内访问数据 | OS 原生密钥链存储、不打印凭证 |

### 安全最佳实践

1. **不要主动修改默认安全设置** — 放宽限制会显著增加风险
2. **作为私人对话助手使用** — 不要将机器人加入群聊或允许其他用户交互
3. **权限最小化原则** — 仅授权必要的权限域
4. **定期审查授权** — 使用 `lark-cli auth status` 检查已授权范围
5. **敏感操作确认** — 对删除、发送、权限修改等操作要求二次确认

---

## 常见问题

### Q1: 安装后 `lark-cli` 命令找不到？

**A**: 确保 Node.js 和 npm 已正确安装，并且 npm 全局 bin 目录在 PATH 中：

```bash
# 检查 npm 全局安装路径
npm bin -g

# 临时使用 npx
npx @larksuite/cli@latest <command>

# 或手动添加到 PATH
export PATH="$(npm bin -g):$PATH"
```

### Q2: 认证登录后提示权限不足？

**A**: 使用 `lark-cli auth scopes` 查看应用支持的权限，然后重新登录授权：

```bash
# 查看可用权限
lark-cli auth scopes

# 重新登录并指定权限
lark-cli auth login --scope "calendar:calendar:read,im:message:send"
```

### Q3: 如何切换 Lark 和 Feishu？

**A**: 在 `config init` 时选择平台，或使用环境变量：

```bash
# 指定平台
lark-cli config init --platform lark    # 国际版
lark-cli config init --platform feishu  # 国内版
```

### Q4: AI Agent 调用失败？

**A**: 检查以下几点：

1. 确保已安装对应 Skill：`npx skills add larksuite/lark-<domain> -y`
2. 确认已登录：`lark-cli auth status`
3. 检查权限：`lark-cli auth check --scope "<required-scope>"`
4. 使用 `--format json` 获取结构化输出

### Q5: 如何查看命令详细用法？

**A**: 

```bash
# 查看服务级帮助
lark-cli calendar --help

# 查看具体命令帮助
lark-cli calendar +agenda --help

# 使用 schema 查看 API 定义
lark-cli schema calendar.events.list
```

### Q6: 能否批量操作？

**A**: 可以，结合 shell 脚本或管道：

```bash
# 批量发送消息
cat recipients.txt | while read chat_id; do
  lark-cli im +messages-send --chat-id "$chat_id" --text "通知：明天全员会议"
done

# 批量导出数据
lark-cli base +records --app-token "xxx" --page-all --format csv > data.csv
```

---

## 参考资源

- **官方仓库**: https://github.com/larksuite/cli
- **Lark 开放平台**: https://open.larksuite.com/
- **Feishu 开放平台**: https://open.feishu.cn/
- **npm 包**: https://www.npmjs.com/package/@larksuite/cli
- **Issue 反馈**: https://github.com/larksuite/cli/issues
- **Pull Request**: https://github.com/larksuite/cli/pulls

---

## 许可证

本项目采用 **MIT License** 开源。运行时会调用 Lark/Feishu 开放平台 API，使用这些 API 需要遵守以下协议：

- [Feishu 用户服务协议](https://www.feishu.cn/terms)
- [Feishu 隐私政策](https://www.feishu.cn/privacy)
- [Lark User Terms of Service](https://www.larksuite.com/user-terms-of-service)
- [Lark Privacy Policy](https://www.larksuite.com/privacy-policy)

---

*本文档最后更新：2026-05-21*
