# 上下文投喂指南：别让 Agent 把全仓库「吃」一遍

> 2026-07-16 | 每日 Agent Tips

你有没有发现，有时候问 agent 一个简单问题，它吭哧吭哧读了 47 个文件，然后给你的答案还是跑偏的？

不是它不努力，是**你把一桌满汉全席摆它面前，它根本不知道该夹哪道菜。**

今天教你怎么「精准投喂」，让 agent 吃得少、看得准、答得对。

---

## 技巧一：直接点名，别让它自己翻

大部分 agent 工具都有「文件引用」功能，但很多人懒得用，习惯直接打字问。结果就是 agent 为了找答案，把整个仓库翻一遍——既慢又容易带偏。

### Claude Code：直接丢文件路径

```
"看一下 src/auth/jwt.ts 里的 validateToken 函数，
为什么它会在某些 token 上返回 false？
不要改其他文件，先分析这个函数的问题。"
```

Claude Code 会自动读取你提到的文件，并且**不会**擅自去翻别的文件（除非你允许）。

### Cursor：`@` 符号是神器

在 Cursor 的 Chat 里输入 `@`，会出现一个文件选择器：

```
@src/auth/jwt.ts 这里的 validateToken 在哪些情况下会失败？
```

Cursor 会只读你 `@` 的文件，然后精准回答。如果还需要关联文件，再 `@` 第二个：

```
@src/auth/jwt.ts @src/config/auth.ts 这两个文件里的密钥配置是不是对不上？
```

### Kimi Code：一键关联 + 手动引用

Kimi Code 的编辑器支持**自动关联**——你提到一个文件，它会智能推荐可能相关的文件。但更可靠的做法是**手动引用**关键文件，确保上下文不跑题。

---

## 技巧二：告诉它「别碰这些」

有时候问题的答案明明就在 3 个文件里，agent 却跑去读 `node_modules`、`vendor/` 或 `dist/` 里的内容，然后被噪音带偏。

### 在 prompt 里直接画禁区

```
分析以下文件找出问题：
- src/auth/jwt.ts
- src/config/auth.ts
- tests/auth.test.ts

注意：
- 不要读取 node_modules/ 或任何第三方库源码
- 不要修改 .env 或配置文件
- 只分析这 3 个文件，不要扩展到其他模块
```

### 进阶：项目级 `.agentignore`

如果你发现 agent 总是手痒去翻某些目录，可以在项目根目录建一个 `.agentignore`：

```
# Agent 不要读这些
node_modules/
dist/
build/
*.min.js
*.lock
coverage/
```

Claude Code 和 Cursor 都会尊重这个列表（如果它们内置 ignore 逻辑），或者你可以手动在 prompt 里引用它。

---

## 一句话总结

**喂得多不如喂得准。** 下次问 agent 问题之前，先花 10 秒列清楚「看这几个文件，别碰别的」。

它省 token，你省耐心，答案还更靠谱。双赢。

---
*来源：Claude Code 上下文管理、Cursor @ 引用、Kimi Code 文件关联*
*生成时间：2026-07-16 08:42*
