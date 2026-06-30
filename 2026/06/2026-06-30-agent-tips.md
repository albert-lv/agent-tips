# 🎯 每日 Agent 技巧 · 2026-06-30

> 致：向上 & 小惠 —— 今天聊一个「给 AI 定家规」的骚操作

---

## 技巧一：`.cursorrules` —— Cursor 的「入职手册」

你有没有遇到过这种情况：每次开新对话，Cursor 都要问你一遍「用啥测试框架」「代码风格是 tab 还是 space」「TypeScript 严格模式开不开」……

就像每次点个外卖都要重新填地址，很烦。

### 解法：在项目根目录建一个 `.cursorrules` 文件

Cursor 每次启动 Agent/Composer 时，会自动读取这个文件作为系统提示。你可以把项目的所有「家规」写进去，Agent 每次都会先读一遍再动手。

### 实战模板

```markdown
# 项目规范

## 技术栈
- 前端：React 18 + TypeScript（严格模式）
- 样式：Tailwind CSS，不用 inline style
- 状态管理：Zustand，不用 Redux
- 测试：Vitest + React Testing Library

## 代码风格
- 使用 2 空格缩进
- 函数优先用箭头函数
- 异步逻辑统一用 async/await，不用 .then()
- 错误处理必须写，不能裸奔 try/catch

## 文件组织
- 组件放在 src/components/
- 工具函数放在 src/utils/
- 类型定义放在 src/types/
- 新增文件要同步更新 index.ts 导出

## 重要约束
- 修改代码前先看现有测试，确保不破坏
- 不要修改不属于本次任务的文件
- 提交前跑一遍 pnpm test
```

### 为什么这招好用？

**Before**：你说了 10 遍「用 async/await」，第 11 次对话它还是给你写 `.then()`

**After**：`.cursorrules` 里写一次，Agent 每次都会自动遵守

💡 **Pro tip**：`.cursorrules` 支持 Markdown，可以分层写。项目通用的放根目录，某个模块的特殊规则可以放在子目录（比如 `go/` 下放 Go 专用规则），Cursor 会逐级合并。

---

## 技巧二：`CLAUDE.md` —— Claude Code 的「项目记忆体」

Claude Code 没有 `.cursorrules`，但它会读取项目根目录的 `CLAUDE.md` 文件（或者 `.claude/CLAUDE.md`）。

和 `.cursorrules` 不同的是，`CLAUDE.md` 更偏向**项目背景知识**，不只是代码规范。

### 实战模板

```markdown
# 项目介绍

这是一个 Agent Arena 项目，提供 RLHF 训练的基础设施层。
核心概念：RolloutProvider、DataProto、RewardModel。

## 架构速览
- go/ —— Go 服务端，负责 Arena 调度
- python/ —— Python 客户端和 veRL 集成
- proto/ —— gRPC 协议定义

## 常见踩坑
- 修改 proto 后必须重新生成代码（make gen）
- arena-sdk 和 arena-server 版本必须一致
- 本地测试用 mock_llm.py，别真调大模型

## 开发流程
1. 改代码前先跑 make test
2. 新增功能要补测试
3. PR 前跑 make lint

## 关键人物
- Jarvis 负责后端和 SDK
- Moss 负责前端和文档
```

### 高级玩法：动态更新

Claude Code 有个隐藏技巧：你可以在对话里说「把这条规则加到 CLAUDE.md 里」，它会自动更新文件。相当于**让 Agent 自己给自己写规矩**。

比如：

> 「以后修改 go/ 目录下的文件时，记得同时检查 go.sum 有没有变。把这个写进 CLAUDE.md。」

下次它就会记住。

---

## 对比速览

| 功能 | Cursor (`.cursorrules`) | Claude Code (`CLAUDE.md`) |
|------|------------------------|---------------------------|
| 自动读取 | ✅ 每次对话都读 | ✅ 每次对话都读 |
| 支持子目录规则 | ✅ 可以 | ❌ 只读根目录 |
| 适合写什么 | 代码规范、技术栈 | 项目背景、架构、踩坑 |
| 格式 | Markdown | Markdown |
| 让 AI 自己更新 | ❌ 手动编辑 | ✅ 可以让 Claude 写 |

---

## 一句话总结

> **`.cursorrules` 让 Agent 写代码时有章可循，`CLAUDE.md` 让 Agent 理解项目时不瞎猜。两个一起用，Agent 从「每次重新认识的陌生人」变成「熟悉你家规矩的老员工」。**

*明天见 👋*
