# 📋 给 Agent 立规矩：用「军规文件」让 AI 记住你的偏好

> **向上 & 小惠的每日 Agent 小技巧** | 2026-06-17

---

有没有重复说过一百遍的话？

> "用 TypeScript 不是 JS"  
> "别用 any"  
> "测试用 vitest 不是 jest"  
> "函数不要超过 50 行"

每次新开对话都要重新交代，像班主任每天早读重复纪律——累不累啊？

今天教你们一招：**让 Agent 自己背规矩，不用你每次都张嘴。**

---

## 💡 技巧：项目根目录放一本「军规文件」

不同工具认不同文件名，但逻辑一样：

| 工具 | 军规文件名 | 生效范围 |
|------|-----------|---------|
| **Cursor** | `.cursorrules` | 整个项目 |
| **Claude Code** | `CLAUDE.md` | 整个项目 |
| **Windsurf** | `.windsurfrules` | 整个项目 |
| **GitHub Copilot** | `.github/copilot-instructions.md` | 整个项目 |
| **Kimi Code** | 暂不支持文件级配置，但 system prompt 同理 | — |

**军规文件里写啥？** 随便，就是你的碎碎念集合：

```markdown
# 项目军规 — 谁违反谁重构

## 代码风格
- 所有新代码用 TypeScript，strict mode 开满
- 禁止 any，unknown 也要尽快 narrow
- 函数超过 50 行必须拆分，命名要能当句子读

## 测试
- 测试用 vitest，不用 jest
- 每个 public 函数必须有单元测试
- 覆盖率低于 80% 的 PR 直接打回

## 框架偏好
- 前端用 React + Tailwind，不用 styled-components
- 状态管理用 Zustand，Redux 仅限 legacy 模块
- 新 API 用 tRPC，REST 逐步迁移

## 提交规范
- commit message 用英文，格式：type(scope): subject
- 禁止 push -f 到 main
```

---

## 🎯 实际效果：从「复读机」到「默契搭档」

**以前你问：**
> "帮我写个表单组件"

Agent 啪给你来个 JS + styled-components + PropTypes，梦回 2018。

**现在有了军规文件，你问同样的：**
> "帮我写个表单组件"

Agent 默默输出 TypeScript + Tailwind + React Hook Form，还有 vitest 测试文件附赠。

**你一句话没说，但它已经知道你要什么了。** 这就是默契。

---

## 🎮 进阶玩法：分层军规

项目大了，规矩可以分层：

**根目录 `.cursorrules`** —— 全项目通用（语言、框架、测试）

**子目录 `CLAUDE.md`** —— 模块特供（比如 `go/` 目录里写"用标准库 context 做超时，不用第三方"）

Agent 会自动**就近合并**规则，根目录的 + 当前目录的，优先级就近。

---

## ⚡ 快速复制模板

`.cursorrules` 通用版：

```markdown
# 项目规范
- 语言：TypeScript（strict），不用 JavaScript
- 风格：函数优先，命名动词开头（getUserById, validateForm）
- 限制：单行不超 120 字符，函数不超 50 行
- 测试：vitest，覆盖 public API
- 依赖：优先标准库，新功能先讨论再引入第三方
- 提交：英文 commit，type(scope): subject 格式

# 沟通风格
- 解释"为什么"，不只是"做什么"
- 给出 2 个选项时说明 trade-off
- 遇到不确定的，先问而不是猜
```

---

## 🧠 为什么这招神了？

LLM 的上下文窗口越来越大，但它**不会自动知道你的偏好**。军规文件就是给 Agent 一本「员工手册」，让它每次进项目先翻一遍，自然按你的习惯来。

最爽的是：**规矩跟着代码走**。换台电脑、换个同事、过三个月再回来，军规文件还在，Agent 还是原来那个味。

---

**今日作业**：在你最活跃的项目根目录丢一个 `.cursorrules` 或 `CLAUDE.md`，写三条你最常说的话，看看下次开新对话 Agent 是不是变乖了 😏

*明天见 👋*
