# 🎯 Agent 工具小技巧 — 2026-07-25

> 今天聊一个被很多人忽略，但用好了能省一半心的事：**系统提示词（System Prompt / Rules）的定制化。**

## 1. Claude Code：`.claude/CLAUDE.md` 是你的隐形副驾驶

Claude Code 会自动读取项目根目录下的 `.claude/CLAUDE.md`，把它当成「你教它的第一课」。

**用法：** 在项目根目录创建 `.claude/CLAUDE.md`，写入你的偏好——比如：

```markdown
# 我的编码习惯

- 优先用 TypeScript，不要用 any
- 写完后先跑 `npm test`，通过了再告诉我
- 遇到不确定的设计决策，先问我，不要自己猜
- 代码注释用中文，提交信息用英文
```

**效果：** 每次 Claude 进入你的项目，它就已经「懂你」了。不用再每次开场白交代一堆。

💡 **进阶玩法：** 你可以在 `.claude/` 下放多个文件，比如 `CLAUDE.md`（通用规则）、`API_CONVENTIONS.md`（接口规范）、`TESTING.md`（测试要求）。Claude 会读整个目录。

---

## 2. Cursor：`.cursorrules` 文件，一次配置，全局生效

Cursor 的规则文件 `.cursorrules` 同理，放在项目根目录即可。但它有个更狠的招：

**在 Cursor Settings → General → Rules for AI 里设置全局规则**，这样所有项目都默认继承。项目级的 `.cursorrules` 可以覆盖或补充。

**一个小技巧：** 在 `.cursorrules` 里加一行：

```
When suggesting code changes, always provide the full file content, not just the diff.
```

这样 Cursor 的 Agent 模式就不会偷懒只给片段，而是直接输出完整文件，减少你手动拼接的麻烦。

---

## 🧠 为什么这招值得用？

因为 agent 工具最怕的就是「每次都得重新交代」。你把偏好写成文件，等于给 agent 装了一个「长期记忆」。下次打开项目，它一上来就知道：

- 你的技术栈
- 你的代码风格
- 你的测试习惯
- 甚至你的「先问再改」原则

**省掉的不仅是打字时间，还有那种「我说了五遍它还是没记住」的血压。**

---

*明天见 👋*
