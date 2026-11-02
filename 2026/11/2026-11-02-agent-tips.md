# 🛡️ 扩展市场惊现「李鬼」claude-code：给 AI 工具链做个安检

> 2026-11-02 · 每天一个 Agent 小技巧

## 📰 今日热点背景

两件事，一凶一吉：

**凶**：上周五（10/29）Cursor 官方发安全公告——有人把**恶意版本的 `Anthropic.claude-code` 扩展**（伪装版本号 2.1.312 和 2.1.313）投进了 Cursor 的扩展市场。官方真身当时才发到 2.1.31x 号段，骗子贴着官方版本号造假，不仔细看根本分辨不出。这是 agent 工具圈第一起公开披露的「供应链投毒」案例。

**吉**：Kimi Code CLI **v2.4.0**（10/28）上线一批实验功能——昨天刚夸完 Codex 能「生成途中插嘴」，今天 Kimi 也补上了同款能力，还附赠一个全屏界面。

## 🛠️ 技巧一：AI 工具链「安检三件套」——agent 权限越大，装它的门槛要越高

以前装个编辑器插件，最坏不过是弹广告。现在装的是 **agent**——它能读你全部代码、跑 shell 命令、碰你的环境变量和密钥。一个被投毒的 agent 扩展，等于给攻击者开了你机器的后门。Cursor 这次事件就是警钟。

**1. 只认官方安装入口**

Claude Code 的官方渠道只有两个：官网安装脚本，或 `npm i -g @anthropic-ai/claude-code`（认准 npm 官方 scope）。**扩展市场里搜到的同名「claude-code」，一律视为可疑**——Anthropic 并没有把官方 CLI 以那种形式分发到第三方市场。同理适用于所有 agent 工具：装之前先去官方文档确认「官方到底通过什么渠道分发」。

**2. 版本号对账**

骗子最爱贴着官方最新版号造假。装任何 agent 工具前，花 10 秒对个账：

```bash
npm view @anthropic-ai/claude-code version   # 官方最新版
claude --version                              # 你本机的版本
```

两个号对不上、且你本机的来源说不清——卸载，重装，别抱侥幸。

**3. 每月一次「扩展盘查」**

```bash
ls ~/.cursor/extensions ~/.vscode/extensions   # 翻扩展目录
npm ls -g --depth=0                            # 扫全局 npm 包
```

看有没有不认识、名字擦边（比如 `anthropic-claude`、`claude-code-official` 这类拼写变体）的包。agent 类工具更新快，顺手把 `claude doctor`（Kimi 侧是 `kimi doctor`）这类官方自检命令也跑一遍——它们会暴露配置漂移和可疑钩子。

> 💡 一句话原则：**把 agent 工具当「能接触你全部代码的实习生」来录用**——背调（来源）、对身份（版本号）、定期述职（盘查），一个环节都别省。

## 🛠️ 技巧二：Kimi Code v2.4.0 实验室开张——等 agent 的时候，现在可以插嘴了

昨天讲 Codex 的 live steering 时还在羡慕，24 小时内 Kimi 就交卷了。v2.4.0（10/28）的几个实验功能，都在 `/experiments` 或设置里手动开：

**1. 后台等待可被消息打断（最实用）**

agent 在跑长命令、等构建的时候（内部 `WaitFor`，最长 90 秒），**你现在可以直接按 `Enter` 或 `Ctrl-S` 把话塞进当前回合**——不用干等它自然醒来，后台任务也不会被打断。昨天的 Codex 是「生成途中插嘴」，Kimi 这个是「等待途中插嘴」——两大 CLI 在「人随时接管」这个方向上正式会师。

**2. NotifyUser：agent 学会「汇报进度」**

默认关闭，在 TUI `/experiments` 或桌面端设置的实验室里开启。开启后 agent 会在回合内主动发布简短进展通知——配合之前讲过的桌面端「进展更新」，长任务黑箱问题算是被两头堵上了。

**3. 全屏模式（实验性）**

`~/.kimi-code/tui.toml` 里设 `tui_mode = "fullscreen"`，重启生效：transcript 独立滚动、支持鼠标选择文本、折叠块点击展开/收起，底部还有可点击的 Jump to bottom。长会话党狂喜。

> ⚠️ 实验功能的正确姿势：**新会话里开**（这些开关只影响之后新建的会话），好用再常驻，不好用 `/experiments` 一关就回原样，零成本试错。

## 🥚 彩蛋：两条一句话速报

- **K3.1 Pro 权重开源倒计时**：官方承诺 11/4（就是明天！）前公开完整权重。想自托管或等第三方平台接入的，明天记得蹲——OpenRouter / OpenCode 这类 BYOK 通道大概率 48 小时内跟进。
- **顺手自查一下 v2.4.0 的小甜点**：取消了每轮 1000 步的硬上限（长任务不再被腰斩）；`KIMI_MODEL_*` 环境变量可以不改配置临时切模型，重启自动失效——CI 里测新模型特别好使；粘贴 GitHub 链接即可安装社区 Plugin，还支持锁定版本。

## 🎯 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| 装 Claude Code | 只走官网脚本或 `@anthropic-ai/claude-code`，扩展市场的同名货是假的 |
| 版本号存疑 | `npm view @anthropic-ai/claude-code version` 对账，对不上就重装 |
| 每月体检 | 翻扩展目录 + `npm ls -g`，顺手跑 `claude doctor` / `kimi doctor` |
| Kimi agent 等待中想插话 | 直接 `Enter` / `Ctrl-S`，消息注入当前回合 |
| 想体验新交互 | TUI 设置 `tui_mode = "fullscreen"` + `/experiments` 开 NotifyUser |
