# 🎯 备胎要常换：十月榜单背后的「多模型池」实操

> 2026-10-25 · 每天一个 Agent 小技巧

## 📰 今天的热点背景

两件事拼在一起看，味道就出来了：

1. **10/23 benchlm 月榜数据**：Claude Fable 5.1 在 **Cursor 自家办的 CursorBench 4.0** 上拿下 51.8 分，领先 Cursor 亲儿子 Composer 2.5（27.7 分）**24.1 分**——Anthropic 的模型把人家主场榜单前四名全包了。
2. **10/16 LogRocket 十月工具榜**：开源的 **OpenCode 冲到第二**，仅次于 Claude Code。上榜理由不是功能多花哨，而是四个字：**75+ 模型随便接**。

翻译一下：模型质量第一名在 Claude 家，但「不被任何一家拿捏」成了本月社区最大公约数。Cursor 10/12 刚改完价、Copilot 同期切 credits 计费，大家突然发现——**把鸡蛋放一个供应商篮子里，风险是真的会兑现的。**

所以今天就讲「多模型池」怎么落地，不是理念，是配置。

## 🛠️ 技巧一：OpenCode —— 开源界的「万能转换头」

OpenCode（`anomalyco/opencode`，MIT 协议）最近能蹿上工具榜第二，靠的是这个思路：**Agent 框架归框架，模型归模型，别绑死。**

核心玩法就三行：

```bash
# 看你当前接了哪些 provider
opencode models

# 启动时直接点名
opencode --model anthropic/claude-fable-5.1

# 项目里固化默认 + 备胎（opencode.json）
{
  "model": "anthropic/claude-fable-5.1",
  "small_model": "moonshot/kimi-k3"
}
```

要点：

- **BYOK（自带 key）**：用哪家充哪家，没有中间商赚差价。对比 Cursor 里第三方模型按 API 原价从预付 credit 扣，OpenCode 是纯粹的「你自己跟模型厂结算」。
- **本地跑、代码不出机**：隐私敏感项目（比如你们那种不能外发的 infra 仓库）也能用。
- **配置按项目走**：每个 repo 一份 `opencode.json`，重型重构走 Fable，日常杂活切便宜模型，项目之间互不影响。

## 🛠️ 技巧二：Claude Code 自带的「降级链」，别浪费

如果你主力还是 Claude Code，它 6 月起就内置了模型路由，只是很多人没开：

```json
// ~/.claude/settings.json
{
  "model": "claude-fable-5.1",
  "fallbackModel": ["claude-opus-5.5", "claude-sonnet-5.5"],
  "env": {
    "ANTHROPIC_SMALL_FAST_MODEL": "claude-haiku-5.5"
  }
}
```

- `fallbackModel`：主模型过载/超预算时**自动降级**，最多挂 3 个。长任务跑到一半被限流，不用再手动重启会话。
- `ANTHROPIC_SMALL_FAST_MODEL`：文件搜索、日志摘要这类杂活自动派给 Haiku 5.5——比主力模型便宜一大截，还快。
- 组合起来就是：**Fable 出图纸，Haiku 打杂，Opus 兜底**——一个会话里三层路由，零手动切换。

（⚠️ 注意把旧的 `ANTHROPIC_DEFAULT_*` 环境变量清掉，别名现在统一指向新一代，残留旧锁定会导致降级链不听话——这点 10-19 那期专门讲过。）

## 🧠 今天记住这个

| 场景 | 动作 |
|---|---|
| 不想被单一供应商计费改革拿捏 | 装个 OpenCode 当万能转换头，BYOK 池子常备 2~3 家 key |
| Claude Code 长任务怕中途限流 | `fallbackModel` 挂好降级链，最多 3 个 |
| 杂活烧主力模型 token 心疼 | `ANTHROPIC_SMALL_FAST_MODEL` 指向 Haiku 5.5 |
| Cursor 里用 Claude 越来越贵 | 记住第三方模型是 API 原价扣 credit，量大时对比 OpenCode BYOK |

> 💡 **一句话总结**：十月风向已经很清楚了——模型是粮食，Agent 是锅。粮食要多囤几家的，锅才炒得安心。

---

*参考：benchlm「Claude Code vs Cursor · 2026-10」（10/23 数据核验）、vibedigest「Best AI Coding Tools · 2026-10-16」（LogRocket 十月榜）、learncursor.dev 计费说明（10/17 刷新）。*
