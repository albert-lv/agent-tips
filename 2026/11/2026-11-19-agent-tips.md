# 2026-11-19 —「Claude 开始写 JavaScript 上班了」：Code Mode 把工具调用从「单发」升级成「脚本」，外加一个 token 不进 shell history 的 MCP 小技巧

> Claude Code 这周上了个狠活：2.1.323（11/12）悄悄发布实验功能 **Code Mode**——Claude 不再一次一个工具调用地「试探」，而是**自己写一段 JavaScript，把「搜文件 → 改代码 → 跑测试」串成一次执行**。交互范式直接从「一问一答」变成「写脚本批处理」。今天讲怎么开、什么时候该开、以及开之前必须知道的限制。

## 📰 今日热点背景

- **Code Mode（2.1.323，11/12）**：实验性 opt-in 功能。传统模式下 Claude 每调一个工具都要等结果回来再决定下一步；Code Mode 下 Claude 直接生成一段 JS（`$` 执行 shell、`edit()` 改文件），并行发命令、结果自己过滤，**只把最后需要看的部分带回上下文**。省的是 round-trip，攒的是 token。
- **Claude Desktop 上线 release channel（11/17）**：可以选 Stable（默认，慢但稳）或 Latest（每天一更，追新功能）。想第一时间玩 Code Mode 这类实验功能的，切 Latest。
- **Claude Meetings beta（11/12）**：Team/Enterprise 用户可以把 Claude 拉进 Google Meet 当参会者——记笔记、现场答疑、顺手生成文档和白板。「会议纪要由 AI 写」正式从外挂变成原生。
- **插件生态 npm 化**：`plugin.json` 新增 `engines["claude-code"]` 字段，版本不匹配的插件直接不装/不加载。插件市场开始有了 node_modules 那味儿。

## 技巧一：Code Mode 上手 —— 一个环境变量的事

**开启**（需要 v2.1.323+，且是 Claude 订阅或 Console 账号，走 gateway/Bedrock/Foundry 的暂时无缘）：

```bash
# 尝鲜：单次开启
CLAUDE_CODE_EXPERIMENTAL_CODE_MODE=1 claude

# 长情：写进 ~/.claude/settings.json 的 env 里
```

第一次会问你要不要开，确认后 Claude Code 重启，顶部出现 `Code Mode (experimental) is on` 就算成功。

**它长什么样**：你说「把项目里所有 Markdown 的失效本地链接找出来」，Claude 可能直接写出这种代码：

```js
const files = await $`rg -l "function retryPolicy" src`.text();
const target = files.trim().split("\n")[0];
await edit(target, "maxAttempts: 3", "maxAttempts: 5");
({ edited: target, tests: $`npm test -- retryPolicy`.nothrow() });
```

最后那个对象就是「带回给 Claude 看的结果」——中间几百行 rg 输出它自己消化，**不占你的上下文窗口**。

**什么时候值得开**（官方点名场景）：

- 一个改动要横跨很多文件的重复操作（批量重命名、统一改格式）
- 搜索结果要先过滤再细看（先 `rg` 出 50 个文件，代码里筛到 3 个再读）
- 好几条互相独立的命令可以并行跑

**开之前必须知道的限制**（这部分建议截图存着）：

- **仅终端交互**：`-p` 模式、Agent SDK、VS Code 面板、Desktop、cloud session 全都不支持
- **代码最长跑 10 分钟**（后台模式可延长到 2 小时，但要 auto / bypassPermissions 模式起步）
- **resume 有坑**：Code Mode 会话只能带着环境变量 `=1` 去 `--continue` / `--resume`，忘了就拒载
- **Remote Control、Claude in Chrome、computer use 全不兼容**
- 模型要求 Opus 5.5 / Sonnet 5.5 / Haiku 5.5 / Fable 5.2

**想看 Claude 写的代码**：对话里只显示工具调用结果（diff 照常出现），按 `Ctrl+O` 打开 transcript，Code 行展开就是完整 JS——这也是个免费的「让最强模型给你写自动化脚本」生成器，好的片段顺手薅进自己的 toolkit。

## 技巧二：`claude mcp add-json` 从 stdin 读配置 —— token 别躺在 shell history 里

接 MCP server 时最顺手的路子是 `claude mcp add-json myserver '{"url":"...","headers":{"Authorization":"Bearer sk-xxx"}}'`——但这条命令会**原样躺在 shell history 和进程列表里**。2.1.319 起支持从 stdin 读：

```bash
# JSON 从标准输入进，token 不进 history、不出现在 ps 输出
cat myserver.json | claude mcp add-json myserver -
```

或者管道里现写也行：`echo '{"type":"http","url":"..."}' | claude mcp add-json myserver -`。

注意一个在 session 里不能用的细节：**`add-json` 不能在 Claude Code 会话内部调用**（得在普通 shell 里跑）。经常接新 MCP 服务的，把这个习惯刻进肌肉：凡是带凭证的 JSON，一律走 stdin。

## 🥚 彩蛋：两个平台侧更新

- **Claude Desktop release channel（11/17）**：Stable 走慢节奏稳字当头，Latest 每天一更。想追 Code Mode 这类实验功能的切 Latest；团队机器建议留 Stable，别大家一起当小白鼠。
- **Claude Meetings（11/12）**：Meet 会议里加一个「看得见」的 Claude 参会者，实时记笔记、答问题、产出文档/白板/幻灯片。下次例会可以名正言顺地「带 AI 开会」了——希望它不会在你说「这块我来补充一下」的时候抢话。
- **plugin.json 的 `engines["claude-code"]`**：跟 npm 的 `engines.node` 同款语义——声明插件兼容的 Claude Code 版本区间，超出范围不装不载不更新。做插件分发的，今天就去补上，别让用户的插件在 2.2 那天集体暴毙。

## 🎯 今天记住这个

| 场景 | 动作 |
|---|---|
| 批量跨文件修改 / 搜索结果要先过滤 | Code Mode：`CLAUDE_CODE_EXPERIMENTAL_CODE_MODE=1 claude` |
| 想看 Claude 批量操作背后写的 JS | `Ctrl+O` 展开 Code 行，好片段薅进自己 toolkit |
| Code Mode 会话续聊 | `--continue` / `--resume` 时环境变量必须保持 `=1` |
| 代码跑到一半超时 | 后台模式（auto / bypassPermissions 起步）最多 2 小时 |
| MCP 配置里带 token | `cat cfg.json \| claude mcp add-json name -`，别写进 history |
| 想第一时间追实验功能 | Claude Desktop 切 Latest channel |

---

*每天 08:42 更新 · 向上 & 小惠的 Agent 工具笔记*
