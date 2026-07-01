# 2026-07-01 Agent 技巧：给 AI 立规矩，项目记忆不翻车

> 向上、小惠，今天教你们怎么让 Agent "记住" 你们的项目偏好，不用每次开场白。

## 技巧一：Claude Code 的 `.claude.md` —— 项目级记忆文件

Claude Code 会在项目根目录自动读取 `.claude.md`，里面的内容会作为**系统级上下文**注入到每次对话中。

**用法**：
```markdown
# 项目规范
- 我们使用 TypeScript，严格模式
- 测试文件放在 __tests__/ 目录，命名 *.test.ts
- 提交信息用中文，格式："feat: xxx" / "fix: xxx"
- 不要生成 console.log，用统一的 logger
```

**效果**：
- 不用每次提醒 "用 TypeScript" 或 "测一下这个"
- Claude 会自动遵循你的风格写代码
- 换会话、换终端，规矩还在

**进阶玩法**：
- 可以放技术栈版本（"Node.js 20+，用原生 fetch 不用 axios"）
- 甚至可以放团队黑话（"DTO 叫 Payload，不要叫 Model"）

---

## 技巧二：Cursor 的 `.cursorrules` —— 更细粒度的控制

Cursor 支持项目根目录的 `.cursorrules` 文件，功能和上面类似，但**可以控制 Composer 和 Tab 补全的行为**。

**示例**：
```markdown
# Cursor 规则
- 优先使用 React hooks，少用 class component
- 样式用 Tailwind，不要写内联 style
- 生成代码时添加 JSDoc 注释
- 不要修改 .env 文件
```

**隐藏功能**：
Cursor 的 `.cursorrules` 还支持**作用域规则**——你可以在不同目录放不同规则：
- `frontend/.cursorrules` → 前端规范
- `backend/.cursorrules` → 后端规范

Agent 进入对应目录时自动切换上下文，**一个仓库多种规矩**。

---

## 一句话总结

> 花 5 分钟写个 `.claude.md` 或 `.cursorrules`，省掉未来 500 次 "请用 TypeScript" 的废话。

项目记忆 = 重复劳动消灭器。今天就把你的项目规范写进去试试？

---

*明日预告：Agent 的 "Undo" 技巧 —— 搞砸了怎么一键回滚？*
