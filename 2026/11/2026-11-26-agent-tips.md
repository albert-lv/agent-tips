# 2026-11-26 Agent Tips：OpenAI「11·30 大限」倒计时 4 天——低代码 Agent 画布正在退潮

> 本周热点：下周四（**11/30**）OpenAI 要一口气关掉三样东西——**Agent Builder**、**Evals 控制台与 API**、**v1/prompts API**（可复用提示词对象）。这是 6 月官宣弃用后的正式执行日，距今只剩 **4 天**。几乎同一时间线，OpenAI 模型已于 11/12 正式停止供应 Cursor（Plus 档现在只剩 Grok），GitHub Trending 则被另一个东西屠了榜——**16 个周榜项目里 13 个是 Agent Skill**，最火的还是「去 AI 味」设计三件套（Impeccable / Hallmark / taste-skill）。把这三件事连起来看，趋势很清楚了：**「拖拽搭一个 agent」的低代码时代在退潮，「写进代码仓库、能被 git 管理」的 Skill 时代在涨潮**。

## 技巧一：Agent Builder 用户，这 4 天照着这个 checklist 搬家

如果你（或你公司）还有工作流跑在 OpenAI Agent Builder 上，现在不是「要不要迁」的问题，是「这周末前能不能迁完」的问题。官方给了两条路，各有坑：

1. **导出 Agents SDK 代码**：官方提供导出 TypeScript / Python 代码的入口。重点：**导出不转换工作流图，也不保证行为原样迁移**——迁移后必须用代表性输入重新跑一遍回归测试，别信「导出即用」。
2. **迁到 ChatGPT Workspace Agent**：适合想让业务同学继续用自然语言维护的场景。前提是有 Business / Enterprise / Edu 工作区权限，而且连接的应用、认证、发布配置要**在 ChatGPT 里重新审核一遍**。

两个容易忽略的点：

- **ChatKit 还在 ≠ 你的后端还在**。官方特意强调 ChatKit 保留可用，但 Agent Builder 的托管后端是实打实要关的，别把「界面还在」当成「工作流还在」。
- **官方没说 11/30 之后还能不能导出**。稳妥做法就是**今天**就把所有工作流的 SDK 代码导出存好，哪怕迁移排期在下个月。

顺带一提：同一天关的还有 **v1/prompts API**——如果你在代码里引用了 prompt 对象 ID，把提示词内容搬进应用代码，版本和变量自己管；**Evals 平台**用户则建议迁 Promptfoo（官方点名的参考方案）。

## 技巧二：别再造「一次性 agent」了——GitHub 热榜告诉你，Skill 才是新通货

如果说技巧一是「旧世界拆迁」，技巧二就是「新世界置业」。11 月第 1 周的 GitHub Trending 周榜（16 个项目）里，**13 个是给 Claude Code / Codex / Cursor 用的 Agent Skill**——这是「低代码画布退潮、可版本化 Skill 涨潮」最直白的市场信号：Agent 的产物开始像代码一样被安装、被 fork、被 star，而不是被拖在厂商的画布里。

直接抄作业，最火的「去 AI 味」设计 Skill 一行装上：

```bash
# Impeccable：24 个设计指令 + 62 条确定性「AI 味」检测规则，不调模型、不要 Key
npx impeccable install

# Hallmark：50 套主题材质 + sloplint 在 8 种屏幕宽度下实测，不达标不交付
npx skills add nutlope/hallmark -g
```

装完在 Claude Code / Codex / Cursor 里直接喊：`/impeccable critique the pricing cards`。

这个榜单值得品的不只是工具本身，而是三个共同点：**都要求真实素材不编数字、都在真实浏览器里检查成品而不是让模型自己说「好看」、都能被 git 管理**。这其实就是 Agent 工程化的新家规——你的 agent 经验，应该沉淀成 SKILL.md 进仓库，而不是散落在某个厂商平台的对话框里。呼应 11/25 刚讲的「聊天记录安全」：Skill 文件在你仓库里，traces 在厂商服务器上，哪个更懂你的团队，一目了然。

## 快讯速览

- **ZCode v4.0.0 发布（11/23）**：智谱把 **GLM-5.4** 接进 Coding Plan 并预置，新增视觉能力和更强的泛 Coding 能力；插件商店按登录地区展示，会话卡死、上下文偶发丢失两个老毛病也修了。用国内模型的同学可以升一波
- **Claude Meetings 开测（11/12 起 beta）**：Claude 能以「可见参会人」身份进 Google Meet 开会、记笔记、现场产出材料，Team / Enterprise 档可用。第一次用建议先在内部会议试水，别一上来就让它进客户会 😂
- **Claude Code 2.1.325（11/13）**：修了 zstd 压缩响应解码的安全问题；组织策略拦截报错后会提示用 `/rewind` 或 `/clear` 继续；跑第三方网关不再动不动就 context length 报错。终端党建议 `claude update`
- **「无斑点代码」（no-slop）宣言走红**：一篇复盘 AI 编程一周年的长帖把「模型能写 95% 的代码，但读懂代码仍是人类刚需」推成热议——和上面的 Skill 热榜互为镜像：**写代码越来越便宜，判断力越来越贵**

---

*数据截至 2026-11-26，来源：OpenAI Deprecations 官方页、Agent Builder 迁移指南、GitHub Trending（11 月第 1 周）、七牛云技术周刊、ZCode Changelog、Oxford AI School（11/13）、Claude Code Changelog（2.1.325）*
