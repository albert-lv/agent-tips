# 2026-11-12 —「不再询问」的账单：一次点击，密钥上 GitHub + 断供生效日验收三招

> 今天双热点撞车：① OpenAI 断供 Cursor **今天正式生效**（11/12），昨晚清单做完的今早验收，没做的现在进入「被动排障」模式；② 安全公司 Apiiro 的报告周末刷屏（Ars Technica 跟进）——Claude Code 那个「Yes, and don't ask again」按钮，正把密码明文写进 `.claude/settings.local.json` 送上 GitHub：10 万个公开副本里 **1,122 个带真密钥**。今天一篇教你给自家仓库做「泄密体检」，一篇给断供首日的验收顺序。

## 📰 今日热点背景

- **OpenAI 断供 Cursor，今天执行**：11/12 起 Cursor 直接取用 OpenAI 模型的权限正式关闭（SpaceX 600 亿全股票收购 Anysphere 后，「控制权变更」条款被援引）。CEO 说 OpenAI 模型只占流量 ~5%，但落到依赖它的团队就是 100%。**昨晚是「主动切换」的最后窗口，今天起全是「被动排障」。**
- **Apiiro 安全报告（11/5 发布，上周末发酵，本周刷屏）**：扫描公开 GitHub 发现 **104,185 份** `.claude/settings.local.json`，其中 **1,122 份装着真密钥**——379 个数据库密码、122 个 Supabase service-role key、103 个 Supabase account token、43 组 SSH 登录、23 个 AWS key……连 git 历史、团队共享的 settings.json、agent 聊天记录一起算，**2,539 个密钥散落在 1,994 个公开仓库、63 个组织**。最扎心的是：能溯源的泄密仓库里，**539/1,105 个是 Claude 自己 co-author commit 提交的**——agent 干活顺手把配置文件也 commit 了，"This is Claude Code's own behavior, not the model's"。
- **Anthropic 官方已回应（11/7）**：Claude Code 负责人 Boris Cherny 在 Hacker News 表态——团队正在做防护原型：「让 auto mode 拦住这类情况、内置基础 secret 扫描」都会试。**工具侧修复在路上，但已经流出的密钥不会自己失效，该轮换还得手动轮换。**

## 技巧一：给自家仓库做「泄密体检」—— 10 分钟 5 步自救

先说原理，一句话就够：让 Claude Code 查数据库 / 跑部署时，命令行里带着密码（`PGPASSWORD=xxx`、`sshpass -p`、`mysql -p`），它弹窗问「跑吗」，你点 **"Yes, and don't ask again"**，它就把**整条命令原文**存进 `.claude/settings.local.json` 当 allow 规则——密码跟着一起入库。这文件本该待在你本机：Claude Code 第一次写它时会加进你的**全局** git excludes。但这个保护**不跟着仓库走**：换台电脑 clone、目录后来才变成 git 仓库、agent 顺手帮你 commit——全都绕过。

**今天花 10 分钟走这 5 步：**

1. **翻文件**：本机每个项目找 `.claude/settings.local.json`，逐条读 allow 列表。凡是命令里 `=` 后面跟着疑似密码/token 的（`PGPASSWORD=`、`sshpass -p`、`--token`、`AKIA` 开头），全部记下来。
2. **查历史**：文件删了不等于安全。`git log --all --full-history -- .claude/settings.local.json` 查每个仓库——**历史里 commit 过 = 现在还在**（fork、clone、GitHub 缓存全会续命）。Apiiro 统计中招仓库的中位暴露时长是 **217 天**，有的甚至躺了一年多。
3. **先轮换，后清理**：发现泄密 → **立刻 rotate**（改密码、吊销 token、重发 key），再删文件、清历史。**顺序千万别反**——只删不轮换，等于给攻击者发「失效前速来」的邀请函。
4. **补 `.gitignore` + pre-commit**：仓库自己的 `.gitignore` 加 `**/.claude/settings.local.json` 和 `.claude/.credentials.json`（本机 OAuth 登录凭证——GitGuardian 在 **371 个公开仓库**里捞到 1,366 个 token）。全局 excludes 只管一台电脑，仓库自带 gitignore 才跟着项目走；团队项目顺手加 pre-commit hook 拦这个路径。
5. **改习惯**：给 agent 派活时**别让密码出现在命令行**。密码放 `.env` / 环境变量，命令里只引用（`psql "$DATABASE_URL"`）。「不再询问」可以继续点——但前提是那条命令本身干净。

> 冷知识：Apiiro 拿 gitleaks + TruffleHog 扫了全部 10 万份文件，**215 份带密钥的文件俩工具一个都没报**——因为命令行里的密码长得太像普通单词，格式型扫描器全漏。所以别指望扫描器兜底，**人眼过一遍 allow 列表**才是正经体检。

## 技巧二：断供首日验收 —— 今早 5 分钟，确认流水线还活着

昨晚的 15 分钟清单（11-11 那篇）做完没？做完用这 3 招验收，没做的现在补：

1. **先查自动化，再查人**：cron / CI / 脚本不会自己适应，今早第一批红灯一定来自它们。翻昨晚 9 点后的定时任务日志，搜 `model` / `not found` / `invalid model` / `401` / `403`——Cursor 里写死 `gpt-*` 的调用今天基本就报这组错。
2. **Tab 不用查，Agent 才要查**：断供只影响 OpenAI 模型的路由，Tab 补全、Composer、Claude/Gemini/Grok 模型全部不受影响。**重灾区是 Agent 模式、CLI、SDK 里点名 OpenAI 模型的自动化**——对着这几处逐个点一遍。
3. **盯 Other Models 额度池**：原本走 bundled 的第三方模型调用，现在按 API 原价从这个池子扣 credit。今天高强度用一天，明早看余额——比你想的浅的话，本周就得定新路由策略（BYOK 或 gateway）。

> 一个没被官方确认过的坑：**BYOK 别当唯一后路**。开发者报告个人 OpenAI key 在 Cursor 里本地 Chat 尚可用、Agent/Edit 一直半残，开了还可能干扰 Composer。Plan B 至少备两条：Codex CLI / Claude Code 该装的装上，关键时刻切换成本最低。

## 🥚 彩蛋：三个周边速报

- **Codex 用户别笑**：GitGuardian 顺带扫了 `.codex/auth.json`——**536 个公开仓库、1,064 个 token**。Claude 用户体检完，隔壁 Codex 账号也顺手查一下，谁家锅底都有灰。
- **`CLAUDE.md` 才是隐形富豪**：GitGuardian 在里面数出 **6,155 个密钥、2,537 个有效**——开发者把真连接串直接贴进给 agent 的说明文档了。写 CLAUDE.md 记住一条：演示格式随便写（`localhost:5432`），真库的地址密码**一律引用环境变量名**，别写真值。
- **网关党特供**：GitGuardian 发现泄密的共享 `settings.json` 里，1/4 是 `ANTHROPIC_BASE_URL` + `ANTHROPIC_AUTH_TOKEN` 指向第三方网关的组合（DeepSeek 205 个有效 key、OpenRouter 62、MiniMax 50……）。用中转网关接入的，这份文件**打死别 commit**。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| 点过「不再询问」 | 今天翻遍本机 `.claude/settings.local.json` 的 allow 列表，命令里带密码的**先轮换密钥** |
| 仓库历史 commit 过这文件 | `git log` 查证 → rotate → 清历史；仓库 `.gitignore` 补 `**/.claude/settings.local.json` |
| 给 agent 派带密码的活 | 密码进 `.env`，命令行只写引用——「不再询问」点才安心 |
| 用第三方网关（DeepSeek/OpenRouter 等） | `ANTHROPIC_BASE_URL` + token 组合别进 git |
| Cursor 用户 | 今早查自动化日志的 model 报错 + 盯 Other Models 余额；BYOK 只算半条后路 |
| 等官方修复 | Anthropic 已在原型「内置 secret 扫描 + auto mode 拦截」（11/7 表态），升新版即获得——但**旧密钥仍要手动轮换** |

---

*Generated on 2026-11-12 · 来源：Apiiro Research「Coding Agents Are Leaking Your Secrets to GitHub」（2026-11-05，apiiro.com；Boris Cherny HN 回应 2026-11-07）、Ars Technica / The Hacker News 跟进（2026-11-07）、GitGuardian Blog（2026-11-09，含 .codex/auth.json 与网关 token 数据）、Shattered / CellCog / Brandefense 梳理（2026-11-09~10）、OpenAI 断供 Cursor 官方公告与 11/12 生效时间线（adersaytech 2026-08-30、shattered 2026-10-21 复核）*
