# 10-21 — 改价地震 + 开眼神器：Cursor 账单大洗牌，Agent 一键长眼睛

## 🤔 这两天的 Agent 圈，一地震一神迹

先把时间线摆上：

- **10/12–13**：Cursor 官网定价页悄悄换了天——老熟人 Pro $20 / Pro+ $60 从页面上消失，取而代之的是 **The X Subscription**（Cursor × Grok × X Premium 三合一订阅）：Plus $8 / Premium $30 / Super $100 / Ultra $200。老用户不动就没事，但**新注册和免费版升级只能走新方案**。
- **同一周**：GitHub 上有个个人项目 **Agent Reach** 冲到了 Trending 第一——10/02 单日涨星 **+1,696**，总星 **9 万+**，把一众大厂项目按在地上摩擦。

两件事看似不搭，其实是一条线：**Agent 工具在疯狂「收租」，而社区在疯狂「找免费的路」**。今天一个帮你守住钱包，一个帮你打开眼界。👀

## 🛠️ 技巧一：Cursor 新套餐，切之前先做一道小学数学题

新方案三个关键变化，每一条都藏着坑：

1. **额度每周刷新，不再按月**。官方话术是「某天爆肝不会烧光整月」，但换算下来重度用户要重新对表自己的节奏；
2. **Cursor、Grok、X 共用一个额度池**。你在 Grok 上聊嗨了、Imagine 生图生爽了，写代码的额度同步缩水——共享钱包，互相挤兑；
3. **最狠的一条：第三方模型不再包进套餐**。Plus $8 档**只有 Grok 模型**；想用 Claude / GPT，Premium 起按 API 原价另付。

所以**别看到 $8 就冲**。官方给的决策公式就一句：

```
你的月成本 = 档位月费 + 第三方模型按 API 标价的实际消耗
```

**实操三步走**：

```text
① 打开 Cursor Usage 页面，翻出近 30 天「Other Models」那一栏的 token 消耗
② 对照各家 API 标价，把第三方用量换算成美元
③ 如果你的主力是 Claude / GPT → 留在旧 Pro $20（权益不变，留着就是赚）
   如果你的主力已经是 Grok / Composer → 可以切，前 6 个月 Premium $20、Super $60（原价 30/100）
```

⚠️ 三个红线记牢：**切换不可逆**（帮助页白纸黑字）；Apple 渠道扣款的切完官方不退那一笔；Enterprise 部分账号压根没有切换入口。另外顺手记一句行业八卦——Cursor 同期还收购了 Continue.dev，开源 IDE 插件赛道又少一员，老用户且用且珍惜。

## 🛠️ 技巧二：Agent Reach —— 一行命令，让你的 Agent 真正「上网」

这是今天真正的重头戏。你的 Agent 是不是经常这样：

- 「去 GitHub 搜搜类似项目」→ 认证配置卡半天；
- 「看看 Reddit 上大家怎么评价」→ 403，匿名接口被封；
- 「去 X 上搜一下舆情」→ Twitter API 付费，起步每月 $200+。

**Agent Reach** 把这些一次性踩坑压缩成一句话。作者 Panniantong 是个人开发者，MIT 协议，思路非常暴力：**不做爬虫，做路由**——每个平台接多条候选后端，实时探测哪条路通，自动切换，用户零感知。B 站风控封 yt-dlp？路由秒切备选；某平台 CLI 停更？后台早就换好了。

装好之后的 Agent 能读 16 个平台：

```text
网页 / GitHub / YouTube / RSS / 全网语义搜索 / Twitter / B站 / Reddit
小红书 / Facebook / Instagram / LinkedIn / V2EX / 雪球 / 小宇宙 / Boss直聘
```

对，包括**小红书**和**B站**——中文互联网的高地，别的工具基本全挂。Cookie 完全本地存储，全程零 API 费用。

**安装就一句话**，粘给你的 Agent 就行：

```text
帮我安装 Agent Reach: https://raw.githubusercontent.com/Panniantong/agent-reach/main/docs/install.md
```

Agent 会自动完成 pip 安装、系统依赖检测（Node.js / gh CLI）、Exa 语义搜索接入、SKILL.md 路由注册，最后跑 `agent-reach doctor` 自检。三条核心命令背下来：

```bash
agent-reach install     # 安装 / 补装渠道
agent-reach doctor      # 全渠道体检，哪条路通哪条路堵一目了然
agent-reach configure   # 重新配置 Cookie / 代理
```

装完直接上强度：

```text
你: 去 X 和 Reddit 搜一下「Agent Reach」的社区口碑，整理 10 条真实反馈
Agent: （真的去搜了，还带回链接和原文）
```

兼容 **Claude Code / Cursor / Windsurf / OpenClaw / Codex** 等一切能跑 shell 的 Agent。调研竞品、追舆情、挖资料——从此不用你亲自开浏览器当人肉爬虫。💀

## 📋 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| Cursor 要不要切新套餐 | 先看 Usage 里 Other Models 消耗，主力是 Claude/GPT 就**不切换** |
| 真想切，薅半价 | 前 6 个月 Premium $20 / Super $60，但**切换不可逆**，想清楚 |
| 让 Agent 读 X / Reddit / 小红书 / B站 | 粘一句「帮我安装 Agent Reach」+ 官方 install.md 链接 |
| 联网能力体检 | `agent-reach doctor`，哪条路通一眼看穿 |
| 别再用付费 Twitter API | Agent Reach 零 API 费用，Cookie 本地存储 |
