# 10-23 — 缓存保鲜 + 多Agent爆炸周：Claude Code 给长会话「自动续杯」，Agent 成群结队要靠「保险」

## 🤔 本周的 Agent 圈：Agent 在繁殖，边界在被测

先把这一周的新鲜事摆上（全在 72 小时内）：

- **10/15~16**：多 Agent 工具**前后脚爆炸**——BAND for Developers 桌面端发布（一人指挥一群跨工具 Agent 的调度台）、腾讯混元开源 **Hy Memory 2.0**（给 Agent 装「跨会话长期记忆」，把试过/改过/定过的事记住）；
- **10/20**：Claude Code 连发两版（v2.1.304 / v2.1.307）——权限审批再加紧、新增 `claude_code.safety_event` 安全事件遥测；
- **10/21**：Claude Code API 版本推进到 **2026-10-21**；
- 背景音：DeepSeek Harness 被曝**沙箱逃逸漏洞（CVSS 9.4）**，普通引号就能绕过 10/11 主流 Agent 的命令过滤；GitHub Copilot 10/2 起正式弃用旧模型，其中 **Kimi K2.7 Code → K3**、Claude Opus 4.7 → Opus 5.5。

串起来就一句话：**Agent 越来越多、越跑越自主，于是「上下文保鲜」和「可审计的安全护栏」成了本周的主角**。今天两个技巧，一个保命（钱包），一个保命（字面意思）。💸

## 🛠️ 技巧一：Idle Compaction —— 长会话的「自动续杯」

如果你有过这种体验：一个跑了大半天、上下文 200K+ 的重磅会话，中途去开了个会 / 吃了个饭，回来时 prompt cache 已经过期——接下来每一轮都要**原价重读整个对话**，账单当场起飞。这是长会话最隐蔽的流血点：上下文既会「撑爆」，也会「放凉」。

Claude Code v2.1.299（10/14 上线，v2.1.302 补齐开关）给的解法是 **Idle Compaction（闲置自动压缩）**：

> 当一个 200K+ token 的会话**闲置约 54 分钟**（赶在 1 小时 prompt cache 到期之前），自动对它做一次压缩——用一小撮 token 保住会话骨架，让 cache 续命。

设计得很鸡贼：压缩本身花 token，但**远比 cache 过期后全量重读便宜**。人不在的时候它自己把家务做了，你回来接着聊，上下文没爆、cache 没凉、钱包没哭。

### 使用须知（都在细节里）

- **只触发 200K+ 的会话**：`CLAUDE_CODE_IDLE_COMPACT_MIN_TOKENS` 可以调这个门槛；
- 想关：`/config` 里的 `Idle compaction` 一行开关（v2.1.302 加入），或 settings.json 里 `"idleCompaction": false`；
- 🚨 **压缩前主动留一句**：`/compact 保留 xxx 结论`——闲置触发的是自动压缩，你没机会现场指挥，重要决策趁早让它落盘（配合 10-16 那篇「出师表」把家规写进 CLAUDE.md 更稳）；
- **配套修复值得点赞**：以前压缩后偶尔会「失忆」不知道自己会哪些技能，现在**压缩完成后技能列表会重新发送**（v2.1.304），`/reload-skills` 也只发增量——不会出现「压缩完变回出厂设置」的灵异事件；
- 彩蛋：`CLAUDE_CODE_AUTO_MODE_MODEL`（v2.1.302）可以自选 auto mode 分类器用哪个模型，企业 gateway 用户也能精细控制成本。

一句话：**长会话别硬扛到 cache 过期，让它在 54 分钟处自己续杯。**

## 🛠️ 技巧二：多 Agent 时代，先给自己的保险柜上锁

本周最大信号不是某个模型，而是**「一个人管一群 Agent」正式成为产品品类**：BAND（调度台思路）、Hy Memory 2.0（共享记忆思路）同周发布。但 Agent 越多，攻击面越大——沙箱逃逸、命令注入、infostealer 已经开始偷 **Agent 的 API key**（Anthropic 本月威胁报告原话）。在你决定上多 Agent 调度台之前，先把现有工具的「保险」用明白：

### ① Kimi Code 的危险命令护栏（免费的内置保险）

权限模式更名后（`/ask-when-needed` 必要时询问 / `/never-ask` 完全自动），Kimi Code 加了一个**内置危险命令护栏**：`shutdown`、`reboot`、`rm -rf` 这类命令在「完全自动」模式下会被**直接拒绝**，其余模式执行前必须经你确认。

```toml
# 真想关（不推荐）：
[permission]
dangerous_command_guard = false
```

```text
# Claude Code 侧的对应物（10/20 v2.1.307）：
# 每个被权限规则 / auto mode / 安全钩子拦下、放行、打回的
# 工具调用，都会记录 claude_code.safety_event 遥测事件
# ——接了 OTel 的团队，Agent「被拦了什么」从此可审计、可报警。
```

### ② 三句保命口诀（比任何护栏都重要）

1. **Agent 拿 shell = 拿到你的账号权限**：研究已证明普通 shell 引号能绕过 11 个主流 Agent 里 10 个的命令过滤——护栏是兜底，不是免死金牌；
2. **API key 是新的 infostealer 目标**：钥匙别躺在 `.env` 里裸奔，Agent 配置目录的权限收紧一点；
3. **未提交的改动永远不交给 Agent 批量跑**（worktree 隔离那篇的铁律依然有效）：出事时 `git reset` 是你最后的后悔药。

> 顺便：Copilot 用户注意 10/2 模型换血——被弃用的 **Kimi K2.7 Code 官方替代是 K3**（1M 上下文），Claude Opus 4.7 → Opus 5.5。如果工作流里还挂着旧别名，顺手切一下。

## 🧠 本周风向小结

十月下旬的 Agent 工具圈，重点已经彻底从「模型更聪明」挪开：**省 context（idle compaction）、可审计（safety_event）、有记忆（Hy Memory 2.0）、能调度（BAND）**。说白了就一句——Agent 正在从「一个聪明的助手」变成「一个需要管理的员工」。而管理的第一步，是先知道它在干什么。🔥

## 📋 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| 200K+ 长会话怕 cache 过期烧钱 | 放着别管，闲置约 54 分钟自动 idle compaction 续杯 |
| 调整自动压缩门槛 | `CLAUDE_CODE_IDLE_COMPACT_MIN_TOKENS`；`/config` 关 `Idle compaction` |
| 压缩后 Agent「忘了自己会什么」 | v2.1.304 起技能列表自动重发，记得 `claude update` |
| `rm -rf` 在多 Agent 并行时乱跑 | Kimi Code 危险命令护栏默认拦截；Claude Code 侧开 `safety_event` 遥测 |
| Copilot 里 Kimi K2.7 Code 报 deprecated | 换 **K3**（1M 上下文），Opus 4.7 → Opus 5.5 |
| 想上多 Agent 调度台 | 先上锁：key 收好、改动先 commit、出事能 `git reset` |
