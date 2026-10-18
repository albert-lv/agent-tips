# 10-18 — Mods 早市开张：先捡官方「你应该知道」，再学验货三件套

## 🤔 改装车间开业 17 天，今天去逛早市

先把时间线补齐——

- **10/01**：Mods 系统上线，官宣帖 210 万浏览；三天后社区目录收录 **1740 个公开 mod**，这生长速度堪比雨后蘑菇；
- **10/15**：Claude Code 又发一版大更新——更丰富的插件 hooks、新的 session 命令、managed-agent 引导，登录审批还多了个 **Deny 按钮**（以前只能干等着，现在可以果断说不）。

也就是说，改装车间已经度过「开业尝鲜期」，进入**早市阶段**：摊子多了，鱼龙混杂，今天不讲概念，讲两件事——**捡漏**和**验货**。🧺

## 🛠️ 技巧一：先装官方的「You Should Know」—— 给你配一个盯梢的

社区 mod 再多，第一个该装的其实是**官方自己藏的**这个：10/03 悄悄上线的内置 mod **「You Should Know」**。

它背后跑着一个 side agent，专门扫描你和 Claude 的输出，把「你可能漏看的东西」标出来——比如 Claude 顺手改了一个你没让它碰的文件、某个警告被埋在一屏输出中间、测试其实悄悄挂了半组。长会话和复杂任务里，它就是你多出来的一双眼睛。

启用只要一行：

```
/plugin enable cc-plugin-you-should-know@builtin
```

前提一句带过：目前限**官方渠道 + 开了 telemetry 的会话**。属于「能开就开」系列，毕竟免费的官方外挂，不装白不装。👀

## 🛠️ 技巧二：1700+ 摊子怎么淘——两个先拿的，外加验货三件套

早市捡漏原则：**先拿解决真痛点的，不拿花里胡哨的**。本周社区口碑里这两个最实诚：

- **`agent-flow`**：跑 `/flow` 弹出一棵**实时子代理树**——哪个 subagent 在跑、跑到哪步、谁卡住了，一屏看完。配合 10-12 讲的「指挥部」心法，从「盲信任」变「可透视」；
- **`cctop`**：在 Claude Code 里嵌一块 **btop 风格的仪表盘面板**，会话状态、资源占用尽收眼底，终端颜值党狂喜。

装法就是标准两步：

```
claude plugin marketplace add <作者/repo>
claude plugin install <mod名>@<marketplace>
```

**但重点在验货**。mod 跑在沙箱外、拿着你的全部权限，装之前花 60 秒过一遍「验货三件套」：

1. **`claude plugin validate`**：静态体检。它会列出这个 mod 订阅了哪些事件、调了哪些 API、读了写了哪些环境变量——输出大概长这样：

   ```
   ❯ ./register.js hooks: session.start, tool.call, command.run{command=tally}
   ❯ ./register.js calls: $.command.register, $.ui.invalidate
   ✔ Validation passed
   ```

   一个「天气预报」mod 却声明要读你的 `AWS_SECRET_ACCESS_KEY`？转身就走，别回头。
2. **`claude plugin test`**：不开会话、不联网，直接给 hooks 发合成事件实测行为，写自己的 mod 时更离不开它。
3. **读源码**：mod 就是明文的 TypeScript，不是混淆字节码。30 秒扫一眼，比任何评测都靠谱。

自己写 mod 的顺手避三个坑：事件名别打错（`"tool.calls"` **不是**合法事件，是 `tool.call`）；API 调用写全 `$.store.get('notes')`，别用解构别名；事件名老老实实写字符串字面量，别搞变量拼接。这三条是社区踩坑统计里的前三名。💀

## 🎯 今天记住这个

| 一句话 | 怎么做 |
|---|---|
| 官方外挂先装上 | `/plugin enable cc-plugin-you-should-know@builtin`，side agent 帮你盯漏看的内容 |
| 早市先拿这两个 | `agent-flow`（子代理实时树）+ `cctop`（仪表盘面板） |
| 装前验货三件套 | `claude plugin validate` 体检 → `claude plugin test` 实测 → 30 秒读源码 |
