# 10-22 — 省钱术 + 隔离单间：Claude Code 教 Agent「省着花」，Codex 给每人发一间「单人牢房」

## 🤔 这两周的 Agent 圈，风向变了

先把时间线摆上：

- **10/08**：OpenAI 发布 **Codex CLI 0.162.0**——Agent 可以自己创建、管理 Git worktree，多开终于不用人肉搬目录；
- **10/09**：Anthropic 发布 **Claude Code v2.1.296**——没有新模型，全是「上下文经济学」：大文件一次读完、子代理自己定压缩线；
- **10/12**：哈佛一份覆盖 **700+ 家公司**的研究刷屏——用了 AI Agent 的团队**代码量多写了约 30%，但软件交付量没显著变化**。

三件事串起来就一句话：**头部工具已经不再卷「更聪明」，开始卷「更省、更隔离、更好 review」**。上下文就是钱，隔离就是命，diff 小才是正义。今天两个技巧，正好一边一个。💸

## 🛠️ 技巧一：Claude Code v2.1.296 —— 上下文的「省钱两件套」

这个版本没有任何花活，但两个新功能都直击钱包：

### ① `allow_large`：大文件别再分页啃了

以前 Agent 读超大日志或生成文件，只能一段一段翻页，既烧 token 又慢。现在 Read 工具多了 `allow_large` 选项——当确实需要整个文件、且上下文装得下时，**一次调用读全文**。

实测效果：一个 3000 行的文件，不加选项读一半就截断；告诉它「整个文件都要看」，它自动带 `allow_large: true` 一把梭，一次到位。Opus 5.5 和 Haiku 5.5 均验证通过。

**什么时候用**：审查大型生成文件、完整读 CI 日志、扫超长配置文件。日常小文件不用管，Agent 自己会判断。

### ② `autoCompactWindow`：给每个子代理发自己的「压缩闹钟」

这是本次更新真正的隐藏大招。以前子代理的上下文压缩点和主会话绑死——调查型子代理翻了几百个文件，常常把宝贵的早期结论压没了。现在可以在子代理 frontmatter 里**单独设压缩线**：

```markdown
---
name: log-reader
description: 读取超长日志，归纳去重后的错误列表
tools: Read, Grep, Glob, Bash
model: sonnet
autoCompactWindow: 200000
---
```

子代理到 20 万 token 就自己压缩，主会话该多大还多大，互不打扰。

⚠️ **三个大坑，全是无声失败**：

1. **必须写纯整数**！`autoCompactWindow: 200k` 会被解析成 `200`，低于下限 10 万，**静默忽略**——子代理照常继承主会话窗口，什么都不报。想验证就 `claude --debug` 后搜 `invalid autoCompactWindow`；
2. 环境变量 `CLAUDE_CODE_AUTO_COMPACT_WINDOW` 一旦设置，frontmatter 直接失效（优先级被覆盖）；
3. `--agent` 会话模式下这个字段不生效。

**彩蛋两连**：`CLAUDE_CODE_WORKFLOW_SUBAGENT_MODEL` 环境变量可以把所有 workflow agent 钉死在一个模型上（普通子代理不受影响）；Sonnet 5.5 的缓存读入价从 $0.20 降到 **$0.10/M tok**，`/cost` 显示已经同步——上个月的大户这个月账单自动瘦了。

## 🛠️ 技巧二：Codex 0.162 —— Agent 多开的「隔离单间」

多 Agent 并行最大的翻车现场：两个 Agent 在同一个目录改同一份代码，互相覆盖、互相 `git checkout` 掀桌子。解法是 git worktree（一任务一分支一目录），但以前得你自己手工搬。0.162 把这个能力**内置成了工具**，三个入口：

```text
① 命令行：codex --worktree          # 新会话直接开在新建的 worktree 里
② TUI 里：/worktree 选择器          # 会话中途开、换、看
③ 对话中：让 Agent 自己调 create_worktree / list_worktrees
```

worktree 统一放在 `~/.codex/worktrees`，每个目录里写着一个 `codex-thread.json`——**把目录和会话绑在一起**。`/worktree` 浏览器按会话标题列出所有检出，点一下就能 resume 对应会话。 disposable 的检出用完即弃，想找回「上周那个重构开在哪儿了」，一眼定位。

**实战规则（血泪版）**：

- **一任务一 worktree 一分支**，你在主目录当 reviewer，Agent 永不共享文件夹；
- 开新 worktree 前**先 commit 或 stash**——新检出从某个 commit 开始，你工作区里没提交的东西它看不见；
- 🚨 **绝！对！不！要！用 stash**！`refs/stash` 在所有 worktree 间共享——一个 Agent stash 了，另一个 Agent 可能把它 apply 到错误的地方。只用自己分支上的 commit；
- 给 worktree 的产物**起名分支**再谈合并，别留着 detached HEAD 裸奔；
- 两个冷知识：Agent 自己 `create_worktree` 出来的目录**不会**把会话搬过去（cwd 原地不动，这是故意的）；sandbox 模式下 `.git` 是只读的，Agent 能改文件但 commit 会锁死——想让 Agent 自己提交就调审批策略。

## 🧠 哈佛研究那 30% 说明什么

多写 30% 代码、交付没变——翻译过来就是：**Agent 的产出膨胀了，但人类的 review 带宽没有**。所以十月这波更新的方向完全正确：上下文省钱让 diff 更聚焦，worktree 隔离让 diff 可归因，小块才好审。

你的角色也在变：从「写代码的人」变成「给 Agent 划格子、审格子的人」。格子划得好，五个 Agent 是五倍产能；格子划得烂，五个 Agent 是五倍 review 地狱。🔥

## 📋 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| 让 Agent 一次读完超大文件 | Claude Code v2.1.296+ 自动可用，只需说「整个文件都要看」 |
| 调查型子代理总被压掉结论 | frontmatter 加 `autoCompactWindow: 200000`（**纯整数，别写 200k**） |
| 验证设置生效没 | `claude --debug`，搜 `invalid autoCompactWindow` |
| Codex 多开改同一仓库 | `codex --worktree`，一任务一目录一分支 |
| 并行 Agent 的铁律 | 开干前 commit；**永远不用 stash**（refs/stash 全局共享） |
| 找回「那个 Agent 的代码开在哪」 | Codex `/worktree` 浏览器，按会话标题列出全部检出 |
