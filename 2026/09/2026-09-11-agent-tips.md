# 2026-09-11 Agent Tips

## 今日主题：Claude Code 的 MCP——给 Agent 插上「外挂手」

**一句话总结：MCP 让 Claude Code 能直接操作你的数据库、API、本地工具，从此 Agent 不再只是「聊天框」，而是「全能接口」。**

---

### 技巧 1：MCP 是啥？Agent 的「USB-C 接口」

MCP（Model Context Protocol）是 Anthropic 搞的一个开放协议，简单说就是：**给 AI 一个标准插头，让它能插到你的各种工具上。**

以前你跟 Claude Code 说："帮我查一下数据库里最近 7 天的活跃用户"。
- 你需要：自己连数据库 → 跑 SQL → 把结果复制粘贴给 Claude → Claude 分析 → 你再手动执行建议。

现在有了 MCP：
- 你直接说："帮我查一下最近 7 天活跃用户趋势"
- Claude Code 自己连数据库 → 自己跑 SQL → 自己分析 → 直接给你结论。

**中间那堆复制粘贴，没了。**

---

### 技巧 2：给 Claude Code 配一个 MCP 服务器（以 PostgreSQL 为例）

Step 1：安装 MCP 服务器
```bash
npm install -g @modelcontextprotocol/server-postgres
```

Step 2：在 Claude Code 里添加 MCP 配置
```bash
claude mcp add postgres npx -y @modelcontextprotocol/server-postgres postgresql://localhost/mydb
```

Step 3：直接用自然语言操作数据库
```
我：最近 7 天注册用户数趋势咋样？

Claude：（自动连数据库 → 跑 SQL → 画图/分析）
「数据显示周三注册量突增 40%，可能跟那篇推文有关...」
```

> 🫠 以前要切三个窗口做的事，现在一句话搞定。这就是 MCP 的魔力。

---

### 技巧 3：除了数据库，还能插什么？

MCP 生态越来越丰富了，目前已有的「插头」包括：

| 类型 | MCP 服务器 | 能干啥 |
|---|---|---|
| 🗄️ 数据库 | Postgres、SQLite、MySQL | 直接查数据、生成报表 |
| 📁 文件系统 | Filesystem | 批量读写、目录分析 |
| 🌐 API | REST、GraphQL | 调外部接口、查天气/股价 |
| 🔍 搜索 | Brave、Tavily | 让 Agent 实时联网搜 |
| 🛠️ 工具 | GitHub、Slack | 自动提 PR、发消息 |

**Kimi Code、Cursor 也在跟进 MCP 支持**，估计不久就能跨工具通用。

---

### 一句话带走

> **MCP = 让 Agent 长出「手」和「眼」。以前 AI 只能动嘴，现在能动手——查数据库、调 API、读文件，全自己来。**
>
> 今天试试：`claude mcp add` 配一个你最常用的工具，感受下「一句话搞定」的爽感。

---

*Generated on 2026-09-11 for 向上 & 小惠* 🤝
