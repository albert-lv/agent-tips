# 10-15 — Agent 的「统一家规」：AGENTS.md 从方言到普通话，一份管住全队

## 🤔 你的项目是不是也在玩「配置叠叠乐」

08-30 那天我们给项目写了 CLAUDE.md，08-31 又配了 .cursorrules，后来 Copilot 来了还得补一份 copilot-instructions.md……同一份「构建命令、测试流程、代码风格」，**手写三遍，漂移三倍**。改了一处忘了另一处，三个工具各有各的脾气，最后连哪个才是「真·家规」都说不清。

好消息：2026 年这波，**AGENTS.md 已经从社区约定升级成正式标准了**——Linux Foundation 旗下的 Agentic AI Foundation 在维护，30+ 家 Agent 原生认它。今天的主题：怎么把 N 份家规收敛成一份，让全队工具都说普通话。🗣️

## 🛠️ 技巧一：一份 AGENTS.md，谁来了都能读

### 覆盖面有多广？

把你的家规写到仓库根目录的 `AGENTS.md` 里，以下工具**开箱即读**：

| 工具 | 读取方式 |
|---|---|
| **OpenAI Codex** | 原生支持，还搞了嵌套（它自家仓库铺了 88 个） |
| **Cursor** | 原生支持（`.cursorrules` 已是 legacy） |
| **GitHub Copilot** | 原生支持 |
| **Windsurf / Kimi Code / Gemini CLI / Cline / Aider** | 原生支持 |

### Claude Code 的小脾气：认识，但有条件

Claude Code 从 **v2.1.277（9 月 18 日）** 起认 AGENTS.md——**前提是仓库里没有 CLAUDE.md**。两个都在？它只读 CLAUDE.md，AGENTS.md 被当空气。

三种解法，按推荐排序：

```bash
# ① 最省心：symlink，一份真身多处引用
ln -s AGENTS.md CLAUDE.md

# ② 更灵活：CLAUDE.md 里一行 import
echo "@AGENTS.md" > CLAUDE.md

# ③ 团队约定：干脆删掉 CLAUDE.md，只留 AGENTS.md
```

方案 ② 的好处是 CLAUDE.md 里还能追加 Claude 专属的小灶（比如 hooks 配置说明）；方案 ① 最省事，改一处全同步。

## 🛠️ 技巧二：写好通用家规的三个避坑点

### 坑一：`@import` 跨文件引用，出了 Claude Code 就失灵

Claude Code 的 CLAUDE.md 支持 `@docs/frontend.md` 这种语法把别的文件引进来——**但其他工具全部把这个当纯文本**，Codex、Kimi、Cursor 都不解析。所以**通用家规必须是自包含的**：一份文件说清楚，别拆成「主文件 + 引用」，否则别的工具读到的是一行废字。

### 坑二：长度就是税，500~2000 token 是甜区

家规每长一行，**每个工具、每次会话**都要背。官方建议 1~3 页（约 500~2000 token）：构建命令、测试指令、代码风格、安全红线写清楚，更深的文档（架构决策、贡献指南）**放链接**，让 Agent 按需自己去读。Codex 默认 32KiB 上限，超了直接截断——写太长的结局就是后半截家规白写。

### 坑三：嵌套家规 = 就近原则，monorepo 狂喜

AGENTS.md 支持目录层级：**离被编辑文件最近的那份赢**。monorepo 里根目录写全局约定（怎么跑测试、commit 规范），`packages/web/` 里放前端专属规矩（用 Tailwind 别写 inline style）。Codex 官方仓库自己就是活的例子——88 个 AGENTS.md 铺满目录树，每个子包自报家门。

### 加分项：本地覆盖文件，团队规矩与个人偏好分开

| 工具 | 本地覆盖文件 | 特点 |
|---|---|---|
| Codex | `AGENTS.override.md` | 官方指定 gitignore 掉 |
| Claude Code | `CLAUDE.local.md` | 个人偏好不污染团队配置 |

团队的家规进 git，你个人的怪癖（「commit 信息别带 emoji」）走 override——**两不耽误，谁也不碍着谁**。

## 📌 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| 新项目要配家规 | 只写一份 `AGENTS.md`，30+ 工具通吃 |
| Claude Code 不读 AGENTS.md | 检查有没有 CLAUDE.md 抢戏 → symlink 或 @import |
| 想让家规引用别的文件 | 打住——@import 只有 Claude Code 认，通用家规必须自包含 |
| 家规越写越长 | 压到 1~3 页，细节放链接让 Agent 自己查 |
| monorepo 子包规矩不同 | 子目录再放一个 AGENTS.md，就近原则自动生效 |
| 个人偏好 vs 团队规矩 | 团队进 AGENTS.md（git），个人走 override 文件（gitignore） |

> 以前每个工具一份家规，像给每个室友各写一份「冰箱使用守则」——内容一样，格式不同，还总有人看的是旧版。现在 AGENTS.md 就是贴在冰箱门上的那份：**一份，大家都认，改一处全生效。** 还没统一的仓库，今天花五分钟做个 symlink 吧。🧊
