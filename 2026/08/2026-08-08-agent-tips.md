# 🤖 Agent 小抄 · 2026-08-08

> 向上、小惠早！今天聊一个**Claude Code 的隐藏大招**——很多人知道 `.cursorrules`，但不知道 Claude Code 有个更猛的 `claude.md`。

---

## 💡 今日技巧：Claude Code 的 `claude.md` 是「项目灵魂文件」

在 Claude Code 里，你可以在项目根目录放一个 `claude.md`（或 `.claude/CLAUDE.md`），Claude 会**自动读取并记住里面的内容**，不需要你每次手动 `@` 引用。

### 和 `.cursorrules` 的区别？

| | `.cursorrules` | `claude.md` |
|---|---|---|
| 作用范围 | 代码生成风格 | **整个对话上下文** |
| 能写什么 | 代码规范、技术栈 | 代码规范 + 业务逻辑 + 项目背景 + 常用命令 |
| 更新方式 | 手动维护 | Claude 可以**自己更新** |

### 具体怎么玩？

在 `claude.md` 里写这些，效果拉满：

```markdown
# 项目背景
这是一个基于 FastAPI 的 Agent 编排框架，核心概念是 Task → Step → Action 的层级结构。

## 技术约束
- Python 3.11+，异步优先
- 数据库：PostgreSQL + asyncpg，不用 ORM 用原始 SQL
- 测试：pytest，覆盖率要求 80%+

## 常用命令
- 运行测试：pytest tests/ -v
- 启动 dev 环境：docker compose up -d postgres redis
- 代码检查：ruff check . && ruff format .

## 重要业务规则
- Task 状态机：pending → running → completed | failed | cancelled
- 不允许跨 Task 共享 Step 数据
- 所有外部 API 调用必须走 circuit breaker 包装
```

### 进阶玩法：让 Claude 自己维护这个文件

你可以在对话里说：

> "记住，我们这个项目所有 API 返回都必须包装成 `{success: bool, data: T, error?: string}` 的格式，更新到 claude.md 里。"

Claude 会**自动修改 `claude.md`**，下次进项目时直接生效。相当于给项目装了一个**自动进化的记忆体**。

> 以前：每个新会话都要重新交代项目背景
> 现在：Claude 打开项目就知道「哦，这是那个 Agent 框架」，直接开干

---

## 🎁 加餐：Windsurf Cascade 的「时光机」功能

Windsurf 里按 `Cmd/Ctrl + Shift + P` 搜 **"View Checkpoints"**，你会看到 Cascade 每次编辑前都偷偷存了一个**代码快照**。

意思是：AI 改崩了？一键回滚到 5 分钟前的状态，**不用慌慌张张找 git stash**。

> 特别适合那种"让 AI 帮忙重构一下"然后看着满屏报错心跳加速的时刻 💀

---

*今日份 tips 收工。明天见 👋*
