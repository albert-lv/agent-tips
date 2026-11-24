# 2026-11-24 Agent Tips：旗舰对轰周，GPT-6.2 正面刚 Fable 5.2

> 本周热点：11/17 OpenAI 甩出 **GPT-6.2（Astra + Sol）**，正面硬刚 11/9 刚发布的 **Claude Fable 5.2**。两家旗舰 API 价格一模一样（$8/$40 per 1M tokens），属于贴脸开打。这周全网都在测这俩到底谁强，榜单互有胜负，社区吵成一锅粥。

## 技巧一：别只看榜单，算「每个任务多少钱」

两家价格相同，但**真实花费差了 6 倍多**——这是本周最反直觉的发现：

- Artificial Analysis 估算：GPT-6.2 Astra **每任务约 $4**，Claude Fable 5.2 **每任务约 $26.54**（Fable 输出 token 多 3.5 倍）
- 榜单上 Fable 在 Intelligence Index 领先（64.6 vs 62.1），但 Astra 在 Agents' Last Exam 反超（78.3% vs 72.8%）

**实操建议**：日常编码任务路由到 **GPT-6.2 Sol**（$2/$10，比 Astra 便宜 4 倍，Cognition FrontierCode 2.0 上只比 Astra 差 0.5 分）；真正难的架构级任务再上旗舰。在 agent 配置里写个简单路由规则，一个月能省出一顿火锅钱。

## 技巧二：Astra 的长上下文有个隐藏附加费

GPT-6.2 Astra 标称 1.05M 上下文窗口，听着很爽，但有个坑：

- **输入超过 272K tokens，整个请求按 2x 计费**：输入/缓存变 $16/$0.80，输出变 $60
- Claude Fable 5.2 的 1M 窗口是**全程平价**，没有这条线

**实操建议**：跑长会话 agent（比如整库重构、超长文档分析）时，如果上下文快摸到 272K，主动 compact / 开新会话，比硬扛便宜一半。OpenClaw 用户可以靠 provider-native compaction 自动压缩，别心疼那点上下文，账单更疼。

## 快讯速览

- **Kimi 用户注意**：K2 系列模型已对新注册用户关闭，**2026 年 12 月底全部退役**。工作流里还钉着 `kimi-k2` 的记得迁移到 K3 系列，别到时候脚本集体罢工 🫠
- **Claude Code 2.1.321** 已把 Fable 5.2 设为默认旗舰；另外 2.1.319 起会警告 claude.ai 登录过期后悄悄切到 `ant auth login` 计费的问题——收到警告记得检查自己走的是订阅还是 API 计费
- **Cursor 本月三连发**：Live Apps（11/5）、语音对话（11/6）、多人协作 Projects（11/10），配合八月的 SpaceX 收购案，这 IDE 现在是整活之王

---

*数据截至 2026-11-24，来源：OpenAI/Anthropic 官方定价、Artificial Analysis、LMArena WebDev 周报（11/15-11/22）*
