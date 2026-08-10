# 今日 Agent 技巧 🛠️

**日期：2026-08-10**

---

## 技巧：给你的 Claude Code 装上「外接大脑」—— MCP 一键接入

如果你还在用 Claude Code 干瞪眼写代码，那你可能错过了一个大招：**MCP（Model Context Protocol）**。

简单说，MCP 就是 Claude Code 的「USB 接口」—— 插上线，它就能连上你的数据库、GitHub、Slack、甚至本地文件系统，直接在里面查数据、提 issue、发消息，不用你来回切换窗口。

### 怎么玩？

Claude Code 已经内置 MCP 支持，你只需要在配置文件里加一行：

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "你的token"
      }
    }
  }
}
```

然后重启 Claude Code，直接问它：

> "帮我看看我最近的 PR 有哪些还没合"

它就会直接调 GitHub API 给你列出来，还能帮你写 review comment。🫠

### 目前值得一试的 MCP Server

| Server | 用途 |
|--------|------|
| `server-github` | 读仓库、提 PR、写评论 |
| `server-postgres` | 直接查数据库，不用写 SQL 客户端 |
| `server-slack` | 在 Claude Code 里发 Slack 消息 |
| `server-filesystem` | 让 AI 安全地读写指定目录 |

### 为什么这个很重要？

以前 AI 写代码是「闭卷考试」—— 只看你给它的上下文。
现在有了 MCP，它是「开卷+可以打电话问人」—— 实时获取外部信息，做出来的东西更准、更贴实际业务。

而且**Kimi Code、Cursor 也在跟进 MCP 支持**，这个协议正在快速成为行业标准。早点上手，后面切工具也无缝。

---

## 小彩蛋 🥚

 Windsurf 最近更新了一个「Cascade」功能，可以自动分析你的代码库并生成 multi-file edits。实测在重构大型项目时，比手动一个个文件改快很多。如果你还没试过，值得花 10 分钟感受一下「AI 帮你跨文件脑补」是什么体验。

---

*明天见 👋*
