# Tracecat - AI 原生安全自动化平台 完全使用指南

> 原文项目：[TracecatHQ/tracecat](https://github.com/TracecatHQ/tracecat)  
> 文档版本：v1.0  
> 更新日期：2026-05-22

---

## 一、项目概述

### 1.1 什么是 Tracecat？

**Tracecat** 是面向团队和 AI Agent 的开源安全自动化平台，定位为 "AI 时代的 SOAR（安全编排、自动化与响应）"。

它由 Temporal 提供持久化工作流引擎，以 `nsjail` 沙箱为默认执行环境，让安全团队能够用自然语言或低代码方式构建端到端的自动化剧本——从威胁告警响应、漏洞管理到事件调查，全部由 AI Agent 和确定性工作流协同完成。

### 1.2 项目数据

| 指标 | 数值 |
|------|------|
| **GitHub Stars** | ⭐ 3,607+ |
| **Forks** | 359+ |
| **主要语言** | Python（FastAPI）+ Next.js（前端） |
| **开源协议** | AGPL-3.0（企业版功能为 EE License） |
| **首次发布** | 2024-02-27 |
| **活跃状态** | 持续更新 |

### 1.3 核心定位

- **Prompt-to-Automations**：用自然语言或 Agent 直接生成自动化剧本
- **Code-native**：从 Git 仓库同步自定义 Python 脚本作为工作流节点
- **All-in-one**：Agent + Workflow + Lookup Tables + Case Management 一体化
- **Self-host anywhere**：Docker Compose、Kubernetes、AWS Fargate 均可部署
- **Sandboxed-by-default**：所有不可信代码和 Agent 默认在 `nsjail` 沙箱中运行

---

## 二、核心功能

### 2.1 四大核心模块

| 模块 | 说明 | 典型场景 |
|------|------|----------|
| **🤖 Agents** | 基于提示词、工具集和 MCP 服务器的自定义 AI Agent | 自动调查告警、生成报告 |
| **🔀 Workflows** | 低代码构建器，支持复杂控制流（条件分支、循环） | 告警响应流水线、漏洞修复流程 |
| **📋 Case Management** | 工单跟踪、自动化处理、团队协作 | 安全事件工单生命周期管理 |
| **🔌 Integrations** | 100+ 预构建连接器 | 对接 SIEM、EDR、邮件、Slack 等企业工具 |

### 2.2 其他亮点

- **MCP Server**：支持通过 Model Context Protocol 与 Claude Code、Codex 等 IDE Agent 对接
- **Custom Registry**：把团队内部的 Python 脚本注册为 Agent 工具或 Workflow Action
- **Lookup Tables**：结构化数据存储与查询，替代简单数据库
- **Variables**：跨 Workflow 和 Agent 复用变量值
- **No SSO Tax**：原生支持 SAML / OIDC，不额外收费
- **Audit Logs**：可导出到外部 SIEM 做合规审计

### 2.3 企业版特性（付费）

- **细粒度访问控制**：RBAC + ABAC + OAuth2.0 Scopes（人类和 Agent 均可授权）
- **Human-in-the-loop**：敏感操作需人工审批（统一收件箱、Slack、邮件通知）
- **工作流版本控制**：与 GitHub/GitLab/Bitbucket 同步
- **监控指标**：Workflow、Agent、Case 的全链路可观测性

---

## 三、技术架构

### 3.1 服务拓扑（Docker Compose 模式）

Tracecat 单节点部署由 **16 个容器** 组成，仅 `caddy` 暴露外部端口：

| 服务 | 端口 | 职责 |
|------|------|------|
| `caddy` | 80（可配置） | 反向代理 + TLS 终止 |
| `api` | 8000 | REST API + Webhook 接收器 |
| `ui` | 3000 | Next.js 前端 |
| `worker` | — | 工作流编排器（Temporal Worker） |
| `executor` | — | Action 执行器（可选 nsjail 沙箱） |
| `agent-worker` | — | AI Agent 编排器 |
| `agent-executor` | — | AI Agent Action 执行器 |
| `litellm` | 4000 | LLM 网关，统一管理多模型请求 |
| `mcp` | 8099 | MCP Server，供 IDE 集成 |
| `postgres_db` | 5432 | 业务数据库（PostgreSQL 16） |
| `temporal` | 7233 | Temporal 工作流引擎 |
| `temporal_postgres_db` | 5432 | Temporal 数据库（PostgreSQL 13） |
| `temporal_ui` | 8081（不暴露） | Temporal Web UI |
| `minio` | 9000/9001 | S3 兼容对象存储 |
| `redis` | 6379 | 缓存 + 消息队列 |
| `migrations` | — | 一次性数据库迁移 |

### 3.2 技术栈

| 层级 | 技术 |
|------|------|
| 后端 | Python + FastAPI + SQLAlchemy + Pydantic + uv |
| 前端 | Next.js + TypeScript + React Query + Shadcn UI |
| 工作流引擎 | Temporal（持久化执行、容错、重试） |
| 沙箱 | nsjail（Linux Namespace + Jail） |
| 数据库 | PostgreSQL 16 |
| 对象存储 | MinIO（S3 兼容） |
| LLM 网关 | LiteLLM |

---

## 四、安装指南

### 4.1 前置要求

- **Docker** 26.0.0+
- **Docker Compose** 2.29.0+
- **openssl**（用于生成密钥）
- 最低资源：8 核 CPU / 16 GB RAM（生产环境推荐 32 核 / 64 GB）

### 4.2 快速安装（Docker Compose）

#### 步骤 1：下载配置模板

```bash
# 创建部署目录
mkdir tracecat && cd tracecat

# 下载环境生成脚本
curl -o env.sh https://raw.githubusercontent.com/TracecatHQ/tracecat/1.0.0-beta.48/env.sh

# 下载 .env 模板（env.sh 依赖此文件生成最终配置）
curl -o .env.example https://raw.githubusercontent.com/TracecatHQ/tracecat/1.0.0-beta.48/.env.example

# 下载 Caddy 反向代理配置
curl -o Caddyfile https://raw.githubusercontent.com/TracecatHQ/tracecat/1.0.0-beta.48/Caddyfile

# 下载 Docker Compose 文件
curl -o docker-compose.yml https://raw.githubusercontent.com/TracecatHQ/tracecat/1.0.0-beta.48/docker-compose.yml
```

#### 步骤 2：生成环境变量

```bash
chmod +x env.sh && ./env.sh
```

脚本会交互式提示：
- `PUBLIC_APP_URL`：访问地址，默认 `localhost`
- PostgreSQL SSL 模式：默认关闭
- **首个超级管理员邮箱**（必填，用于初始化管理员账号）

`env.sh` 会自动生成以下核心密钥（请勿丢失）：

| 密钥 | 用途 | 生成方式 |
|------|------|----------|
| `TRACECAT__DB_ENCRYPTION_KEY` | 数据库敏感字段加密 | `python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"` |
| `TRACECAT__SERVICE_KEY` | 内部服务认证 | `openssl rand -hex 32` |
| `TRACECAT__SIGNING_SECRET` | Webhook 签名 | `openssl rand -hex 32` |
| `USER_AUTH_SECRET` | 密码重置/验证令牌 | `openssl rand -hex 32` |

#### 步骤 3：启动服务

```bash
docker compose up -d
```

首次启动需要拉取多个镜像并初始化数据库，大约 3-5 分钟。

#### 步骤 4：访问系统

| 端点 | 地址 |
|------|------|
| Web UI | `http://localhost:80`（或你配置的 `PUBLIC_APP_URL`） |
| API 文档 | `http://localhost:80/api/docs` |
| MCP Server | `http://localhost:80/mcp` |
| Temporal UI | `http://localhost:8081`（如需暴露） |

使用步骤 2 中填写的邮箱注册首个账号，自动获得超级管理员权限。

### 4.3 关键环境变量说明

编辑 `.env` 文件可调整以下常用配置：

| 变量 | 说明 | 默认值/示例 |
|------|------|-------------|
| `PUBLIC_APP_PORT` | 对外端口 | `80` |
| `PUBLIC_APP_URL` | 外部访问地址 | `http://localhost` |
| `TRACECAT__AUTH_TYPES` | 认证方式 | `basic`（可选 `oidc,saml`） |
| `TRACECAT__AUTH_SUPERADMIN_EMAIL` | 首个管理员邮箱 | 必填 |
| `TRACECAT__AUTH_ALLOWED_DOMAINS` | 限制注册域名 | 留空表示不限制 |
| `TRACECAT__AUTH_MIN_PASSWORD_LENGTH` | 最小密码长度 | `12` |
| `TRACECAT__EXECUTOR_BACKEND` | 执行器后端 | `direct` |
| `TRACECAT__DISABLE_NSJAIL` | 禁用 nsjail 沙箱 | `true`（开发环境） |
| `TRACECAT__LITELLM_BASE_URL` | LLM 网关地址 | `http://litellm:4000` |

### 4.4 生产环境建议

1. **启用 SSO**：将 `TRACECAT__AUTH_TYPES` 改为 `oidc` 或 `saml`，配置对应的 Issuer/Client ID
2. **启用 HTTPS**：配置 Caddyfile 使用 TLS 证书（Let's Encrypt 或自有证书）
3. **启用 nsjail**：生产环境设置 `TRACECAT__DISABLE_NSJAIL=false`
4. **独立数据库**：使用外部托管 PostgreSQL（如 AWS RDS）替代容器内数据库
5. **外部对象存储**：使用 AWS S3 / MinIO 集群替代单节点 MinIO

---

## 五、使用手册

### 5.1 工作流（Workflow）自动化

#### 创建第一个工作流

1. 进入 Web UI → **Automations** → **Workflows**
2. 点击 **New Workflow**，输入名称（如 `Phishing-Response`）
3. 在工作流编辑器中拖拽节点：
   - **Trigger**：选择 Webhook（接收告警）或 Schedule（定时）
   - **Action**：从 100+ 集成中选择（如 Send Email、Query SIEM、Create Jira Ticket）
   - **Control Flow**：添加 If-Condition 分支、For-Each 循环
4. 点击 **Save** 并 **Enable**

#### 核心 Action 类型

| 类型 | 示例 |
|------|------|
| **HTTP** | 调用任意 REST API |
| **Email** | SMTP 发送告警邮件 |
| **Chat** | Slack / Discord / Teams 通知 |
| **Case** | 自动创建/更新 Case |
| **Data** | 查询 Lookup Table、操作变量 |
| **AI** | 调用 LLM 分析文本、生成摘要 |
| **Custom** | 执行注册到 Registry 的 Python 脚本 |

### 5.2 AI Agent 配置

#### 创建自定义 Agent

1. 进入 **Agents** → **New Agent**
2. 填写系统提示词（System Prompt），定义 Agent 角色：
   ```
   你是一个安全事件调查员。接收告警后，你需要：
   1. 查询相关日志
   2. 判断威胁等级
   3. 生成处置建议
   ```
3. **Tools**：勾选该 Agent 可使用的工具（HTTP、Database、SIEM Query 等）
4. **Model**：选择底层 LLM（通过 LiteLLM 网关接入 OpenAI、Claude、DeepSeek 等）
5. 保存后可在 Workflow 中调用，或通过 MCP 在 IDE 中使用

#### MCP 服务器集成

Tracecat 自带 MCP Server，支持 Claude Code、Codex、Cursor 等 IDE Agent 直接操作：

```bash
# 在 IDE Agent 中配置 MCP
cat > ~/.cursor/mcp.json << 'EOF'
{
  "mcpServers": {
    "tracecat": {
      "url": "http://localhost:80/mcp",
      "env": {
        "TRACECAT_API_KEY": "your-service-key"
      }
    }
  }
}
EOF
```

### 5.3 Case 管理

1. **自动创建 Case**：在工作流中使用 **Create Case** Action
2. **分配与跟踪**：在 **Cases** 面板查看所有工单状态（Open / In Progress / Resolved）
3. **Agent 协作**：将 Case 分配给 AI Agent 自动调查，或人工介入审批
4. **关联证据**：自动附加日志、截图、分析结果到 Case

### 5.4 Lookup Tables

用于存储结构化参考数据（如 IP 黑名单、资产清单、规则映射）：

1. 进入 **Tables** → **New Table**
2. 定义 Schema（列名、类型）
3. 可通过 Workflow Action 查询或更新

示例场景：告警触发时，查询 Lookup Table 判断 IP 是否在白名单中，决定是否升级。

### 5.5 Custom Registry（自定义脚本）

将内部 Python 脚本注册为平台工具：

```python
# tracecat_registry/tools/my_tool.py
from tracecat_registry import registry

@registry.register(
    description="查询威胁情报",
    namespace="custom"
)
def query_threat_intel(ioc: str) -> dict:
    """输入 IP/域名/Hash，返回威胁情报评分"""
    # 你的查询逻辑
    return {"score": 85, "sources": ["VirusTotal", "AbuseIPDB"]}
```

提交到 Git 仓库后，Tracecat 自动同步并暴露为 Workflow Action 和 Agent Tool。

---

## 六、更新与维护

### 6.1 升级 Tracecat

```bash
# 1. 下载新版本迁移脚本和模板
curl -o env-migration.sh https://raw.githubusercontent.com/TracecatHQ/tracecat/<version>/env-migration.sh
curl -o .env.example https://raw.githubusercontent.com/TracecatHQ/tracecat/<version>/.env.example

# 2. 执行迁移（会自动备份旧 .env）
chmod +x env-migration.sh && ./env-migration.sh

# 3. 下载最新 Compose 和 Caddyfile
curl -o docker-compose.yml https://raw.githubusercontent.com/TracecatHQ/tracecat/<version>/docker-compose.yml
curl -o Caddyfile https://raw.githubusercontent.com/TracecatHQ/tracecat/<version>/Caddyfile

# 4. 重启
docker compose up -d
```

⚠️ **警告**：升级前务必备份 `.env` 中的核心密钥（`SERVICE_KEY`、`SIGNING_SECRET`、`DB_ENCRYPTION_KEY`），丢失将导致凭证和 Webhook 失效。

### 6.2 日志排查

```bash
# 查看所有服务日志
docker compose logs -f

# 查看指定服务
docker compose logs -f api
docker compose logs -f worker
docker compose logs -f temporal

# 进入 API 容器调试
docker compose exec api bash
```

---

## 七、开源 vs 企业版

| 功能 | 开源版 (AGPL-3.0) | 企业版 (EE License) |
|------|-------------------|---------------------|
| Workflow + Agent + Case | ✅ | ✅ |
| 100+ 集成 | ✅ | ✅ |
| nsjail 沙箱 | ✅ | ✅ |
| MCP Server | ✅ | ✅ |
| Custom Registry | ✅ | ✅ |
| SSO (OIDC/SAML) | ✅ | ✅ |
| RBAC / ABAC | ❌ | ✅ |
| Human-in-the-loop | ❌ | ✅ |
| Git 同步版本控制 | ❌ | ✅ |
| 监控仪表板 | ❌ | ✅ |
| 专业技术支持 | 社区 | 商业 |

---

## 八、相关资源

| 资源 | 链接 |
|------|------|
| 官方仓库 | https://github.com/TracecatHQ/tracecat |
| 官方文档 | https://docs.tracecat.com |
| Discord 社区 | https://discord.gg/H4XZwsYzY4 |
| 官方网站 | https://tracecat.com |
| 企业版咨询 | https://cal.com/team/tracecat |

---

*本文档由开源社区整理，遵循 AGPL-3.0 精神。安全自动化，从 Tracecat 开始。*
