# 2026-11-13 —「允许一次，终身有效」：Kimi Code 昨晚双端齐发，两个最被低估的新功能

> 昨晚 Kimi Code 罕见双端同更：**CLI 跳到 2.6.0**（11/12），**Desktop 更到 1.3.4**（11/12）。新功能不少，但大多数是界面微调；真正改变日常体感的是两个「沉默的更新」——**持久化权限规则**（终于不用每个会话重新点「允许」了）和**全屏模式工具调用折叠**（长任务刷屏问题终结）。外加一个方便但要当心的变化：`@` 现在能搜到 `.gitignore` 忽略的文件了——**包括你的 `.env`**（昨天刚讲过密钥体检，懂的都懂）。

## 📰 今日热点背景

- **Kimi Code CLI 2.6.0（11/12 发布）**：全屏模式自动把连续工具调用和思考折叠成摘要行；持久化用户级 + 项目级权限规则；`@` 补全默认包含隐藏路径和 `.gitignore` 忽略的文件；全屏下 `Ctrl-C`/`Cmd-C` 直接复制选中文本；Tower 新增交付物审阅任务。
- **Kimi Code Desktop 1.3.4（11/12 发布）**：自定义界面/对话/代码/终端字体；粘贴长文本自动折叠；`@` 提及支持搜到 `.gitignore` 忽略的文件；终端标签可拖拽排序；截图新增放大镜 + 取色器；内置浏览器可开本地文件、标签页默认静音；支持导入 Google GenAI 服务商；修复断线重连、会话恢复等一批稳定性问题。
- **生态暗线（接上篇）**：昨天讲了 OpenAI 断供 Cursor 正式生效 + Claude Code 密钥上 GitHub 的体检清单。今天 Kimi Code 这俩更新恰好给了两件新武器——**持久化权限规则让「精细化 allow 列表」成本骤降**，而 `@` 能搜到忽略文件意味着 **agent 看得见你藏起来的东西了，昨天的体检别忘了复查**。

## 技巧一：持久化权限规则 —— 把「允许」写成配置文件，别再当点击工人

2.6.0 之前，Kimi Code 的权限确认基本是「会话制」：这次点了允许，下次新开会话，同一命令再弹一次。派活激进一点的人，一天能点掉几十次「允许」——点到后来要么开 yolo（all in，风险自负），要么开 dangerous_command_guard=false 把所有护栏拆了（**这俩都别干**）。

现在可以直接写规则了，两个层级：

```toml
# 用户级：~/.kimi-code/config.toml —— 跨所有项目生效
[permissions]
allow = [
  "Bash(npm run test:*)",
  "Bash(git log:*)",
  "Read(**)",
]
deny = [
  "Bash(rm -rf:*)",
  "Bash(curl * | sh)",      # 顺手把「管道执行远程脚本」这类经典送命写法直接禁了
]
```

```toml
# 项目级：<项目根>/.kimi-code/local.toml —— 只在这个工作区生效
[permissions]
allow = [
  "Bash(go test ./...)",
  "Bash(docker compose up -d)",
]
```

匹配优先级是 **`deny` > `ask` > `allow`**——同一条命令被 deny 和 allow 同时命中，deny 赢。会话启动时自动加载。

**三个实用心法：**

1. **deny 放用户级，allow 放项目级**。`rm -rf` / `git push --force` / `curl|sh` 这种在哪都危险的，用户级 deny 一次管终身；项目专属的测试、构建命令，写进项目的 `local.toml` 跟着仓库走（**注意：这文件别 commit**，和昨天 `.claude/settings.local.json` 一个待遇——路径规则、项目习惯不该公开）。
2. **从宽到紧，反向收敛**。先开一周 verbose 观察弹窗频率，把高频确认的命令捞进 allow；低频但吓人的进 deny。上来就写满 allow 列表，等于闭眼复制粘贴过去的点击记录——里面可能混着带密码的脏命令（昨天的体检流程直接复用：翻历史、先轮换、再入库）。
3. **规则支持通配**：`Bash(npm run test:*)` 能匹配 `npm run test -- --verbose` 这种带参数变体，比逐条死记省十倍事。

## 技巧二：全屏模式 + 折叠 —— 长任务终于不用在刷屏里找重点

跑一个「读 40 个文件、改 12 处、跑 3 轮测试」的 agent 任务，终端滚动速度堪比开奖。2.6.0 的答案：

```bash
/settings → TUI mode → Fullscreen   # 重启后生效
```

全屏模式下，**连续的工具调用和思考过程自动折叠成摘要行**——你看到的是「🔧 Read ×12 · Edit ×5 · Bash(go test) ✓」这样的进度卡片，而不是几千行原始输出。折叠密度在 `/settings` 里可调；想恢复旧版全展开布局，改 `~/.kimi-code/tui.toml`：

```toml
[transcript]
mode = "ungrouped"
```

配套两个小升级也顺手开：全屏下 **`Ctrl-C`/`Cmd-C` 直接复制选中文本**（终端党狂喜，不用鼠标去拽了），`/settings` 里还有「选中即复制」开关；用户消息滚出视口后**顶部保留一行摘要**，点一下跳回原消息——长对话回看体验质变。

> Desktop 党对应物：1.3.4 把「粘贴长文本自动折叠」和「截图取色器」加上了，外加内置浏览器标签页默认静音——agent 半夜自己开 20 个网页查资料，终于不用听此起彼伏的提示音了 🫠

## ⚠️ 顺手提醒：`.env` 现在能被 `@` 到了

2.6.0 和 Desktop 1.3.4 同时改了同一个行为：**`@` 补全默认包含隐藏路径和被 `.gitignore` 忽略的文件**。匹配质量相同时优先展示未忽略文件——但只要你打得出文件名，它就能被提及。

好处显而易见：临时想让 agent 看一眼 `.env.example` 的结构、确认 ignore 规则，不用先改 gitignore。但要清醒：**你的 `.env`、`*.pem`、`.claude/settings.local.json` 也在这个射程内**。昨天的密钥体检做过的人，今天顺手加一步——想想哪些忽略文件**永远不该进对话上下文**，在项目 `local.toml` 的 deny 列表里把它们点名：

```toml
deny = [
  "Read(**/.env)",
  "Read(**/*.pem)",
  "Read(**/.claude/settings.local.json)",
]
```

deny > allow 的优先级就是为此刻准备的。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| 每天反复点「允许」 | 写 `config.toml`（用户级 deny）+ 项目 `.kimi-code/local.toml`（项目级 allow），通配符覆盖参数变体 |
| 开全屏模式 | `/settings → TUI mode → Fullscreen`，重启生效；折叠密度可调，`tui.toml` 设 `"ungrouped"` 恢复旧布局 |
| 全屏里想复制输出 | `Ctrl-C`/`Cmd-C` 直接复制选中内容；「选中即复制」在 settings 里 |
| `.gitignore` 里的敏感文件 | `.env` / `*.pem` / 各家 `settings.local.json` 加进项目 deny，别只靠「它搜不到」的侥幸 |
| 项目 `local.toml` | **别 commit**——和昨天讲的 `.claude/settings.local.json` 同一待遇 |

---

*Generated on 2026-11-13 · 来源：Kimi Code CLI 2.6.0 变更记录（2026-11-12，moonshotai.github.io/kimi-code）、Kimi Code Desktop 1.3.4 变更记录（2026-11-12，kimi.com/code/docs）、昨日 Apiiro/GitGuardian 密钥泄露体检清单（见 2026-11-12 篇）*
