# 10-14 — Agent 的「车库整理日」：外挂越装越多，Context 房租谁来付？

## 🤔 你的 Agent 是不是也得了「收集癖」

09-11 学了装 MCP，10-07 又逛插件市场提了新车。一个月后回头看：数据库 MCP、浏览器 MCP、GitHub MCP、飞书 MCP、自己手搓的 FastMCP……装的时候一个「yes」的事，现在每次开会话，光这些外挂的「说明书」就吃掉小一万 token——还没开始干活，房租先交出去了。

更要命的是**抢戏**：你只是问个 SQL，它却认真研究起了浏览器自动化的 14 个工具。外挂多 ≠ 能力强，很多时候是 **Context 负重越野**。

今天给车库做个整理。🧹

## 🛠️ 技巧一：Claude Code——MCP 按需开关，按项目租房

### `/mcp` 面板：@ 一下就能开关

进 `/mcp` 面板，**@mention 某个 server 就能单独启停**它（v2.0.10+）。开工前花十秒扫一眼：这个活儿需要数据库吗？不需要就停掉。工具列表瞬间清爽，模型选工具也准了。

### 租房别买房：scope 选对，一省一辈子

`claude mcp add` 默认装进 user scope——意味着**你所有项目、每次启动都背着它**。正确姿势是按项目装：

```bash
claude mcp add --scope project postgres -- npx @modelcontextprotocol/server-postgres ...
```

只在那个仓库生效，别的项目零负担。user scope 只留真正到处都用的（一两个顶天）。

### `/plugin` 面板也变聪明了

v2.1.110 起，Installed 标签页会把**需要处理的和收藏的置顶**，禁用的折进折叠组，按 `f` 收藏常用插件。再配合 `/doctor` 的 MCP scope 检查（同一个 server 在多个 scope 配了不同 endpoint 会警告）——车库仪表盘齐活。

## 🛠️ 技巧二：Kimi Code——`/plugins` 一键瘦身 + 信任徽章防翻车

Kimi Code 的 `/plugins` 是一个面板管全部（`Tab` / `Shift-Tab` 切页）：

| 标签页 | 用途 |
|---|---|
| **Installed** | 切换启停、移除、管 MCP、看详情——瘦身主战场 |
| **Official** | Kimi 官方维护的市场插件 |
| **Third-party** | 第三方市场插件，**装前有确认提示** |
| **Custom** | GitHub URL / zip / 本地路径直装 |

两个细节很戳人：

- **更新徽章**：Installed 上有可更新的插件会亮徽章，按 `Enter` 直接装更新——外挂也会过时，旧版可能就是 bug 本 bug；
- **信任级别徽章**：每个插件标着 `kimi-official` / `curated` / `third-party`。第三方 = 别人的代码在你机器上跑，装之前瞄一眼来源，和 10-03「安全员」一个道理。

插件改完**不用重启会话**：`/reload` 刷新后当前会话直接生效。

## 🧭 心法：外挂的「月租」公式

粗算一笔账：

```
每个 MCP server 的工具描述 ≈ 500~2000 token
装 10 个 ≈ 1~2 万 token 的固定「月租」
（每次会话都交，不管你用不用）
```

整理原则三条：

1. **用到才开**：`/mcp`、`/plugins` 十秒开关，比省着用强；
2. **就近租房**：项目级的活，scope 就选 project，别全款买成 user；
3. **定期盘点**：每装一个新外挂，顺手想想有没有可以退役的老家伙。和 10-06「体检日」的 `/doctor` 搭配使用——一个查记忆，一个查外挂。

## 📌 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| 某个 MCP 今天用不上 | `/mcp` → @ 它，停用 |
| MCP 只在某项目用 | `claude mcp add --scope project ...` |
| 插件太多找不到重点 | `/plugin` Installed 页，按 `f` 收藏置顶 |
| 查 MCP 配置冲突 | `/doctor`（多 scope 不同 endpoint 会报警） |
| Kimi Code 插件瘦身 | `/plugins` → Installed → 切换 / 移除 |
| 装了第三方插件不放心 | 看信任徽章：official / curated / third-party |
| 插件刚更新完 | `/reload`，当前会话直接生效 |

> 外挂的意义是「随时能调用的肌肉」，不是「挂在身上的沙袋」。今天下班前，花两分钟给车库透透气——你的 Context 会感谢你的。🧹
