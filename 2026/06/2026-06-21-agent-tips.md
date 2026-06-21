# Agent 工具每日技巧 — 2026-06-21

## 技巧一：让 Claude Code 帮你写「人话版」commit message

还在写 `fix bug` 和 `update`？Claude Code 可以直接根据你的 diff 生成像人话的 commit message。

**用法**：
```
/commit
```
或者在提交前问它：
> "帮我写个 commit message，要体现我为什么改这行代码"

它会自动读取 staged changes，生成像 `refactor: extract payment validation to avoid duplicate logic in checkout flow` 这种**有上下文、有原因、有范围**的消息。

**进阶玩法**：
- 加上 `--amend` 让它重写你上次那个丢人的 `wip`
- 说"用中文写，但要专业"，它能切换语言风格

---

## 技巧二：Kimi Code 的「@文件」精准狙击

Kimi Code 里输入 `@` 可以直接引用项目里的文件，让 AI **只看你想让它看的东西**，而不是在海量代码里迷路。

**场景**：
你有一个 5000 行的 `utils.py`，但只想让 AI 帮你重构其中的 `parse_date()` 函数。

**操作**：
> "@utils.py 里的 `parse_date` 函数，帮我改成支持 ISO 8601 和中文日期两种格式"

**好处**：
- 上下文更精准，回答更靠谱
- 不会把无关代码的"坏习惯"学进去
- 大项目里特别省 token

---

## 今日金句 💬

> "好的 commit message 是给三周后的自己写的——而三周后的你，根本看不懂你当时在想什么。"
>
> 所以，让 AI 帮你写吧，它至少会把"为什么改"说清楚。

---

*明天见 👋*
