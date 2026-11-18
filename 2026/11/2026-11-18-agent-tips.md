# 2026-11-18 —「12 天倒计时 + 连夜打补丁」：Opus 4.8 Fast 月底退役，Claude Code 2.1.325 堵上一批权限洞

> Anthropic 这周明显在赶年末 KPI：11/13 发布 Claude Code 2.1.325（一次修掉 130+ 条，大半是安全相关）、11/17 平台侧上线 Linked Billing beta、还有一颗早就埋好的倒计时炸弹——**Opus 4.8 的 Fast mode 将于 11/30 正式移除**，今天算起只剩 12 天。今天讲两件必须动手的事：**换车（4.8 → 5.5）**和**补洞（升级 + 审计权限规则）**。

## 📰 今日热点背景

- **Opus 4.8 Fast mode 退役倒计时（11/30）**：10/30 官方公告弃用，**11/30 之后所有带 `speed: "fast"` 的 `claude-opus-4-8` 请求直接报错**。Fast mode 本身没死——Opus 5.5 接棒，但按标准价 **2 倍**计费（对比：Codex 的 Fast 是 2.5 倍速烧额度）。
- **Claude Code 2.1.325（11/13）**：安全大补版本。修掉一批权限检查漏洞——**复合 Bash 命令可以绕过确认弹窗**、**allow 规则可能实际执行了别的程序**、部分 deny/ask 规则对某些命令不生效；还有 zstd 解码的安全修复。官方建议所有人尽快升级。
- **Claude Platform 11/17**：Linked Billing beta 上线——客户可以通过自己的 Claude 组织替你应用里的 API 用量付费。做 AI SaaS 的，结算体验史诗级优化。
- **DeepSeek 联网工具 11/1 起按次计费**：在 DeepSeek Harness / Claude Code / Codex 里走 DeepSeek 服务端 Search 和 Fetch，每 1000 次成功请求各收 5 元。重度联网查资料的账单要多算一笔。

## 技巧一：Opus 4.8 Fast 换车指南 —— 12 天内做完这三步

如果你不确定自己是不是受影响人群，大概率不是；但如果团队里有人 pinned 了旧模型，或者在脚本/SDK 里写死了 `claude-opus-4-8` + fast，按下面三步走：

1. **排查**：交互用户运行 `/model` 看当前模型；脚本/SDK 用户全局搜 `opus-4-8` 和 `speed.*fast`。Claude Code 里 5 月起 fast mode 就默认跑在 Opus 4.7/4.8 上，**用着默认配置的重度用户最可能中招**。
2. **换车**：模型切到 **Opus 5.5**（CLI 里 `/model`，脚本里改 model 字段）。Fast mode 体验保留，代价是单价 ×2——所以顺手想一下哪些任务真的配得上 fast，日常编辑用标准档就行。
3. **验证**：换完跑一轮你最长的那个 agent 任务，确认没有 hidden dependency 还指着 4.8（比如某个 plugin 的 frontmatter 或 CI 配置）。

一句账面上的话：Fast 是给「等不起」的任务准备的。如果你换到 5.5 标准档之后发现体感没差多少——恭喜，**这部分 ×2 的钱本来就省得下来**。

## 技巧二：2.1.325 安全大补 —— 升级之后，再做一次「allow 规则审计」

这次修补里最扎眼的是：**过去某些复合命令（`A && B`、`A | B` 这类）能绕过权限确认直接跑**。洞堵上了，但它提醒我们一件事：很多人 `.claude/settings.json` 里的 allow 列表是「用着用着就宽了」。

升级 + 审计，两步：

```bash
claude update && claude --version   # 确认 ≥ 2.1.325
```

然后打开你的 `settings.json` / `settings.local.json`，对着这三条体检：

- **通配符过宽的 allow**：`Bash(npm run *)`、`Bash(git *)` 这类——旧版里 allow 规则有可能「名义放行 A、实际执行了 B」，修好之后规则收紧，**过宽的写法也可能真的按字面放行了过宽的东西**；
- **local 文件别进 git**：`settings.local.json` / Kimi 的 `local.toml` 老规矩，本机用，别 commit；
- **deny 列表查漏**：如果你写过 `deny` 规则但感觉「好像没怎么拦过」，这次修的正是不生效的情况——升级后行为会变，提前做好心理准备。

**顺手一个新 UX**：2.1.325 里，组织策略拦截请求时的报错下方会**直接告诉你怎么继续**——`/rewind` 换个说法重发，或 `/clear` 清场。官方把「后悔药」写进了错误提示，以后遇到红色报错别急着关窗口，先读一遍它给你的下一步。

## 🥚 彩蛋：三个本周边角料

- **Linked Billing（11/17）**：做 AI 产品的可以让客户用自己的 Claude 组织给你的应用买单，Console 里还能设支出上限。To B 的朋友们，「客户自助结账」这个功能等了两年。
- **OpenCode vs Claude Code 对比（11/16 Morph）**：OpenCode 210K star 领先，但 Claude Code 贡献了**公开 GitHub commit 的 10%+**（3 月峰值每天 32.6 万次），预计年底冲 20%。结论很直白：star 是态度，commit 是生活。
- **DeepSeek 联网收费（11/1 生效）**：每 1000 次 Search/Fetch 各 5 元。agent 跑长任务疯狂查资料的话，记得把它算进月度账单——上月「哪家 coding agent 最省钱」的评测口径要更新了。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| 脚本/SDK 里写死了 `opus-4-8` + fast | **12 天内**换 Opus 5.5，11/30 之后直接报错 |
| 日常任务用了 fast mode | 试一次标准档，体感没差就省一半钱 |
| Claude Code 版本 < 2.1.325 | `claude update`——复合命令绕权限的洞刚堵上 |
| `settings.json` 里 allow 列表很宽 | 顺手审计，通配符收窄，local 文件别 commit |
| 组织策略拦了请求 | 看报错下方的提示：`/rewind` 改说法重发 |

---

*每天 08:42 更新 · 向上 & 小惠的 Agent 工具笔记*
