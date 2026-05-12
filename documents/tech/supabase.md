# Supabase 全面介绍

## 什么是 Supabase？

Supabase 是一个**开源的 Firebase 替代品**，基于 PostgreSQL 构建，提供完整的后端即服务（BaaS）能力。

```
Supabase = PostgreSQL + 实时订阅 + 认证 + 存储 + Edge Functions + 向量数据库
```

### 核心组件

| 组件 | 技术基础 | 作用 |
| --- | --- | --- |
| Database | PostgreSQL | 关系型数据库 |
| Auth | GoTrue | 用户认证（支持 OAuth/SSO） |
| Storage | S3 兼容 | 文件存储 |
| Realtime | Phoenix WebSocket | 实时数据同步 |
| Edge Functions | Deno | 无服务器函数 |
| **Vector** | **pgvector** | **AI 向量存储** |

---

## Supabase 在 AI 时代的角色

### 核心价值：成为 AI 应用的数据底座

```
LLM (Claude/GPT/Gemini)
         ↕
    AI 应用层
         ↕
    Supabase
   ┌────────────────────┐
   │ pgvector 向量存储   │  ← RAG 知识库
   │ PostgreSQL 结构数据 │  ← 业务数据
   │ Realtime 实时推送  │  ← 流式对话
   │ Auth 身份认证      │  ← 用户管理
   │ Storage 文件存储   │  ← 文档/图片
   └────────────────────┘
```

### 1. RAG（检索增强生成）的理想后端

```sql
-- 存储文档向量
CREATE TABLE documents (
  id UUID PRIMARY KEY,
  content TEXT,
  embedding VECTOR(1536)  -- OpenAI/Claude embedding
);

-- 语义相似度搜索
SELECT content, 1 - (embedding <=> query_embedding) AS similarity
FROM documents
ORDER BY embedding <=> query_embedding
LIMIT 5;
```

### 2. AI Agent 的记忆系统

- 存储对话历史、用户偏好
- 长期记忆持久化
- 跨会话上下文检索

### 3. 实时 AI 应用

- AI 回复流式推送到前端
- 多用户协作 AI 工作流
- 实时数据变更触发 AI 处理

---

## 通过 MCP 与 Supabase 交互

### MCP（Model Context Protocol）是什么？

MCP 是 Anthropic 提出的协议，让 AI（如 Claude）能够**直接操作外部工具和服务**，Supabase 官方提供了 MCP Server。

### 连接方式

```json
// Claude Desktop / Claude Code 配置
{
  "mcpServers": {
    "supabase": {
      "command": "npx",
      "args": ["-y", "@supabase/mcp-server-supabase@latest"],
      "env": {
        "SUPABASE_ACCESS_TOKEN": "your-personal-access-token"
      }
    }
  }
}
```

### 通过 MCP 能做什么

**数据库操作**

> 你：帮我查询过去 7 天注册的用户数量，按天分组
>
> Claude：[直接查询 Supabase] → 返回结果 + 可视化

**Schema 管理**

> 你：给 orders 表加一个 shipping_status 字段，枚举类型
>
> Claude：[执行 ALTER TABLE] → 完成并确认

**Edge Functions 部署**

> 你：写一个处理 Stripe webhook 的 Edge Function 并部署
>
> Claude：[编写代码 → 调用 MCP 部署] → 给出函数 URL

### 完整的 AI 开发工作流

```
1. 设计数据库结构  →  Claude 直接建表
2. 生成 RLS 策略   →  Claude 编写并应用行级安全
3. 查询调试        →  Claude 直接执行 SQL 验证
4. 数据迁移        →  Claude 生成并执行迁移脚本
5. 性能分析        →  Claude 查看慢查询并优化索引
```

---

## 高阶功能

### 1. Row Level Security（行级安全）

```sql
-- 用户只能看自己的数据，Supabase 原生支持
CREATE POLICY "users_own_data" ON documents
  FOR ALL USING (auth.uid() = user_id);
```

AI 平台中每个用户的私有知识库天然隔离，无需应用层过滤。

---

### 2. pgvector 混合搜索

```sql
-- 同时做语义搜索 + 关键词搜索（混合检索）
SELECT *,
  ts_rank(to_tsvector(content), query) AS keyword_score,
  1 - (embedding <=> $1) AS vector_score
FROM documents,
  plainto_tsquery($2) query
WHERE to_tsvector(content) @@ query
ORDER BY (keyword_score + vector_score) DESC;
```

比纯向量搜索精度更高，是生产级 RAG 的标准做法。

---

### 3. Realtime + AI 流式输出

```javascript
// 前端订阅 AI 回复实时推送
supabase
  .channel('ai-response')
  .on('postgres_changes', {
    event: 'UPDATE',
    table: 'conversations'
  }, (payload) => {
    appendToken(payload.new.latest_token)
  })
  .subscribe()
```

---

### 4. Database Webhooks → AI 触发器

```
数据库新增记录
    ↓
Supabase Webhook
    ↓
Edge Function
    ↓
调用 Claude API 处理
    ↓
结果写回数据库
```

实现**数据驱动的 AI 自动化**，如新订单自动生成摘要、新文档自动建立索引。

---

### 5. 与 Keycloak 集成（企业 SSO 场景）

```
用户登录 Keycloak（SSO）
    ↓
获取 JWT Token
    ↓
Supabase 验证 JWT（自定义 JWT Secret）
    ↓
RLS 策略基于 JWT claims 生效
```

企业 SSO + Supabase 数据权限完美结合。

---

## 总结

```
传统时代：Supabase = 快速构建 CRUD 应用的后端

AI 时代：  Supabase = AI 应用的全栈数据基础设施
             向量存储 + 结构数据 + 实时 + 认证 + 函数
             一个平台解决 AI 应用 90% 的后端需求
```

对于正在建设的企业 AI 平台，**Supabase + Keycloak** 的组合是非常主流且成熟的企业级架构选择。

---

*最后更新：2026-05-12*
