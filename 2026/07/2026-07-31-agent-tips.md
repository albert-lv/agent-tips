# 每日 Agent 技巧 — 2026-07-31

## 今日主题：Claude Code 的「批量重构模式」

Claude Code 有个隐藏很实用的技巧：**批量文件重构 + 自动测试验证**。

### 技巧 1：多文件联改

当你需要改一个函数签名，而这个函数被 10 个文件调用时：

```bash
claude "把 utils.parseDate() 改名为 utils.parseISO8601，所有调用点一起改，改完跑测试"
```

Claude Code 会：
1. 先 `grep` 找到所有调用点
2. 批量改完
3. 自动跑 `npm test` / `pytest`
4. 如果有报错，自动修，修到绿为止

**省下来的时间**：以前这种 refactor 你可能要开 10 个 tab 手动改，现在一句话搞定。

### 技巧 2：`--dangerously-skip-permissions`

如果你信任当前目录的代码（比如自己的项目），可以加这个 flag：

```bash
claude --dangerously-skip-permissions
```

效果：Claude Code 不再每次询问"我能改这个文件吗"，直接改。

**适合场景**：你已经 review 过它的方案，只想让它快点执行。
**不适合场景**：你不确定它要改什么的时候（默认询问模式更安全）。

---

> 💡 **一句话总结**：把 Claude Code 当"会自己跑测试的 refactor 机器人"用，比当"问答助手"效率高 10 倍。

明天见 👋
