# Agent 的"项目说明书"——让它进门就知道规矩

> 2026-07-18 | 每日 Agent Tips

你有没有这种感觉：每次打开一个新项目问 agent 问题，它都要先"摸索"一阵子——

"这个项目的测试命令是什么？"
"代码风格是 tab 还是空格？"
"用什么包管理器，npm 还是 pnpm？"

你回答了十遍，换了个会话，它又忘了。🫠

其实你可以给 agent 一本**"进门说明书"**，让它每次进项目就自动知道所有规矩。

---

## 技巧一：Claude Code 的 `CLAUDE.md`

在项目根目录放一个 `CLAUDE.md`（或者 `.claude/CLAUDE.md`），Claude Code 会自动读取它。

**写什么进去？** 什么都行，比如：

```markdown
# 项目规范

## 技术栈
- Node.js 20 + TypeScript
- 包管理器：pnpm（不要用 npm）
- 测试框架：Vitest

## 代码风格
- 2 空格缩进
- 单引号
- 末尾不加分号

## 常用命令
- `pnpm dev` — 启动开发服务器
- `pnpm test` — 跑测试
- `pnpm lint` — 检查代码风格

## 特别注意
- 所有新功能必须写测试
- API 路由放在 `src/routes/` 下
- 不要直接操作数据库，用 `src/db/` 里的封装函数
```

然后你问 `"加一个新接口"`，它会：
- 自动用 pnpm 装依赖 ✓
- 把路由放对地方 ✓
- 记得写测试 ✓
- 用单引号和不加分号的风格 ✓

**不是它会读心，是你给了它说明书。**

---

## 技巧二：Cursor 的 `.cursorrules`

Cursor 用户同理，在项目根目录放 `.cursorrules`：

```
You are an expert TypeScript developer.

Rules:
- Prefer functional programming patterns
- Always use `const` instead of `let` when possible
- Write concise, self-documenting code
- When modifying code, preserve existing style

Project specifics:
- Use pnpm, not npm
- Tests are co-located with source files (*.test.ts)
- Never skip writing tests for new features
```

Cursor 的 Composer 和 Tab 补全会自动遵守这些规则。

---

## 技巧三：Kimi Code / Windsurf 通用玩法

就算你的工具不自动读文件，也有招：**在对话开头引用说明书**。

把项目规范存在一个文件里（比如 `AGENT_GUIDE.md`），每次新会话先丢一句：

> "请先阅读 `@AGENT_GUIDE.md`，然后帮我实现 [需求]"

Kimi Code 的 `@` 引用会直接读取文件内容，相当于手动给 agent "灌输"项目背景。

**进阶玩法：让 agent 自己写说明书**

如果你还没写，可以让 agent 帮你生成：

> "帮我分析这个项目，生成一份 AGENT_GUIDE.md，包含技术栈、代码规范、常用命令和项目结构说明。"

它生成的可能比你写的还全——毕竟它能看到所有文件。🤝

---

## 适用场景

| 场景 | 为什么需要说明书 |
|------|---------------|
| 团队协作 | 新成员（包括 agent）快速上手 |
| 多项目切换 | 不同项目有不同规范，agent 不会搞混 |
| 个人项目 | 三个月后再打开，agent 还能按你的习惯来 |
| 开源项目 | 贡献者（人 or AI）知道规矩再动手 |

---

## 一句话总结

> **Agent 不是记不住，是不知道要记什么。** 给它一本说明书，它就能从"通用实习生"变成"懂你家规矩的老员工"。

今天花 5 分钟写个 `CLAUDE.md` 或 `.cursorrules`，以后每次对话省 5 分钟解释——这账怎么算都赚。💀

---

*来源：Claude Code、Cursor、Kimi Code、Windsurf 通用技巧*
*生成时间：2026-07-18 08:42*
