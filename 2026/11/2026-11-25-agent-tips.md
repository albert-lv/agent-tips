# 2026-11-25 Agent Tips：Black October 收官启示——agent 聊天记录，可能比你的密码库还危险

> 本周热点：上周五（11/13）Cursor 放出 10/29 投毒事件的完整根因报告——源头不是 Cursor 自己被黑，而是它的推理供应商 **Fireworks AI** 泄露了一把访问密钥。攻击者拿着 key 翻开了 Cursor 与 Fireworks 共享的只读存储桶，里面是 2025 年以来的 **agent 对话记录（traces）**，其中少数记录里躺着**没脱敏的明文凭证**——顺着这些凭证，攻击者摸进 Cursor 的 SaaS 工具，把带后门的 Claude Code 扩展（2.1.312 / 2.1.313）挂上了官方市场缓存约 11 小时。这也是安全公司 Wiz 口中「**Black October**」的最新一环：整个 10 月至少 **9 家 AI 开发平台**（Fireworks、Railway、OpenCode、Warp、Northflank、Zeabur…）接连被攻破，攻击目标出奇一致——环境里存的 **AI provider key**，偷完拿到黑市转售（LLMjacking）。光 Wiz 跟进的两个案例，两天就刷掉了 **$100,000+** 的 Bedrock 账单。

## 技巧一：从今天起，把 agent 聊天记录当机密文件对待

Cursor 报告里最扎眼的一行字是：「a small number of those traces held credentials that had not been redacted」。翻译成人话：**agent 对话记录存在厂商服务器上，而你很可能在聊天里贴过真密钥**。

三层防御，今天就能做：

1. **换姿势，别贴明文**：需要给 agent 用密钥时，让它读环境变量（`$GITHUB_TOKEN`）、读 `.env`（先确认在 `.gitignore` 里）、或者用短期临时 token。对话一旦进了 traces，就脱离你的控制了。
2. **贴过就按泄露算**：翻一遍历史会话，凡是明文贴过 API key / 密码的，现在就去**轮换**。Cursor 已经逐户给 team admin 发了「你哪些 key 暴露了」的邮件；自家 agent 的聊天记录自己查，别等厂商通知。
3. **给 key 戴紧箍咒**：spend cap + budget alert 是底线（账单异动第一时间报警）；scoped key 把权限钉死在「只用哪些模型、调多少量」；能上 OIDC / 短期凭证就别用长效 key。Wiz 案例里一个被 scope 限住的 key 损失小得多，没设限的那个 **13 小时被刷了约 170 万次调用**。

一句话：**agent 时代，密钥卫生从「管好密码库」升级成「管好我跟 AI 说过的每一句话」**。

## 技巧二：Claude 月度回顾转正——会读你 Claude Code 会话的「复盘搭子」

昨天（11/24）Anthropic 把 **Monthly Recap（Reflect）** 从 beta 转正：Free / Pro / Max / Ultra 全档可用，最大的变化是——**它能读你的 Claude Code 会话和 Cowork 云端任务**，每月自动生成本月回顾。

- **入口**：网页版 / Claude Desktop / 手机 App 的 Settings → Reflect
- **用法**：月底花 5 分钟扫一眼「这个月我用 Claude Code 干了啥」，比翻会话列表高效十倍；写周报、做月度总结时直接薅现成素材
- **隐私开关**：页面里可以**删除已存储的 recap**。呼应上面技巧一——这类「汇总你行为」的功能，用不用是一回事，知道数据存在哪、能删，是另一回事

顺手一提，同天还上了一个 beta：**artifacts 可以直接安装成 Mac / Windows 桌面 app**（独立窗口 + 独立图标，还能像普通 app 一样跟操作系统交互）。让 Claude 给你写个小工具 → 一键变成真·软件，这条链路越来越短了。

## 快讯速览

- **Kimi Code 双端小补丁**（11/24）：CLI 2.7.3 + Desktop 1.4.3，修了「模型不可用后会话打不开」「MCP 需要浏览器授权时聊天里不弹授权入口」两个烦人 bug，建议顺手升级
- **Cursor 善后动作**：全部推理流量迁出 Fireworks、撤销其 key、重建扩展市场、全仓加 secret scanning——如果你 10/29 当天从 Cursor 市场装/更新过 Claude Code 扩展，官方清理脚本该跑还得跑
- **LiteLLM 用户注意**：Black October 期间 1.107.0 曾被恶意打包，最后一个干净版本是 **1.106.2**；官方已换新签名 key 发版，升级前核对签名
- **Zeabur 因被攻破宣布关停**——Black October 目前唯一「被打死的」平台，项目还没迁的抓紧了 🫠

---

*数据截至 2026-11-25，来源：Cursor 官方安全事件更新（11/13）、Wiz Research「Black October」报告、The Hacker News、Anthropic Release Notes（11/24）*
