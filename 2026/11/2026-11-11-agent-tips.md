# 2026-11-11 — 断供前夜：Cursor 用户今晚的 15 分钟清单 + Claude Code「无人值守三件套」

> 明天 11/12，OpenAI 断供 Cursor 正式生效——AI 工具史上第一起「模型供应链断供」进入执行日。另一边，Claude Code 本周三连发（2.1.317 → 2.1.319，11/3~11/6），主题出奇地一致：**让无人值守的 agent 跑得更稳**。今天一篇给你今晚该做的检查清单，一篇拆这三个专治「人不在，活儿卡死」的新开关。

## 📰 今日热点背景

- **OpenAI × Cursor 断供，明天执行（11/12）**：8/28 OpenAI 通知 SpaceX（600 亿美元全股票收购 Anysphere 后），基于「控制权变更」条款取消定制协议，明天起 Cursor 直接取用 OpenAI 模型的权限正式关闭。CEO 说 OpenAI 模型只占流量 ~5%——但落到具体团队，依赖的那条工作流就是 100%。**断供日撞上双十一，这日子选得也是挺会整活的。**
- **Claude Code 本周三连发（11/3~11/6，v2.1.317 → 2.1.319）**：没有炫技大功能，全是自动化硬骨头——权限弹窗不再干等、登录过期提前预警、MCP 长任务自动转后台。跑 cron / 后台长任务 / CI 的人，这三天更新全是给你的。
- **Anthropic 企业侧两个 beta（11/5~11/6）**：Console 上线 **IP allowlist**（API key 泄露后也能锁死使用范围，单组织最多 1000 条 IPv4/IPv6/CIDR），Enterprise 可用 **project tags 按成本码追踪花费**（按 charge code / client-matter 拆账单）。团队管钱的同学值得关注。

## 技巧一：断供前夜 15 分钟清单 —— 明早醒来别发现流水线红了

策略 11/08 那篇讲过了（bundled 死、BYOK 半活、gateway 全活），今天不讲道理，只给动作。**今晚花 15 分钟过一遍：**

1. **全局搜 pin 死的 OpenAI 模型**。翻 `settings.json` 的 model 覆盖、`.cursor/rules`、自定义 modes、团队共享配置，把 `gpt-*` / `codex*` 的硬编码点名全换成具体替代（Sonnet 系 / Composer / Gemini 系）。**Tab 补全不受影响，重灾区是 Agent 模式、CLI、SDK 里写死模型的调用**——这几个明早会直接报错，没有兜底。
2. **今晚用替代模型完整跑一遍核心工作流**。不是跑个 hello world，是你团队最依赖的那条（比如大范围重构、review、测试修复），确认质量落差在你的接受线内。早发现问题明早是「切换」，晚发现是「事故」。
3. **看一眼 Other Models 额度池余额**。断供后原本走 bundled 的第三方模型调用会转移到这个按 API 原价扣 credit 的池子（Pro 档约 $20 第三方额度）。用量大的团队，这个池子可能比你想的浅。
4. **检查所有自动化入口**。有 cron、CI、脚本在调 Cursor agent + OpenAI 模型的，今晚改路由；人忘了，机器人不会忘，明早 9 点准时给你表演红灯。
5. **顺手把关键 prompt 存档**。断供之后各家模型路由还会变（订阅制工具买的本质是「界面+合约」，合约会变），你的 prompt 资产攥在自己手里才不慌。

> 一句话：**今晚是「主动切换」的最后窗口，明早就是「被动排障」了。**

## 技巧二：Claude Code「无人值守三件套」—— 人不在，活儿别再卡死

跑自动化的人最怕的不是 agent 干错，是**agent 干到一半停在那等人**——权限弹窗没人点，一挂一夜。本周三连更给了三个开关，专治这个：

### ① `permissions.denyInsteadOfPrompting`：弹窗变拒绝，回合继续走

v2.1.319 新增设置：

```json
{
  "permissions": {
    "denyInsteadOfPrompting": true
  }
}
```

效果：某个工具调用本该弹出权限确认、但**没人能回答时，直接拒绝这次调用并让回合继续**，而不是永远挂起等一个不会来的回车。终端会话、子代理、teammate 全生效。

这正是 `claude -p` 管线、cron 定时任务、云端会话的救命开关——以前 headless 跑到一半撞上一个未授权命令就僵死，现在它会换个方式绕过去（或者明确失败），至少**把「静默挂起」变成「明确结果」**。配合 allowlist 用：常用命令放 `allow`，剩下的宁可拒掉也别卡住流水线。

### ② `claude auth status` 显示登录过期日 —— 凌晨 3 点 cron 哑火的真凶现形

v2.1.318 起，`claude auth status` 新增 `loginExpiresAt`：你存的 claude.ai 登录**到底哪天到期**，一行看清。更贴心的是，`claude -p` 从到期前 10 天开始，每次文本输出都会在 stderr 打一行警告。

每天都跑定时任务的人（对，说的就是正在读这篇的你）应该遇到过：流水线某天突然 401，查半天发现是 stored sign-in 悄悄过期。**把 `claude auth status` 加进每周巡检，比事故后排查便宜一百倍。**（v2.1.317 还修了一个恶性 bug：某个请求 401 后会每次重试都刷新登录、把**其他会话**也拖进 401 循环——升级即修复。）

### ③ MCP tasks 自动转后台 —— 长工具调用不再霸屏阻塞

v2.1.319：当 MCP server 把一次工具调用标记为 task，Claude Code 会**立刻把它挪到后台**，跑完再把结果喂回来，主回合不用傻等。对「通过 MCP 触发长构建 / 长分析」的场景，上下文里少一大段等待垃圾，主 agent 还能干别的。想回到旧行为设 `CLAUDE_CODE_DISABLE_MCP_TASK_BACKGROUND=1`。

> 三件套一句话总结：**拒绝代替干等、过期提前报、长活丢后台**——全是把「不确定性」翻译成「确定性」的工程，这正是自动化能睡得着觉的前提。

## 🥚 彩蛋：两个小但香的安全更新

- **邮箱外发前询问（v2.1.317）**：现在 web / shell 工具调用要**把你的账号邮箱发出去之前**，会先问你一声。agent 时代，你的邮箱就是半条身份，这个闸门上得好。
- **`/tasks` 新增 "Earlier in this session"（v2.1.319）**：已完成的子代理不再跑完就蒸发，列表里留一行，**甚至可以重启后重新打开接着聊**。多子代理并行跑长任务的人，终于不用靠截图记结论了。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| Cursor 用户 | 今晚 15 分钟：搜 pin 死的 gpt-*、替代模型跑一遍核心流、查 Other Models 余额、改自动化路由、存档 prompt |
| 跑 cron / CI / `claude -p` | settings.json 开 `permissions.denyInsteadOfPrompting`，弹窗变拒绝，回合不断 |
| 定时任务莫名 401 | `claude auth status` 看 `loginExpiresAt`，到期前 10 天 stderr 会自动预警；顺带升到 ≥2.1.317 修 401 传染 bug |
| MCP 里有长工具调用 | v2.1.319 起自动转后台，不需要就 `CLAUDE_CODE_DISABLE_MCP_TASK_BACKGROUND=1` |
| 多子代理并行 | `/tasks` 的 Earlier in this session 能找回跑完的儿子，重启也能接着聊 |
| 团队管 API key | Console 的 IP allowlist beta 安排上，key 泄露也锁死使用范围 |

---

*Generated on 2026-11-11 · 来源：AI 要聞彙整（2026-08-30 OpenAI 断供通知与条款背景，adersaytech）、Claude Code Changelog v2.1.317/318/319（2026-11-03~06，gradually.ai / aiparabellum 实测）、Anthropic Platform Release Notes（2026-11-05 IP allowlist、2026-11-06 project tags cost tracking beta，thismonthinai）、AI coding tool pricing 2026（2026-11-06 复核版，qcode.cc）、zhouweiwei AI 资讯速览（2026-11-01）*
