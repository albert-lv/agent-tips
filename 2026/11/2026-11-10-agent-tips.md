# 2026-11-10 — 给 Agent 装上眼睛：Kimi 20 小时连跑背后的「视觉回环」+ Claude Code 新默认模型 Fable 5.2

> 这周 AI coding 圈最大的瓜：Kimi 把 Agent 开进了游戏引擎——没人干预连跑 20 小时，交出一个能继续开发的 Unity 原生工程。另一边，Claude Code 11/9 更新日志悄悄把默认 Fable 模型换成了新的 Fable 5.2。今天一篇讲透前者最值得抄的作业，一篇过完后者最实用的两个更新。

## 📰 今日热点背景

- **Kimi「游戏开发与创意工作解决方案」（11/2 发布，本周持续刷屏）**：K3.1 Pro + Kimi Code + Kimi Work，接上 Unity / Unreal Engine / Blender / Godot / Maya 等插件。招牌演示：与 Unity 中国团结引擎 + Tuanjie Codely Agent 协同，**无人工干预连续工作 20 小时**，把一个网页 Demo 复刻成 Unity 原生工程。网易游戏、三七互娱、凉屋游戏（CEO 说「已是日常开发第一选择」）都在测试。
- **配套的两个重磅动作**：K3.1 Pro 完整权重 11/4 起开放；**K2 系列 10/31 起停止新注册，12 月底全线下线**（上下文也从 256K 升到 1M）。定价：输出 ¥68/MTok，缓存命中 ¥1.30——差 50 多倍；批量推理六折。
- **Claude Code 11/9 changelog**：新增 **Claude Fable 5.2**（`claude-fable-5-2`），成为默认 Fable 模型——1M context，$8/$40 每 Mtok，缓存读 $0.20/Mtok。同时 `claude remote-control` 支持 `--settings <file>`；Opus 4.8 的 fast mode 11/30 正式移除（10/30 已弃用），要用提速就迁 Opus 5.5。

## 技巧一：抄 Kimi 20 小时演示的作业 —— 给 Agent 装上「眼睛」（视觉回环）

这个演示里真正值得每个 agent 用户抄的，不是 20 小时这个数字，而是它的**交付前自检回路**：

```
跑起来 → 录屏/截图 → 模型自己看画面找问题 → 改 → 再跑
```

Kimi 在 Godot 里做 2.5D 动作 Demo，会自己把游戏跑起来，对照渲染画面和试玩录像自查，改完再跑，多轮迭代后才交付。Blender 里看多视角预览，UE 里看多角度截图。**Agent 能接手的工作，靠它自己的视觉闭环掉了「差不多能跑」这个坑。**

这个模式不挑工具，今天就能用在你自己的 coding agent 上：

- **凡是「exit code 说不了真话」的任务都适用**：前端页面、可视化图表、文档排版、报表样式——测试全绿但长得一塌糊涂的惨案，大家都遇到过。
- **落地方式很简单**：在 AGENTS.md / 系统提示里写明验收标准时，加一句「改完后启动服务并截图（或导出产物），对照需求逐条自查后再交付」。Claude Code / Kimi Code 都能读图，截屏工具一行命令的事。
- **进阶版**：像 Kimi 那样把「运行证据」也喂回去——日志、录屏、测试输出，让 agent 基于证据迭代，而不是基于猜测。我们正在做的 nano-symphony 可观测性，本质上就是给长跑 agent 补这条回路。

**顺手两个算账提醒**（都是这次 Kimi 方案文档里写明的）：
1. **K2 用户该搬家了**：12 月底 K2 全线下线，新项目直接从 `kimi-k3.1-pro` 起步，1M context 对这种「几十个脚本 + 设计文档一起塞」的场景是刚需，256K 真撑不住。
2. **长任务先算钱**：20 小时连跑这种场景 token 消耗是滚雪球的。省钱两个口子——缓存命中 ¥1.30 vs 输出 ¥68，agent 反复读同一个工程文件时，缓存命中与否直接决定账单；批量推理六折。把长任务拆成「一次能跑完的粒度」，也是对钱包的尊重。

## 技巧二：Claude Code 11/9 双连更 —— Fable 5.2 接棒默认 + remote-control 多会话统一配置

**第一件：模型轮换**。Fable 5.2 成为默认 Fable 模型：1M context、$8/$40 每 Mtok、缓存读只要 $0.20。做长上下文任务（整库分析、长文档）的同学可以试试把它放进 `--model`。同时 **Opus 4.8 的 fast mode 月底正式移除**，还指望它的记得迁 Opus 5.5——11/30 之后 fast mode 请求直接报错，不会自动回落到标准模式。

**第二件：远程党狂喜**。`claude remote-control` 新增 `--settings <file>`：给这个 remote-control 实例启动的**每一个会话**统一套一份 settings 文件，改配置不用逐个会话补。设置文件变更后，新会话要等 remote-control 重启才生效——注意这个语义，改完记得重启实例，不然你以为改了其实没改。

**一个 gateway 用户的小预警**（来自 v2.1.319，11/6 发布）：走 LLM gateway 的会话，如果服务端审查不了某个操作，auto mode 不再用内置分类器兜底，而是**直接转人工确认或拒绝**（提示「Auto mode can't check actions in this session」）。所以最近 gateway 用户会觉得「auto 模式怎么确认变多了」——不是 bug，是策略收紧。Bedrock / Vertex / Foundry 直连不受影响。

## 🥚 彩蛋：Omarchy 4.5「Quattro RS」—— 最 agent-ready 的 Linux 桌面

11/5 发布的 Omarchy 4.5 被外网称为「今天能装到的最 agent-ready 的 Linux 桌面」：内置二十多个 coding agent 的 lazy launcher、一键呼出 agent 窗口、下拉控制台直接启动默认 agent、**多订阅自动切换**（一个 agent 挂几个号，额度用完自动切）、为同时跑多个 agent 设计的 tmux 布局，还有顶栏面板实时显示各家 plan 的额度余量。

CLI agent 重度玩家（说的就是我们这类）看了很难不心动。它最妙的设计是那个**额度看板**——把「我这个月还剩多少」从盲区变成常驻信息，和 11/7 讲的 token 算账是同一个思路：透明度才是省钱的起点。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| 结果「测试全绿但不像话」的任务 | AGENTS.md 加一句：改完截图自查，对照需求逐条验收后再交付 |
| 还在用 K2 系列 | 12 月底下线，迁 `kimi-k3.1-pro`，1M context |
| 跑 Kimi 长任务 | 盯住缓存命中率（¥1.30 vs ¥68），任务拆到一次能跑完 |
| 用 Claude Code 长上下文 | 试 `claude-fable-5-2`；Opus 4.8 fast mode 11/30 移除 |
| remote-control 管理多会话 | `--settings <file>` 统一配置，改完重启实例 |
| gateway + auto mode 用户 | 确认变多是策略收紧（v2.1.319），不是坏了 |

---

*Generated on 2026-11-10 · 来源：Kimi 游戏开发与创意工作解决方案官方发布（2026-11-02，凤凰科技 / woshipm / 掘金社区解读）、Claude Code Changelog（2026-11-09，Fable 5.2 / remote-control --settings / Opus 4.8 fast mode deprecation）、koromo.io Claude Code auto mode 指南（2026-11-07，v2.1.319 gateway 行为变更）、wearacceptall Omarchy 4.5 评测（2026-11-05）*
