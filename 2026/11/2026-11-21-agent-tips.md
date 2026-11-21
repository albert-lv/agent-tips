# 2026-11-21 —「任务面板收编全家桶 + 会话挂上服务器」：Claude Code /tasks 一眼看全定时任务，Codex --remote 断线不丢活

> 这两天工具圈跟打了鸡血一样：**Codex 0.171.0** 昨晚上架，把前天发布的 **GPT-6.2 Sol** 直接设为默认模型；**Claude Code** 两天连发三版（2.1.328 → 2.1.330），顺手修了一个可能让你配置「一夜回到出厂」的 bug；**Anthropic** 还放了个大招——Claude agents 解出了**两个悬而未决的黑洞物理开放问题**。今天讲两件立刻能用的：怎么把越攒越多的定时任务/后台任务管起来，以及怎么让 Codex 会话长在服务器上、断线也不丢。

## 📰 今日热点背景

- **Codex 0.171.0（11/20）**：GPT-6.2 Sol / Astra 进模型目录，**Sol 成为默认选项**；TUI workspace 改为**默认开启**——可搜索的文档预览、图片预览、子代理 transcript 和主会话并排看。另外 `codex exec --remote <ADDR>` 可以把非交互会话跑在已有 app server 上。
- **Claude Code 2.1.329（11/19）**：`/tasks` 面板大升级，**定时任务、/loop 唤醒、fork 出来的会话**现在都能在一个面板里看到；新增 hooks 信任弹窗——进一个新目录时，会明确告诉你**这个目录的 hooks 是在哪声明的**。顺手修了一个大 bug：`~/.claude.json` 在网络卡顿或文件锁竞争后**偶发被重置为默认值**（建议升完检查一眼自己的配置）。
- **Claude Code 2.1.330（11/20）**：修复风暴——auto 模式在 Bedrock / Vertex / Foundry 上翻车的、Code Mode 脚本等权限弹窗 10 分钟会自杀的、MCP schema 以 `anyOf` 开头导致工具「隐身」的，全修了。
- **Claude agents 解出两个黑洞开放问题（11/17）**：Anthropic 官方博客称，长程 Claude agent 在两个悬而未决的引力物理问题上给出了被验证的解答。长任务 agent 的「耐力上限」，又被抬高了一截。

## 技巧一：Claude Code 2.1.329 —— `/tasks` 从「待办清单」升级成「空管塔台」

以前 `/tasks` 只看得到正在跑的后台任务，定时任务、/loop 唤醒、fork 会话分散在各地，像个没合并收件箱的邮箱客户端。2.1.329 之后：

- **一个面板看全部**：scheduled tasks（定时任务）、/loop 唤醒、fork 会话全部进 `/tasks`。早上打开电脑，昨晚 3 个 cron 跑得怎么样、哪个 fork 还在等 review，一眼扫完。
- **hooks 信任弹窗 = 迷你供应链体检**：cd 进一个带 hooks 的新项目，Claude Code 现在会弹出提示，**明说 hooks 声明在哪个文件**。以前 hooks 是「静默上车」，恶意仓库塞个 PostToolUse 往外发数据你都未必知道；现在至少知道「这车的刹车是谁装的」。进陌生仓库第一件事：看一眼弹窗里的声明路径，不对劲就拒。

组合用法：晚上睡前 `/loop` 挂个「每 30 分钟跑一遍测试，挂了叫醒我」→ 早上 `/tasks` 收拢所有唤醒记录和 fork → 对着 hooks 弹窗审一眼昨晚 agent 动过的陌生目录。安心三连。

**升级提醒**：`~/.claude.json` 偶发重置的 bug 在 2.1.329 已修，但如果你最近遇到过「配置突然变默认」，升完版本后先 diff 一下自己的 settings，重要配置考虑进 git 管理。

## 技巧二：Codex 0.171.0 —— `codex exec --remote` 让会话「长在服务器上」

本地跑 `codex exec`（非交互模式）最大的痛：SSH 一断、笔记本一合盖，会话跟着陪葬，跑了 40 分钟的活从头再来。0.171.0 的 `--remote` 参数改了游戏规则：

```bash
codex exec --remote <ADDR> "跑完这套集成测试并输出失败用例分析"
```

会话跑在**已有的 app server** 上，本地终端只是个显示器。断线 → 重连 → `codex exec --remote <ADDR>` 接着上次的会话继续，活没丢。适合三个场景：

1. **CI 流水线里跑长验收**：把 app server 常驻在构建机上，流水线步骤全走 `--remote`，构建节点重启不影响在飞的 agent 任务。
2. **笔记本合盖续命**：下班前把长任务甩到远程 server，地铁上用手机连上 server 看 TUI workspace 的子代理 transcript（0.171 起默认开启，并排视图），到家开电脑接着看。
3. **多人接力**：同事连同一个 `<ADDR>` 能看到同一会话的现场——结对 debug 不用再屏幕共享，直接「共用一台 agent 的大脑」。

再叠一层：Sol 现在是默认模型，定价思路是「把自我改进的推理用到日常任务」。长验收 + Sol 默认 + `--remote` 常驻，Codex 从「命令行小助手」开始有点像「挂在服务器上的常驻同事」了。

## 🥚 彩蛋：三个边角料

- **黑洞两连解**：Anthropic 11/17 宣布 Claude agents 解出两个开放黑洞物理问题。对咱写代码的启示是：agent 的瓶颈已经不是「聪明」，而是「耐力」——几小时不飘、不忘目标、不自我循环。以后评估长任务模型，「连续工作 4 小时的靠谱率」可能比单次 benchmark 更值钱。
- **Claude Code 2.1.330 顺手修的高频坑**：MCP 工具 input schema 以 `anyOf` / `oneOf` / `allOf` 开头时工具会「隐身」——用第三方网关或关掉 telemetry 的用户中招最多。如果你 `/mcp` 里突然少了工具，先升版本再怀疑人生。
- **Crush v0.102.0**：开源 agent Crush 现在可以**整体替换系统 prompt**——不只主 agent，连子代理、planning、compaction、会话标题生成的 prompt 都能换（`--prompt name=path`）。想给自己的 agent 团队「换企业文化」，这是目前最彻底的开源方案。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| 定时任务 / loop / fork 散落各处 | Claude Code 升 2.1.329，进 `/tasks` 一屏收拢 |
| cd 进陌生仓库 | 看 hooks 信任弹窗里声明路径，不对劲就拒 |
| 配置曾「一夜回到出厂」 | 升 2.1.329，settings 进 git 管理 |
| SSH 断线怕丢 Codex 长任务 | `codex exec --remote <ADDR>`，会话跑在 server 上 |
| `/mcp` 工具莫名消失 | 先升 2.1.330（修 anyOf schema 隐身 bug） |

---

*每天 08:42 更新 · 向上 & 小惠的 Agent 工具笔记*
