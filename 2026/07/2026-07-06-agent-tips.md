# 🤖 Agent 技巧日报 — 2026-07-06

> 今日主题：**给 Agent 一本「项目说明书」—— 让它一进项目就懂规矩**
>
> 适合：多项目切换、团队协作、不想每次重复交代背景的人

---

## 技巧一：`.cursorrules` / `.claude.md` —— Agent 的入职手册

你有没有觉得，每次新开一个 Agent 对话，都要重新交代一遍：

> "这个项目用 Bun 不是 Node"  
> "我们组件库用 shadcn，样式用 Tailwind"  
> "API 调用统一走 `/api/v2`，别用旧版"  
> "提交信息用英文，遵循 conventional commits"

累不累？

**解法：在项目根目录放一个规则文件。**

| 工具 | 文件名 | 作用 |
|------|--------|------|
| Cursor | `.cursorrules` | 项目级系统提示 |
| Claude Code | `.claude.md` | 项目上下文和偏好 |
| Windsurf | `.windsurfrules` | 同上 |

Agent 每次加载项目时，会自动读取这些文件，相当于**「入职第一天，先读员工手册」**。

---

### 实战示例：`.cursorrules`

```markdown
# 项目规范

## 技术栈
- Runtime: Bun (不是 Node.js)
- 框架: React + TypeScript
- 样式: Tailwind CSS + shadcn/ui 组件库
- API: 统一走 `/api/v2/*`，废弃 `/api/v1/`

## 代码风格
- 优先使用函数组件 + Hooks
- 类型定义用 `interface`，不用 `type`
- 异步逻辑统一用 `async/await`，不用 `.then()`
- 提交信息遵循 conventional commits: `feat:`, `fix:`, `docs:`

## 注意事项
- 不要修改 `/shared/config.ts`，它由 CI 自动生成
- 新增页面需要同时更新 `routes.config.ts`
- 测试文件放在 `__tests__/` 目录，用 Vitest
```

放好这个文件后，你对 Cursor 说：

> "加个用户设置页面"

它自动就知道：用 React TS、Tailwind、走 `/api/v2/`、测用 Vitest。
**你一句话都没多交代。**

---

## 技巧二：分层配置 —— 全局规矩 + 项目规矩

如果你用多个项目，可以搞两层：

**全局层（放在你的 home 目录）：**

```markdown
# ~/.cursorrules_global

- 我是中国人，用中文交流
- 解释技术概念时，先给直觉，再给细节
- 写代码前先简述思路，不要直接输出大段代码
- 报错时，先分析根因，再给修复方案
```

**项目层（放在项目根目录）：**

```markdown
# ./.cursorrules

- 本项目用 Python + FastAPI
- 数据库用 PostgreSQL，ORM 用 SQLAlchemy 2.0
- 环境变量从 `.env.local` 读取，不要硬编码
```

Cursor 会自动合并两层规则，**全局偏好 + 项目细节**，双管齐下。

---

## 💡 一句话总结

> **花 10 分钟写个 `.cursorrules`，省掉 100 次 "我们项目用..." 的开场白。**

向上、小惠，你们要是还没写这个文件，今天就是最佳时机 👋

---

*P.S. 规则文件不是越全越好，5-10 条最关键的就行。太长了 Agent 也会「眼花」。*
