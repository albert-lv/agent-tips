# 2026-11-14 —「屠榜分不是日常分」：Fable 5.2 横扫榜单的这一周，教你把跑分翻译成自己的用法

> Claude Fable 5.2 发布（11/9）刚满一周，本周 Benchmark 成绩陆续出分：**Arena WebDev 1838 分登顶（11/11，历史第一）**、**FrontierCode 2.0 49.1%（11/12，132 个真实开源仓库任务）**、SciCode 67.7%（11/10）。榜单很燃，但先别急着把日常会话全挂上 Fable——**这些 headline 数字几乎全是 max/xhigh effort + 顶配 scaffold 跑出来的**。今天的主题：怎么把「榜单分」翻译成「你的分」。

## 📰 今日热点背景

- **Fable 5.2 屠榜周**：11/9 发布以来接连出分——Arena WebDev 1838（11/11）、FrontierCode 2.0 49.1%（11/12，Cognition 真仓库评测，「maintainer 会不会 merge」标准）、SciCode 67.7%（11/10）、ProgramBench 19.0%（11/9，从二进制重建程序，全场最高但分数本身很低，说明这题还很难）。Claude 系本周基本包揽 coding 榜前排。
- **配额现实**：Fable 5.2 已是 Claude Code 新默认，但 Pro/Max/Team 套餐里它**最多占每周用量上限的 50%**——不是无限火力。
- **榜单脚注运动**：各家榜单现在普遍标注 effort 档位（high/medium/xhigh/max）和 scaffold（harness）。同一个模型，medium 和 max 能差出十几个百分点——不看脚注比数字，等于不看单位比体重。

## 技巧一：看榜单先翻三个脚注，然后给自己跑一份「个人 mini-eval」

 Benchmark 现在是个「带标注的谎言」时代——数字都是真的，但条件各不相同。看任何一个榜单，先翻三个脚注：

| 脚注 | 看什么 | 为什么重要 |
|---|---|---|
| **effort 档位** | high？xhigh？max？ | 同模型不同档位能差 10~20 个点，**max effort 的账单可能是 high 的 3~5 倍** |
| **scaffold/harness** | 裸 bash？完整 agent？ | SWE-bench 上「模型分」和「agent 分」经常差 20+ 个点，harness 才是大头 |
| **出分日期** | 模型发布后多久测的 | 发布后 1 周内的分数，prompt 调优还没收敛，参考权重打折 |

然后是关键一步：**别信别人的榜单，给自己跑一份 mini-eval**。从你最近的 backlog 里挑 3 个**已经解决过**的真实任务（bug 修复、小 feature、重构各一），脱敏后存档成 fixture。同一 prompt、同一份上下文，分别用「日常档」（high effort + Sonnet 5.5）和「顶配档」（xhigh + Fable 5.2）各跑一遍，看三个指标：

1. **一次通过率**——agent 的改动不返工直接合入的比例；
2. **你的 review 时间**——diff 越干净、解释越到位，你审得越快（这是榜单永远测不了的）；
3. **revert 率**——参考 claude.dev 的做法（10-24 那篇），这是终极质量指标。

跑完你会发现一个常见结论：**顶配档在一次通过率上确实碾压，但很多日常任务上「好 5 个点、贵 3 倍」**——这时候答案不是「全上顶配」，而是「分级配车」（技巧二）。mini-eval 的价值在于：它是用**你的代码库、你的任务分布**测的，比任何公开榜单都贴近你的账单。

> Claude Code 实操回顾：effort 旋钮上周讲过——平时 high，硬仗 ultrathink（现在等于 xhigh），杂活子代理 `CLAUDE_CODE_SUBAGENT_EFFORT=low`。今天加一层：**先把 3 个 fixture 建好，以后每次换模型/换档，半天就能出你自己的对比数据**。

## 技巧二：Fable 5.2 每周只有 50% 额度——把它当「狙击枪」不是「机关枪」

Pro/Max/Team 用户注意：Fable 5.2 再强，每周可用量也**只有配额上限的一半**（Anthropic 官方口径，企业版看合同）。用错姿势，周三就把额度打光了。推荐的分级配车：

| 任务类型 | 用什么 | 理由 |
|---|---|---|
| 跨 5+ 文件的架构改动、诡异并发 bug、线上事故复盘 | **Fable 5.2 + ultrathink** | 这类活 diff 错一处代价远大于 token 钱，值得顶配 |
| 最终 self-review / 难点 PR 的二审 | **Fable 5.2** | 一次终审比三次返工便宜 |
| 日常 feature、补测试、改名重构、文档 | **Sonnet 5.5（默认档）** | Terminal-Bench 4.0 仍有 61.8%，足够硬 |
| 搜索、跑测试、格式检查等杂活 | **子代理 + Haiku 5.5** | 便宜 75%，上下文窗口还大 |

两个防翻车细节：

1. **`/model` 随时切，不用重开会话**。开局让 Sonnet 探路、摸清结构，确认要动大手术时切 Fable——上下文不丢，钱花在刀刃上。反过来也成立：Fable 攻坚完，收尾的测试和 commit message 切回 Sonnet。
2. **额度见底有信号**：`/usage` 看本周消耗曲线，发现 Fable 占比逼近 50% 就主动降级——别等被限速了才被动切（10-27 那篇讲过撞额度改自动压缩续跑，但那是兜底不是策略）。

## 🥚 彩蛋：Cursor 的 agent 装上嘴了 + Kimi 的前端分

- **Cursor 语音对话（11/6 上线，基于 Grok Voice）**：现在可以对着 Cursor 的 agent 口述需求、口述 review 意见。通勤路上把 bug 复述一遍让 agent 先复现，到公司直接看结果——agent 的输入带宽终于从打字解放了。当然，口述完记得让它把结论落盘成文字，语音不产生可检索的记录。
- **Kimi K3.1 Pro 的 Startrise Frontend 82.5%（10/28 出分）**：12 个真实浏览器环境跑的前端任务，这个分数接近饱和区——做前端演示和原型验证，K3.1 Pro 的性价比依然很能打（昨天 2.6.0 的全屏折叠模式正好配它跑长任务）。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| 看到屠榜新闻心痒 | 先翻脚注：effort 档 / scaffold / 日期，三个缺一个都别激动 |
| 想验证新模型值不值 | 建 3 个自己 backlog 的 fixture（已解决的 bug/feature/重构），双档各跑一遍，看一次通过率 + review 时间 + revert 率 |
| Fable 5.2 额度分配 | 架构硬仗和终审才用 Fable，日常 Sonnet 5.5，杂活 Haiku 子代理；每周三 `/usage` 看占比 |
| 会话中途换模型 | `/model` 直接切，上下文保留——探路用便宜的，动刀再上贵的 |
| 语音派活 | Cursor Grok Voice 口述可以，**结论必须落盘成文字** |

---

*Generated on 2026-11-14 · 来源：benchleader.com coding benchmark 汇总（2026-11-11/12 出分）、ssojet.com 11 家 AI coding agent 对比（2026-11，Fable 5.2 50% 配额口径）、Morph 2026 年 10 月榜单（Sonnet 5.5 Terminal-Bench 61.8%）、Cursor changelog（Grok Voice 2026-11-06）、claude.dev 工程实践复盘（见 2026-10-24 篇）、Kimi Startrise Frontend 榜（2026-10-28）*
