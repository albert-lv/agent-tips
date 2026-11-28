# 2026-11-28 Agent Tips：供应链惊魂 + 模型自由切换

> 今日热点：10 月底 Cursor 扩展市场被曝出持续 11 小时的供应链攻击（篡改的 Claude Code 扩展试图植入 SSH 后门，影响约 5000 名开发者）；与此同时 Claude Fable 5.2 已全量进 Claude Code，Kimi K3.1 Pro 也在 10 月底全量上线 Kimi Code。安全警钟和模型自由，是今天的主角。

## 技巧一：Cursor 后门事件后，给你的 agent 工具链做一次「安检」

10 月 29 日，Cursor 的扩展市场在大约 11 个小时里分发了一个被篡改的 Claude Code for VS Code 扩展（版本 2.1.312 / 2.1.313）。攻击者在扩展包里预置了 JS 加载器，一加载就往你的 `authorized_keys` 里塞 SSH 公钥，还试图开反向隧道。讽刺的是，最后救场的是攻击者自己代码写崩了——大部分机器上远程访问没跑通。

这件事最扎心的教训不是「Cursor 不行」，而是：**你的 agent 工具链里，任何一个中间环节被污染，你的整个开发机就裸奔了**。几个今天就能做的自检：

1. **只从官方渠道装 agent**：Claude Code 用 `curl -fsSL https://claude.ai/install.sh | bash` 或官方 npm 包，别从第三方扩展市场捡现成的。
2. **锁版本 + 手动升级**：关掉自动更新，升级前瞄一眼 changelog 和官方安全公告。自动更新省事，但也等于把「何时在我机器上跑新代码」的决定权交了出去。
3. **定期检查 `~/.ssh/authorized_keys`**：出现不认识的 key 立刻警觉。这条文件值得加进你的心智 checklist，跟检查 `crontab -l` 一个待遇。

供应链安全不是大厂专利，个人开发者就这几台机器，一旦被开后门，GitHub token、代码、密钥一锅端。花 5 分钟体检，胜过事后哭三天。

## 技巧二：Claude Code 不换工具换模型，Kimi / DeepSeek / GLM 一键接入

Claude Fable 5.2 上线、Kimi K3.1 Pro 全量（三万亿参数、100 万上下文、还有快 2-3 倍的 Highspeed 版）——模型圈最近卷疯了。但工具不用换来换去：Claude Code 只是个壳，背后是哪家模型，几个环境变量说了算。

把配置写进 `~/.claude/settings.json`，一次到位，不用每次 export：

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://你的兼容接口地址",
    "ANTHROPIC_AUTH_TOKEN": "你的 Key",
    "ANTHROPIC_MODEL": "模型名"
  }
}
```

改完重启，`/status` 里确认 Base URL 已经生效。两个经典大坑，提前帮你踩过：

- **环境变量优先级陷阱**：如果你 shell 里还残留 `ANTHROPIC_API_KEY`，它会压过配置文件里的 token，请求直接打到 Anthropic 按美元扣费。`echo $ANTHROPIC_API_KEY` 查一下，有就 `unset` 掉。
- **Key 是明文**：settings.json 别提交进 Git，进 `.gitignore` 是它唯一的归宿。

这套玩法的适用场景：没有 Anthropic 账号、订阅额度不够用、或者想白嫖 Claude Code 的工作流 + 国产模型的性价比。写脚本改 bug 差别不大，大项目重构还是能感觉出模型差距的——但用来跑日常任务，真香。

---

*今日一句话：工具可以混搭，来源必须官方；模型可以平替，密钥必须看紧。* 🛡️
