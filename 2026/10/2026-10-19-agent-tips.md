# 10-19 — Agent 的「换引擎体检」：别名集体跳槽新型号，你的配置跟上了吗

## 🤔 上周引擎舱被悄悄换了一轮

先把背景摆清楚——过去两周 Claude Code 的模型层变动密度爆表：

- **10/07–10/09**：四连发（v2.1.293 ~ v2.1.296），**Claude Haiku 5.5** 正式上线——比 Haiku 4.5 便宜约 **75%**、跑分还更高，官方定位就是子代理、批量任务、浏览器操作这类「高周转杂活」；
- **别名大挪移**：`fable`→Fable 5.1（v2.1.257）、`opus`→Opus 5.5（v2.1.280）、`sonnet`→Sonnet 5.5（v2.1.284）、`haiku`→Haiku 5.5（v2.1.293）——四个别名**全部**指向新一代；
- **旧船票作废**：老一辈的 `ANTHROPIC_SMALL_FAST_MODEL` 正式**废弃**，换成四个全家桶变量 `ANTHROPIC_DEFAULT_{FABLE,OPUS,SONNET,HAIKU}_MODEL`。

也就是说：你升级 CLI 之后**什么都没改，引擎就已经换新的了**。但另一类人刚好相反——几个月前手动固定过环境变量的人，正被自己的配置**锁在旧型号上**，还在纳闷「大家都说快，我怎么没感觉」。😭

顺带一句行业背景：OpenAI 的 **GPT-5.5 已于 10/14 从 ChatGPT / Codex 所有订阅方案中下线**（API 仍可用），Codex 用户被迫迁移——模型「换引擎」已是常态，不是事故。你的工具链最好具备一键跟新的能力。

## 🛠️ 技巧一：两分钟「别名体检」——确认你跑的是新引擎

先查版本再查模型：

```bash
claude --version        # 低于 2.1.293 的话，haiku 别名还是旧模型
```

进会话后敲 `/model` 打开选择器，**看每个别名当前解析到谁**。官方版本历史对照（10/15 文档核对）：

| 版本 | 变化 |
|---|---|
| v2.1.293 | `haiku` → Haiku 5.5 |
| v2.1.284 | `sonnet` → Sonnet 5.5 |
| v2.1.280 | `opus` → Opus 5.5 |
| v2.1.257 | `fable` → Fable 5.1 |

然后重点翻一遍 shell 配置（`~/.zshrc` / `~/.bashrc`）和 `~/.claude/settings.json` 的 `env` 块，清这三类**历史遗留**：

```bash
# ❌ 老的，已废弃，直接删
export ANTHROPIC_SMALL_FAST_MODEL=xxx

# ⚠️ 9 月前加的锁定，现在反而把你按在旧型号上
export ANTHROPIC_DEFAULT_HAIKU_MODEL=claude-haiku-4-5
export ANTHROPIC_DEFAULT_FABLE_MODEL=claude-fable-5
```

`fable` 那条尤其隐蔽：官方原话是「之前加过 `ANTHROPIC_DEFAULT_FABLE_MODEL=claude-fable-5` 的，**删掉这行**别名才会改用 Fable 5.1」。想固定就固定到新型号（`claude-haiku-5-5`、`claude-fable-5-1`），不想固定就整行删掉、交给别名自动跟新。还有个小坑：在 **Claude apps gateway 会话**里 `fable`/`best` 仍解析到 Fable 5，想用 5.1 得敲全名 `/model claude-fable-5-1`。

## 🛠️ 技巧二：子代理编队重新点兵——Haiku 5.5 是本周最大甜点

9/23 我们讲过「模型编队」（杂活别麻烦教授）。今天要**升级这条**，因为杂活头子换代了：

```bash
export CLAUDE_CODE_SUBAGENT_MODEL=haiku    # 子代理/后台任务默认走 haiku
export CLAUDE_CODE_SUBAGENT_MODEL_FORCE=1  # v2.1.257+：强制全体子代理服从，frontmatter 也不好使
```

Haiku 5.5 的甜点属性：

- **便宜约 75%**：批量摘要、分类、机械改代码，随便造不心疼；
- **原生 1M 上下文**（Anthropic API 上，连 `[1m]` 后缀都不用加）：整个仓库塞给子代理也不虚；
- ⚠️ 但**提示词超 100K 后单价跳档**，超长上下文前先算笔账；会话默认约 967K 时自动压缩，想提前可以调小该模型的压缩窗口。

选型速查·更新版：

| 活儿 | 用谁 |
|---|---|
| 日常编码 | `sonnet`（= Sonnet 5.5，官方认证「拿不准就用它」） |
| 架构设计 / 大型重构 | `opus`（= Opus 5.5）或 `fable`（= Fable 5.1） |
| 子代理 / 批量杂活 | `haiku`（= Haiku 5.5，本周性价比之王） |
| 想省额度又要脑子 | `opusplan`（Opus 5.5 出图纸、Sonnet 5.5 施工） |

**彩蛋一个坑**：Fable 5.1 能读 Opus 5.5 的思考块，**反过来不行**——不兼容的块会被 API **静默忽略，不报错**。所以长会话中途从 Fable 降级到 Opus、再凭感觉判断「谁更聪明」，结论是不作数的。要公平对比就：新开会话 + 同一起点文件 + 完整模型 ID 跑对照。💀

## 🎯 今天记住这个

| 一句话 | 怎么做 |
|---|---|
| 别名全换新型号了 | `claude --version` ≥ 2.1.293；`/model` 里确认四个别名都指向新一代 |
| 检查历史遗留锁定 | 删 `ANTHROPIC_SMALL_FAST_MODEL`；旧的 `ANTHROPIC_DEFAULT_*` 要么删要么改成新型号全名 |
| 子代理交给新杂活头子 | `CLAUDE_CODE_SUBAGENT_MODEL=haiku` + `CLAUDE_CODE_SUBAGENT_MODEL_FORCE=1`，便宜 75%、1M 窗口；超 100K 记得算账 |
| 模型对比要公平 | 新会话 + 同起点 + 完整模型 ID；Fable 会话中途降 Opus，思考链会静默丢 |
