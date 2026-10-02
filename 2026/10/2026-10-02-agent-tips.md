# 10-02 — Agent 的「心电感应」+「三层脑」：编辑器报错自动直达，Windsurf 记忆别再用混

## 🤔 两个经典破防瞬间

**瞬间一：**VS Code 里一片飘红，你在 Problems 面板数出 17 个 TypeScript 报错，切到终端，把 `tsc` 输出复制、粘贴、发给 Claude Code——还要担心截全了没。而你的 IDE 明明就开着，Agent 却像瞎了一样，全靠你口述病情。

**瞬间二：**你在 Windsurf 里教 Cascade：「这个项目用 pnpm，别用 npm」。下周同事打开同一个仓库，Cascade 热情洋溢地跑了一条 `npm install`。你明明「教过」了——不，你教的是**存在你自己电脑里的那个它**。

今天两个技巧，一个治好「复制粘贴病」，一个治好「记忆错乱症」。

## 🛠️ 技巧一：Claude Code IDE 集成 —— 别再当你的「人肉报错机」

给 VS Code / JetBrains 装上官方 Claude Code 扩展（`Cmd+Esc` / `Ctrl+Esc` 直接唤起），或者在 IDE 自带终端里跑 `claude`（外部终端跑的话，会话里敲 `/ide` 就能连上）。连上之后，最香的是这三件事：

### 1. 诊断共享：Problems 面板自动进 Agent 的脑子

连接激活后，Claude 会多出一个 `mcp__ide__getDiagnostics` 工具——它能**直接读你 IDE 的 Problems 面板**，TypeScript 报错、lint 警告、Rust 编译错误，和你看到的一模一样。

也就是说，以后只需要说一句：

```text
把这些报错都修了
```

不再需要复制粘贴。省掉的不只是几秒钟，是「截图→描述→Agent 猜」这一整条误差链。

### 2. 选中即上下文：划一下，比 `@` 还快

扩展模式下，你在编辑器里**选中一段代码再提问**，选中的内容自动进上下文。写 review 评论草稿、纠结某段重构、问「这段为啥这么写」——零搬运成本。

### 3. diff 在 GUI 里审：告别终端里「脑补改动」

Agent 改完代码，改动直接铺在你熟悉的 IDE diff 视图里，行级高亮、逐块接受拒绝。之前在终端看文本流 diff 看到眼花的同学，这一步体验是质变。

> 💡 彩蛋：扩展还支持 `@terminal:终端名` 引用某个终端面板的输出——测试日志、构建报错，不用切窗复制。

**以前：** 你当传话筒，Agent 靠二手信息猜。
**现在：** Agent 和你看同一块屏幕，报错直达病灶。

## 🛠️ 技巧二：Windsurf 的「三层脑」—— Rules 是家规，Memories 是便签，Workflows 是菜谱

Windsurf 用户最容易踩的坑：以为 Cascade 只有一种「记忆」。其实它有**三套完全不同的系统**，用混了就会得到「我教过它啊，它怎么又忘了」的经典体验。

| 系统 | 长什么样 | 谁写的 | 进 Git？ | 团队共享？ |
|---|---|---|---|---|
| **Rules** | `.windsurf/rules/*.md` | 你手写 | ✅ | ✅ |
| **Memories** | `~/.codeium/windsurf/memories/` 本地文件 | Cascade 自动生成 | ❌ | ❌ |
| **Workflows** | `.windsurf/workflows/*.md` | 你手写 | ✅ | ✅ |

### 各管什么事

**Rules = 家规（必须遵守，人人遵守）**
团队规范、架构红线、测试要求——「永远用 React Query 管理服务端状态」「禁止改 generated 目录」。支持 4 种触发模式（常驻 / 按文件 glob / 模型自己判断 / 手动激活），单个文件上限 12000 字符。写具体，别写「写干净的代码」这种正确的废话。

**Memories = 便签（你自己省事）**
对话中 Cascade 自动记下「这人在重构 auth 模块」「他用 pnpm」。也可以主动说 `remember that...` 手动创建。**免费、不占 credit**，但存在你本地、不进 git——同事那边完全不知情。所以它只适合「私人的、琐碎的」上下文。

**Workflows = 菜谱（重复流程一键复用）**
就是我们 09-15 讲 Claude Code `.claude/commands/` 时的那个思路：把「每次开 PR 前的自检清单」存成 `/code-review`，敲斜杠就出菜。

### 🚨 最大的坑

**别把团队规范寄托在 Memories 上。** 它是概率性的：不一定抓到你说的重点，也不保证下次一定检索出来。官方原话：Memories 是便利层，不是可靠层。

口诀：**团队要遵守的 → Rules；自己省事的 → Memories；重复三遍的流程 → Workflows。**

> 💡 运维习惯：每季度翻一次 `~/.codeium/windsurf/memories/`，删掉过期的，把其中重要的升级成 Rules——便签变家规，才算真的沉淀了。

## 🎯 今天记住这个

| 场景 | 操作 |
|---|---|
| IDE 里一堆报错要 Agent 修 | 装扩展连上，直接说「把这些报错修了」（diagnostics 自动共享） |
| 想问某段代码 | 编辑器里选中再提问，自动进上下文 |
| 团队规范怕忘 | 写进 `.windsurf/rules/`，进 git，人人有份 |
| 个人小偏好 | 让 Cascade「remember that...」，走 Memories |
| Memories 攒了三个月 | 季度大扫除：删旧的，重要的升级成 Rules |

**一句话：IDE 集成的本质是「Agent 和你看同一块屏」，三层记忆的本质是「别拿便签当家规」——两件事都对了，重复沟通直接腰斩。**

明天见 👋
