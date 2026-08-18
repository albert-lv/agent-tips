# 🤖 Agent 小抄 · 2026-08-18

> 向上、小惠早！今天聊一个**每天都在做、但从来没让 AI 帮过忙**的事——**Git 提交**。
>
> 不是教你怎么 `git commit`，是让 Agent 当你的**提交前质检员 + commit message 写手**。🫠

---

## 💡 今日技巧：Agent 的「Git 搭档」模式

你是不是也经常这样：

> 改了一堆代码，临到提交却 staring at the diff 发呆——"我到底改了啥来着？"
>
> 或者 commit message 写成 `fix bug`、`update`、`wip` 然后被未来的自己骂。

Agent 工具其实能把这个流程包圆了。

---

### 技巧一：Claude Code 帮你「审 diff」

改完代码别急着 commit，先丢给 Claude 看一眼：

```bash
git diff
```

然后复制粘贴到 Claude Code（或者直接说 "review my uncommitted changes"，Claude 会自动看 working tree）：

> 💬 **你**：帮我 review 一下当前的改动，看看有没有明显的 bug、遗漏的边界情况、或者不该提交的 debug 代码。

Claude 会逐行分析，经常能抓到你漏掉的：
- 那个 `console.log` 忘了删 💀
- 改了一个函数签名但漏了调用处
- 新加的代码没处理错误分支
- 变量名打错了一个字母（对，这种 diff 自己看十遍都看不见）

> 🎯 **效果**：相当于一个**不会累、不会不好意思、且读过你整个项目**的 Code Reviewer。

---

### 技巧二：让 AI 写「人话版」commit message

别再用 `fix stuff` 了。把 diff 丢给 Claude：

> 💬 **你**：根据这个 diff 写一条 conventional commit 格式的 message，要让人一眼看懂改了什么、为什么改。

Claude 会输出类似：

```
feat(auth): add JWT refresh token rotation

- Implement sliding window refresh strategy
- Add token family tracking to prevent replay attacks
- Update /token/refresh endpoint to issue new rotation ID

BREAKING CHANGE: old refresh tokens are invalidated immediately
```

比你自己憋半小时像人话多了。

**进阶玩法**：在 Claude Code 里直接说：

> 💬 **你**：stage all changes and generate a commit message for me

Claude 会自己 `git add`、读 diff、写 message，你确认一下就 `git commit`。一条龙。🤝

---

### 技巧三：Cursor / Kimi Code 的「提交前检查清单」

如果你用 Cursor 或 Kimi Code，可以把这个做成**可复用的 prompt**：

```text
我要提交代码了，帮我检查：
1. 有没有遗留的 TODO、FIXME、console.log、debugger
2. 有没有修改了但没加到 git 的文件
3. 测试能不能过
4. 用一句话总结这次改动的核心目的
```

AI 会逐项检查并汇报。你就像有个**提交前的安检员**，专门拦那些"啊糟了忘了删"的狼狈时刻。

---

## 🎁 加餐：Windsurf 的「自动 commit」

Windsurf Cascade 有个冷门但好用的设定：在 Cascade 设置里打开 **"Auto-commit accepted changes"**，AI 每帮你改完一批代码会自动 `git commit`，并且 commit message 写得还挺像人话。

适合那种"让 AI 批量重构"的场景——改完不用你自己一条一条整理提交，Cascade 已经帮你切好了。

> ⚠️ 但建议只在**个人分支**开这个，主分支还是手动 review 一下再提交比较安全。

---

## 🎯 今日速查表

| 场景 | 做法 |
|------|------|
| 改完代码心里没底 | 丢 diff 给 AI review，抓 bug + 抓漏 |
| 写不出 commit message | 让 AI 读 diff 生成 conventional commit |
| 怕提交前漏东西 | 用固定 prompt 跑提交前检查清单 |
| Windsurf 批量重构 | 开自动 commit，AI 帮你切提交 |

**今天记住这个**：AI 不只能帮你写代码，还能帮你**把代码体面地送进去**。🎯

---

*明早见 👋*
