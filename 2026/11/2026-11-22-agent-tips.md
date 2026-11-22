# 2026-11-22 —「榜一是个话痨 + 官方补丁还没来」：Gemini 4 Neon 的账单藏在思考档位里，Claude Code 泄密进入"有回应、没补丁"阶段

> 周四上架的 **Gemini 4 Neon** 迎来了第一波独立测评（11/19-20），结论很有意思：榜一是真的，话痨也是真的——它的"便宜"要看你会不会用。另一边，**Claude Code 泄密事件**终于等来了官方声音：Boris Cherny 在 Hacker News 亲自回应，建议用户切 auto mode——但当场被社区抓住 auto mode 也存敏感命令，本人回复 "good catch"。今天讲两件立刻能用的：怎么把 Neon 用出性价比，以及官方补丁落地前的"双保险"自查。

## 📰 今日热点背景

- **Gemini 4 Neon 独立测评出炉（11/19-20）**：发布后第 2-3 天，Artificial Analysis / dev.to 等第三方数据陆续到位——AA Coding Agent Index 58.6 登顶、Intelligence Index 61 排第三，都验证了 Google 的口径。但测评也扒出它的隐藏属性：**话痨**。xhigh 档跑一轮 Intelligence Index 要吐 **283M output tokens**，是 GPT-6.2 Astra（65.8M）的 4 倍多。
- **Claude Code 泄密事件 · 官方回应**：针对 Apiiro 报告（2,539 个密钥躺进约 2,000 个公开仓库），Boris Cherny 在 HN 承认问题，给了三条：`.claude/settings.local.json` 永远不该进 git、建议改用 auto mode、团队正在做内置 secret scanning 原型。但马上有评论指出 auto mode 同样会把敏感命令写进 settings——Cherny 回了个 "good catch"。也就是说：**补丁在路上，但还没上**。
- **Agent 安全成了收购热点**：CrowdStrike 被曝 4.95 亿美元连收两家 AI agent 安全公司（Fiddler AI + Dreadnode），Palo Alto Networks 还在谈 5 亿收 Clutch Security。巨头开始给 agent 买"身份证"了。

## 技巧一：Gemini 4 Neon —— 榜一的话痨，账单藏在思考档位里

Neon 的 headline 价 $3/$15 只是故事的一半，真实账单取决于你开哪档思考：

| 档位 | AA Intelligence Index | 每个任务成本 | 性价比 |
|---|---|---|---|
| `high`（默认） | 59.6 | **$3.74** | ✅ 日常就停在这 |
| `xhigh` | 61 | $6.44 | 多花 72% 的钱，只换 +1.8 分 |

三条实战用法：

1. **默认 high，别手痒开 xhigh**。+1.8 分在榜单上是名次，在你的工单上基本无感，但账单多了七成。xhigh 只配留给"卡了两小时没头绪的架构题"这种狙击场景。
2. **把缓存吃满**：cached input 只要 $0.15/M（比标价便宜 95%）。agent 工作流每轮都把同一大坨上下文重发一遍，这 95% 的折扣就是为 agentic 场景准备的——长任务里稳定的大材料（家规文档、长 README、参考代码）放 prompt 前缀，让它一直命中缓存。
3. **盯紧 11/30 这个时间窗**：Gemini 4 Carbon 的促销价 $2/$10 月底到期，12/1 起涨到 $4/$20——届时 Neon 会顺势变成"全系性价比之王"。近期有大批量 coding 任务且嫌 Carbon 慢的，可以趁促销最后一周锁一批；日常直接 Neon high 档，不用纠结。

最后一个来自独立测评的提醒：Neon 被评价为"**像开发者一样测试，不像攻击者**"——安全相关的代码它能写得又快又对，但缺对抗思维。涉及鉴权、加密、注入防护的改动，它写完必须人审 + 补恶意用例，别直接合并。

## 技巧二：泄密双保险 —— 官方有回应，但补丁还没来

Cherny 的 HN 回应给了方向，也暴露了现状：auto mode 不是解药（"good catch" 已经说明它也会存敏感命令）、内置 secret scanning 还在原型阶段。**在官方补丁落地之前，自己上双保险**：

**第一重：验证 ignore 真的生效。** Claude Code 只把 settings.local.json 写进你本机的全局 git excludes——这玩意儿不随仓库走，队友的电脑、CI、你换台机器，全部裸奔。在每个仓库里跑一句：

```bash
git ls-files | grep -i claude    # 有输出 = 已被 track，ignore 管不着它，危险
```

有输出就先 `git rm --cached` 移除跟踪，再把 `.claude/settings.local.json` 写进**仓库自己的** `.gitignore`，随仓库分发。

**第二重：查自己的暴露面 + 人眼兜底。**

```bash
gh search code --owner <你的用户名> settings.local.json   # 全 GitHub 搜自己有没有传上去
grep -nE 'Bash\(' .claude/settings.local.json | grep -iE 'pass|token|key|secret|://[^/]*:[^@]*@'
```

注意一个反直觉的数据：Apiiro 确认带密钥的 1,122 份文件里，**TruffleHog 漏了 479 份、gitleaks 漏了 558 份，两家都漏的有 215 份**——扫描器全绿 ≠ 干净。allow 列表定期用上面那条 grep 人眼过一遍，看到 `user:password@`、`sk-`、`ghp_`、`AKIA` 开头的字符串先轮换密钥再清理。

## 🥚 彩蛋：三个边角料

- **给 agent 上"身份证"是下一个战场**：CrowdStrike 4.95 亿美元连收 Fiddler AI + Dreadnode，Palo Alto 5 亿谈 Clutch Security——全在买"agent 身份与权限"这张船票。翻译成个人版最小权限：能给子 key 别给主 key，能设限额别裸奔，token 出事**先轮换再排查**。
- **ADE Index 热帖**：Hacker News 上有人做了个 agentic coding 工具榜单，已收录 **975 个**工具、每日刷新——"全村名录"级别的选工具参考，选工具困难症患者的福音。
- **Codex 用户注意 12 月**：OpenAI 宣布 12 月起 Plus/Pro/Business 套餐的用量配额在 **ChatGPT 和 Codex 之间共用**——聊天聊嗨了可能挤占写代码的池子。Codex 重度用户提前规划额度，别到月底才发现 agent 没粮了。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| Neon 日常用 | 默认 `high` 档别动（$3.74/task），xhigh 只留狙击题 |
| Neon 跑长任务 | 大材料放 prompt 前缀，吃 cached input 95% 折扣 |
| 想用 Carbon | 11/30 促销价截止，$2/$10 → $4/$20，要锁趁早 |
| 换电脑 / 队友 clone 仓库 | `git ls-files \| grep claude` 验证 ignore 生效 |
| 扫描器报全绿 | 别信，allow 列表定期 grep 人眼过一遍 |

---

*每天 08:42 更新 · 向上 & 小惠的 Agent 工具笔记*
