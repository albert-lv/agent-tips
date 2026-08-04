# 🏄 Agent Tips 08/04 — Windsurf Cascade：Cursor 学不会的「双模式」绝活

> 向上、小惠早～今天兑现昨天预告，聊聊 Windsurf 的 Cascade 到底有什么是 Cursor 复制不了的 👇

---

## 1️⃣ Write 模式 vs Chat 模式：两个面板，两种脑回路

Windsurf 最大的设计差异——它把 AI 交互拆成了 **两个独立面板**，不是像 Cursor 那样挤在一个对话框里：

| 模式 | 快捷键 | 适合场景 | 行为风格 |
|------|--------|---------|---------|
| **Chat** | `Cmd+L` | 问问题、讨论方案、查文档 | 话多，解释详细，像技术顾问 💬 |
| **Write** | `Cmd+I` | 直接改代码、生成文件、重构 | 话少，直接动手，像资深工程师 🛠️ |

**为什么这个设计很妙：**

Cursor 的 Composer 虽然也有 Normal/Agent 切换，但本质上还是"同一个对话框的不同态度"。Windsurf 则是**物理上分开两个入口**——你的肌肉记忆会自然帮你选对模式：

- 想讨论 "这个架构设计有没有问题" → Chat
- 想让它 "帮我把这个组件拆成三个文件" → Write

**经典翻车对比：**

> 你对 Cursor 说 "分析一下这段代码的性能瓶颈"——它可能顺手就把代码改了 😰
> 
> 你对 Windsurf Chat 说同样的话——它只会分析，不动手，除非你切到 Write 模式 🎯

**省下来的精神内耗：** 不用每次先加 "请不要修改代码，只分析" 这种免责声明。

---

## 2️⃣ Cascade Agent 的「Auto-Run」——比 Yolo 更聪明

Windsurf 的 Agent 模式也有自动执行能力（设置里开 **"Auto-run accepted commands"**），但它和 Cursor Yolo Mode 有个关键区别：

**它会先给你看一个「执行计划」，你可以逐条勾选** ✅

比如你说：

```
帮我把这个 React 项目迁移到 Next.js App Router，
先列出你要改哪些文件、删哪些文件、装哪些依赖
```

Cascade Agent 会生成一个类似这样的清单：

```
□ 安装 next@latest react@latest react-dom@latest
□ 创建 app/layout.tsx
□ 创建 app/page.tsx  
□ 删除 pages/index.tsx
□ 更新 tsconfig.json 的 paths
□ 跑 npm run build 验证
```

你勾几个，它执行几个。比 "全自动驾驶" 安全，比 "每一步都问" 高效。

**实战建议：** 大额重构任务用这种模式，小修小补才开全自动。

---

## 3️⃣ 隐藏的 Web 搜索能力（不用自己贴文档）

Cascade Chat 里可以直接让它查最新文档：

> "查一下 Next.js 15 的 async request API 怎么用，给我个例子"

它会自己打开浏览器搜官方文档、读内容、给你总结。比你自己去翻 docs 再贴回来快得多。

⚠️ **但注意：** 这个搜索能力目前只有 Chat 模式有，Write 模式专注改代码，不联网查东西。所以"查资料+改代码"的工作流通常是：先在 Chat 问清楚，再切 Write 执行。

---

## 🧠 今日一句话

> "Cursor 是一个对话框换语气，Windsurf 是两个对话框换脑子——Chat 负责想，Write 负责干。"

明天见 👋

---

*明日预告：Kimi Code 的长上下文到底能多离谱？一个代码库全扔进去会怎样？*
