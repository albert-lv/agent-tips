# 🏢 Agent 有了自己的工位：Cursor Agent SDK 正式上岗，Claude Code 分身出厂自带

> 2026-10-30 · 每天一个 Agent 小技巧

## 📰 今日热点背景

十月最后一周，Agent 圈的主题是「从玩具到工件」：

**① GPT-6 本月全面开放**，ChatGPT 正式上线 Intelligent UI，各家 agent 工具连夜适配新模型；**Meta 在 10/16 放出 Muse Spark Open**——816B 参数开放权重，明确冲着 coding agent 场景来，巨头的火药味已经漫到开源侧。

**② Cursor Agent SDK 正式 GA（10/20）**。这不是又一个聊天框，而是一套「把 agent 当软件工程来管」的脚手架：agent 是一个代码项目，能进 git、能跑测试、能一键部署。Cursor 官方博客原话是 "define an agent as a folder of code, test it with evals, and deploy it"。

**③ Claude Code 2.1.30x 系列密集更新**：fork 子代理（fork subagent）在终端之外的 `claude -p`、Agent SDK、桌面端、VS Code 里**全部改为默认开启**；桌面端和 VS Code 的主会话子代理也统一改为后台运行——和终端行为对齐了。

一个是「agent 工程化上架」，一个是「分身系统统一出厂设置」。今天各讲一个能立刻用的。

## 🛠️ 技巧一：Cursor Agent SDK —— 三行命令，把一个 Agent 从聊天框变成代码项目

以前写一个「PR 来了自动审查」的 bot，你要自己搭 webhook、管 prompt、写胶水代码。现在 Cursor 把这条路铺平了：

```bash
npx @cursor/agent-sdk init ./my-bot   # 脚手架
cd my-bot && agent-sdk dev            # 本地起 playground 调试
agent-sdk deploy                      # 构建 + 给 URL + 上云
```

**agent 就是一个文件夹**，每个部分都有明确职责：

| 文件/目录 | 作用 |
|---|---|
| `instructions.md` | 就是 prompt |
| `tools/` | 每个文件 = 一个强类型动作，模型可调用 |
| `skills/` | 按需加载的流程手册（跟 SKILL.md 一个思路） |
| `mcp-connections/` | 从 MCP server 挂工具 |
| `channels/` | 定义 agent 在哪「上班」 |

**Channels 是灵魂**：GitHub PR / 评论 / CI 事件、Slack @ 和私信、任意 webhook、cron 定时，都能唤醒它。GitHub 事件走你的 Cursor 账号中转，**不需要公网 webhook URL**。而且每个会话（比如某个 PR 的线程）保持独立 session，跟进回复自动续上，不会失忆。

**Evals 是工程化的关键**：`agent-sdk eval` 发固定消息并断言「该调哪些工具、参数对不对、回复长什么样」，本地和 CI 都能跑。更香的是：

```bash
agent-sdk github replay <pr-url>   # 把真实 PR 转成签名 webhook 回放测试
```

不用等下次 push 就能验证 GitHub bot 的行为。

> 🚀 上手捷径：`init` 时加 `--template`，官方自带 security reviewer（安全审查员）、PR autofixer、Linear / Jira triager（工单分拣员）等模板——挑一个最接近你需求的改，比从空白写快一个量级。

## 🛠️ 技巧二：Claude Code 的「分身」现在出厂自带——脚本里 spawn 子代理不用再手动开 fork

如果你用 `claude -p`、Agent SDK 或者在 VS Code / 桌面端里编程式地 spawn 子代理，以前有个细节坑：**终端里默认是 fork 子代理（在分叉点继承上下文、独立运行），但其他环境默认不是**，行为不一致，脚本换个环境就跑出不同结果。

**2.1.302 起统一了**：fork subagent 在终端、`claude -p`、Agent SDK、桌面端、VS Code **全部默认开启**。想关掉才需要显式设：

```bash
export CLAUDE_CODE_FORK_SUBAGENT=0
```

配套还有两个行为对齐：

- **桌面端 + VS Code 的主会话子代理，现在永远在后台运行**（之前是 Claude 自己决定）。你 ← 键把会话丢到后台时，子代理照常干活，不会跟着睡。
- 子代理自己再调 Agent 工具时，在 `claude -p` / SDK / 桌面 / VS Code 里默认转前台——嵌套层级的行为终于可预期了。

> 💡 对你正在搞的 AgentLoop / nano-symphony 这类多 agent 编排项目，这意味着：**同一套 spawn 逻辑，本地调试和 CI 里跑，行为一致了**。不用在每个入口写环境判断，少一整类「在我机器上是好的」bug。

## 🍰 彩蛋

Cursor 同周的另一个更新容易被忽略：**Cloud Agent Environments 现在支持最多 300 个仓库**，大环境的加载速度快了 5 倍，Environments API 进入 beta——可以用代码批量创建和管理 agent 跑的云环境了。如果你之前因为「环境塞不下整个 monorepo」而放弃过云 agent，现在值得回头再看一眼。

## 🎯 今天记住这个

| 场景 | 做法 |
|---|---|
| 想要「PR 自动审查 / Slack 工单分拣」这类常驻 bot | `npx @cursor/agent-sdk init --template security-reviewer`，channels 选 GitHub/Slack |
| 测试 GitHub bot 不想等真实事件 | `agent-sdk github replay <pr-url>` 回放真实 PR |
| 脚本里 spawn 子代理行为不一致 | 2.1.302 起全环境默认 fork，不用手动开 |
| 想恢复旧的子代理行为 | `CLAUDE_CODE_FORK_SUBAGENT=0` |
| monorepo 塞不进云 agent 环境 | 现在上限 300 仓库 + Environments API（beta） |

---

*📮 明天 08:42 见。有想看的工具/场景，留言给向上或小惠，安排上。*
