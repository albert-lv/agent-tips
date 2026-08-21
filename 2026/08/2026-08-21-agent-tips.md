# 🤖 Agent 的「分身术」——开多个 Session 并行干活

> **日期：** 2026-08-21  
> **适合：** Claude Code、Cursor、Kimi Code、Windsurf 用户  
> **一句话：** 一个 Agent 干一件事，开三个窗口 = 三倍效率，零上下文污染。

---

## 你是不是也这样？

你跟 Agent 聊了半天，好不容易把它调教懂了业务逻辑，突然冒出个新需求——去查查某个库的最新 API，或者 refactor 另一个模块。

你想着"顺便一起做了"，把新需求丢进去。结果：

- 🤡 Agent 开始混淆两个任务的上下文
- 🤡 之前聊好的业务逻辑被新话题冲散了
- 🤡 最后两个任务都没做好，还得重新来过

**这不是 Agent 的问题，是你把一匹马当三匹用了。**

---

## 💡 技巧：一个 Session = 一个独立任务

Agent 工具的 Session（对话窗口）本质上是一个**独立的上下文空间**。你把所有任务塞进一个窗口，等于让一个人同时写代码、查文档、做设计——不翻车才怪。

### 正确姿势：分身术

| Session | 职责 | 例子 |
|---|---|---|
| **Session A** | 核心开发 | 写业务逻辑、改 bug |
| **Session B** | 调研探索 | 查新库 API、对比方案 |
| **Session C** | 代码审查 | 看 diff、写测试、优化 |

**三个窗口同时开，Agent 互不干扰。**

---

## 🛠 实战示例（以 Claude Code 为例）

### 场景：你要加一个 OAuth2 登录功能

**❌ 错误做法（单 Session 硬扛）：**
```
你：帮我加一个 OAuth2 登录
Agent：好，用哪个库？
你：我先查查... 哦对了，还有这个 bug 先修一下
Agent：（开始混乱）
```

**✅ 正确做法（三开分身）：**

**Terminal 1 - 调研 Session：**
```
你：对比 passport.js、next-auth、auth0 这三个方案，
    我们的场景是 Next.js + Prisma + 自建用户表，
    要支持 GitHub 和 Google 登录。
    给我一份对比表 + 推荐方案 + 原因。
```
→ Agent 去查文档、比优劣，给你一个决策参考。

**Terminal 2 - 开发 Session（保持干净）：**
```
你：根据这份方案（paste 调研结论），
    实现 OAuth2 登录模块，包含：
    1. GitHub 登录路由
    2. 用户创建/关联逻辑
    3. JWT session 管理
    先出 plan，再执行。
```
→ Agent 专注实现，不跑偏。

**Terminal 3 - Review Session（等开发完）：**
```
你：review 这个 PR 的改动（paste diff），
    重点关注安全漏洞、边界情况、测试覆盖。
```
→ 第三方视角审查，更容易发现问题。

---

## 🎯 各工具怎么开分身

| 工具 | 开新 Session 方式 |
|---|---|
| **Claude Code** | 新开一个 Terminal 窗口，重新执行 `claude` |
| **Cursor** | 开新的 Editor Window（File → New Window），每个窗口有独立 Chat |
| **Kimi Code** | 新开 Terminal / 重启 Session，或用 Web 端开多个标签 |
| **Windsurf** | Cascade 目前主要单会话，但可用不同 Workspace 隔离 |

> 💡 **Pro Tip：** 给每个 Terminal/窗口改个标题，比如 `claude-dev`、`claude-research`、`claude-review`，切的时候一目了然。

---

## 🔥 进阶玩法

### 1. 调研 Session → 开发 Session 的知识传递

调研完后，不要让开发 Session 的 Agent 重新查一遍。直接 paste 关键结论：

```
你：背景知识（已调研确认）：
- 选 next-auth，因为它内置 Prisma adapter
- GitHub Provider 配置需要 CLIENT_ID 和 CLIENT_SECRET
- 用户表需要加 emailVerified 字段

基于以上，开始实现。
```

**Agent 直接站在调研成果上开工，省去重复沟通。**

### 2. 开发 Session → Review Session 的 diff 传递

开发完后，把 diff 丢给 Review Session：

```bash
# Terminal 1 里执行
git diff > /tmp/my-feature.diff
# 复制内容，paste 到 Terminal 3
```

Review Agent 从零开始看代码，不受开发过程干扰，更容易抓问题。

### 3. 紧急情况：主 Session 崩了，分身顶上

主 Session 上下文爆炸（Claude Code 的 context limit 告警），不要抢救了——

```
你：summarize our current plan and progress
```

拿着摘要，去新 Session paste，**5 秒满血复活**。

---

## 🧠 为什么这个技巧好用？

| 优势 | 说明 |
|---|---|
| **上下文隔离** | 调研的混乱不影响开发，开发的思路不污染审查 |
| **并行推进** | 调研和开发可以同时进行，不用串行等待 |
| **质量提升** | Review Session 的第三方视角 = 自带"新鲜眼睛" |
| **风险分散** | 一个 Session 崩了，其他 Session 不受影响 |

---

## 📝 今天记住这个

| 要点 | 速查 |
|---|---|
| 一个 Session = 一个任务 | 别把所有事塞一个窗口 |
| 三开：调研 / 开发 / Review | 分工明确，效率翻倍 |
| 跨 Session 传递 = paste 摘要 | 不要让 Agent 重复查 |
| Session 崩了 = 新开 + summarize | 不要抢救，直接重生 |

---

> 💬 **网友锐评：** "以前开一个 Claude 觉得慢，开了三个才发现——不是 Agent 慢，是我让它一次干太多。"
>
> 🤝 **明天见，继续把 Agent 用出花来。**
