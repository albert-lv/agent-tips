# 10-07 — Agent 的「试驾员」：嘴上说「已完成」不算数，让它自己上路点一圈

## 🤔 一个每周上演的黑色喜剧

周五下午，Agent 拍拍胸脯：「注册流程重构完成，所有测试通过 ✅」

你顺手一点——注册按钮没反应。console 里一片血红。它改的是代码，跑的也是测试，**唯独没有真的打开过浏览器**。你花了十分钟手动复现，然后把截图甩回去：「你自己看看这能用吗？」

昨天（10-06）刚给配置做完体检，今天该上路实操了。两个技巧：一个给 Agent 发「车钥匙」（浏览器），一个带你去「人才市场」（插件市场）——昨天那个背后灵，其实只是货架上的冰山一角。🚗

## 🛠️ 技巧一：Playwright MCP —— 让 Agent 亲自开浏览器验收自己的活

微软官方出品的 `@playwright/mcp`，一行命令接进 Claude Code：

```bash
claude mcp add playwright -- npx @playwright/mcp@latest
```

（Cursor / Windsurf / Kimi Code 同理，往 mcpServers 里加同一条配置即可，MCP 是通用协议）

### 它能干什么？

接好之后，Agent 手里多了一套真·浏览器工具：导航、点击、填表、读页面、截图。你直接下指令：

> 「dev server 我起好了，打开 localhost:3000，把注册流程完整走一遍，走完截图给我看。」

它真的会自己打开 Chromium、填邮箱密码、点提交——**console 报错它当场就能看见**，按钮点不动它第一个发现，比你当传话筒效率高一个数量级。

### 为什么这个比「截图投喂」高级

9-13 我们讲过扔截图让 AI 干活，但那是**你**截图**它**看。这个是**它自己操作、自己看**：更妙的是，它默认通过 accessibility tree（页面的结构化文本骨架）读页面，不是逐张截图喂视觉模型——token 便宜、定位准，生成的选择器还是语义化的（`getByRole` 这种），**顺手就能沉淀成可复跑的 Playwright 测试**。等于雇了个试驾员，试驾完还帮你写了检测报告。

> 适合的场景：改完前端自查、 PR 前的冒烟测试、复现「我这边好好的啊」式 bug、抓取需要登录的页面数据。

## 🛠️ 技巧二：插件市场 —— 别重复造轮子，「人才市场」直接提车

昨天雇背后灵用的 `/plugin enable ...@builtin`，你可能已经感觉到了：Claude Code 现在有个完整的插件生态。

### 市场在哪

- **官方市场 `claude-plugins-official`**：首次启动会自动挂载，Anthropic 精选，311 个插件。直接敲 `/plugin` 打开 Discover 页慢慢逛。
- **社区市场 `anthropics/claude-plugins-community`**：2,282 个插件，过自动化安全筛查、每个都钉死在 commit SHA 上：

```
/plugin marketplace add anthropics/claude-plugins-community
```

- **第三方/自建**：`/plugin marketplace add owner/repo`，一个 GitHub 仓库就是一个市场。团队可以把内部 SOP 打包成插件统一分发。

### 逛市场前，两个心眼要长

1. **安装前看详情页的「Context cost」**——官方插件会标两个数：每轮对话的固定税 + 触发时的开销。有的插件每轮白吃几千 token，比外包出去的脏活还贵。
2. **权限大的插件先读清单**——插件能带 hooks 和 MCP server（等于能跑命令、能联网）。装之前看一眼「Will install」列表里都有什么，和 10-03 的 `/security-review` 一个思路：**先划信任边界，再放权**。

## 📌 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| Agent 说「前端改完了」 | 「自己打开 localhost 走一遍流程，截图给我看」 |
| 接浏览器给 Agent | `claude mcp add playwright -- npx @playwright/mcp@latest` |
| 想要现成的技能/插件 | `/plugin` 逛市场，社区市场先 `marketplace add` |
| 装插件前 | 看 Context cost + Will install 清单，别让轮子比车重 |

> 配置体检完要上路，活干完要验收。让 Agent 自己点自己的网页，就像让厨师吃自己做的菜——说好吃不算数，吃了没吐才是真的好。🍳
