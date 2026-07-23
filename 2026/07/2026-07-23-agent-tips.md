# 给 Agent 装个"肌肉记忆"

> 2026-07-23 | daily agent tips

你有没有过这种体验——

每次新开一个 Claude Code 会话，都要先打字："请用中文回答"、"我们项目用 Bun 不是 npm"、"记得跑测试再提交"……

说累了。真的。

其实你的 Agent 完全可以记住这些，你只需要给它写一个**项目级系统提示词**。

---

## 🧠 Claude Code：CLAUDE.md

在项目根目录放一个叫 `CLAUDE.md`（或者 `.claude/CLAUDE.md`）的文件，Claude Code 每次启动都会自动读它。

里面可以写啥？举个小例子：

```markdown
# 项目习惯

- 用中文交流，但代码注释保持英文
- 包管理器用 pnpm，别碰 npm
- 提交前必须跑 `pnpm test`，通过才能推
- 项目用 ESM，不写 CommonJS
- 函数注释用 JSDoc，类型用 TypeScript strict mode
```

下次打开 Claude Code，它直接就知道你的规矩，不用再废话。

**小彩蛋**：这个文件支持 Markdown，写多长都行。有用户把整个架构决策记录（ADR）丢进去，Claude Code 直接变成"资深老员工"。

---

## 🎯 Cursor：.cursorrules

Cursor 用户也有同款——项目根目录放 `.cursorrules` 文件。

格式更简单，纯文本就行：

```
Always use TypeScript strict mode.
Prefer functional components over class components.
Use pnpm for package management.
Run tests before suggesting commits.
```

Cursor 的 Agent 模式（Composer Agent）会主动读这个文件，然后按你的规矩干活。

---

## 💡 进阶玩法

**别只写代码规范**，试试这些：

- **常用命令速查**："部署用 `fly deploy`，别问我为什么"
- **项目黑话词典**："'老模块'指 `legacy/` 目录下的任何东西"
- **踩坑记录**："`lib/crypto.ts` 那堆正则别碰，上次改完修了三天"

本质上，这就是在给 Agent 写** onboarding 文档**——只不过读者是个 AI。

---

## 一句话总结

> 与其每次重复唠叨，不如写个 CLAUDE.md / .cursorrules，让 Agent 自己长记性。

毕竟，好工具不值得被废话浪费额度。

---

*明天聊点啥？或许说说怎么让多个 Agent 打配合——Claude 写，Cursor Review，Kimi 兜底。*
