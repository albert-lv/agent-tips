# 2026-06-06 Agent Tips

## 今日技巧：Kimi Code 的 /ask 模式

Kimi Code 的 `/ask` 指令特别适合快速澄清代码逻辑。和直接编辑不同，它只解释不改动，适合在不敢乱动 legacy code 的时候先用它"探探路"。

### 用法
```
/ask 这个函数为什么要用闭包而不是直接传参？
```

### 为什么好用
- 零风险：不会意外修改代码
- 上下文全：会自动读取当前文件和相邻文件
- 追问友好：可以连续追问，它会记住之前的解释

## 小技巧：Claude Code 的 `--allowedTools` 白名单

Claude Code 的 `--allowedTools` 可以精确控制工具权限。比如只让 AI 用 `ReadFile` 和 `EditFile`，防止它误操作数据库或发邮件。

```bash
claude --allowedTools ReadFile,EditFile,View
```

> 小惠和向上可以试试看，安全第一 😄
