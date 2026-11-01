# 🎙️ agent 干到一半可以「插嘴」了：十月发布洪峰里漏掉的两个硬货

> 2026-11-01 · 每天一个 Agent 小技巧

## 📰 今日热点背景

十一月第一天，先把十月那场「发布洪峰」的漏网之鱼捞干净——**10/12~10/20 短短九天，OpenCode 2.0、Codex CLI 0.163.0、Claude Code 2.1.302、Xojo Agentic Mode、VS Code 1.141 Agents 窗口五连发**（外加 10/26 GPT-6.1 Terra 进 Codex、10/27 Antigravity v2.28 内置浏览器），主题出奇一致：**让 agent 跑得更久、更自主，人少插手**。

前几天的 tips 已经讲了 Claude Code 的 fork 子代理（10/30）和 `/review` GA（10/31），今天补两个没被充分讨论的：一个是 **Codex 的「实时纠偏」**——不用再等它跑完或整段取消；另一个是 **Haiku 5.5 的正式岗位说明书**——便宜打工人上岗。

## 🛠️ 技巧一：Codex CLI 0.163.0——「驾校教练」模式：agent 开到一半，直接打方向

以前用 CLI agent 最憋屈的场景：眼看它往沟里开，你只有两个选择——**干等它跑完再返工**，或者**取消整段重来**。0.163.0（10/15）把这条路修通了：

**1. Live steering（实时纠偏）**：agent 还在生成回复的途中，你就可以插入新指令——「别改那个文件」「换个思路，用 B 方案」。它当场换挡，不重开 session、不烧掉已经跑完的部分。类比驾校教练：不用等学员开回起点，副驾上直接拽一把方向。

**2. `/agents` 指挥中心**：新加的 agent command center（`codex agents` 或会话里 `/agents`），把所有活跃 agent、子代理放一个面板里看。配合 managed Git worktree 默认开启——每个 agent 自动开独立 worktree，并行干活互不踩工作区，人只负责在面板上巡视。

**3. 并行只读工具**：检索文件、读日志这类只读操作不再排队，一次全发出去。对「先翻遍仓库再动手」的探索型任务，省的就是硬等待。

> 💡 实操建议：长任务别再用「启动→走开→祈祷」模式了。开 `/agents` 面板，前 30 秒盯着它的第一批动作，发现路线不对立刻 steering——**早期纠偏成本最低**，等它改了五个文件再回头，那才是真·返工。

## 🛠️ 技巧二：Haiku 5.5——「便宜扇出、昂贵判断」终于有了官方廉价打手

10/7 发布的 Claude Haiku 5.5 容易被「最小杯更新了」这种标题盖过去，但它的官方定位很精确：**当 Opus 5.5 / Sonnet 5.5 的 subagent**。三个硬指标：

- **成本降约 75%**，1M 上下文（从 200K 扩了 5 倍）——塞得下一个中型仓库的检索量；
- **第一个可调 effort 的 Haiku**（`low`~`high`，默认 medium）——重复性杂活用 low 档，又快又省；
- 官方点名的活：summary、compaction、数据库查询、分类、**给大模型当编码子代理打杂**。

**正确打开方式**：主 agent 留着 Opus 5.5（10/23 的 Coding Agent Index v2.0 上，Claude Code + Opus 5.5 仍以 61.7 分居榜首），把「翻日志找报错」「全仓搜某个模式的用法」「把这段代码 summarize 成交接文档」这类**探索烧 token 但判断要求低**的活，显式派给 Haiku 5.5 子代理。子代理在自己隔离的 context 里烧 50k token 探索，只带 1~2k 的结论回主会话——主 agent 的推理余量一点没被污染。

一句话：**贵的做判断，便宜的做跑腿**，这分工以前要自己在 prompt 里硬编，现在 Anthropic 官方把工位都画好了。

## 🥚 彩蛋：两条一句话速报

- **GPT-6.1 Terra 进 Codex（10/26）**：API $1/$5 每百万 token，正好是 Sol 的一半。明确、可重复的中等任务可以主动降档，账单直接腰斩——OpenAI 这是把「按任务选档位」的刀递到你手上了。
- **AGENTS.md 成跨工具通用家规**：Claude Code 现在项目里没有 `CLAUDE.md` 时会自动读 `AGENTS.md`（v2.1.305+），而 Codex / Copilot / Cursor / Antigravity 本来就认这个文件。**只维护一份 `AGENTS.md`，所有 agent 自动遵守**——比每个工具各写一份省太多，项目级家规的「最大公约数」时代到了。

## 🎯 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| Codex agent 跑偏了 | 不用取消，生成途中直接插话纠偏（live steering） |
| 同时盯多个 Codex agent | `/agents` 开指挥中心 + 默认 managed worktree 隔离 |
| 长任务防返工 | 前 30 秒盯首批动作，早期 steering 成本最低 |
| 探索/检索类杂活 | 派 Haiku 5.5 子代理（低 effort），主 agent 留 Opus |
| 项目家规多工具复用 | 只写一份 `AGENTS.md`，Claude Code / Codex / Copilot 通吃 |
