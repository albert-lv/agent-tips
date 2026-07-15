# 2026-07-15 Agent Tips：给你的项目写个"人设文件"

每次打开 Claude Code 都要跟它解释一遍"我们这个项目用 Go 不用 Python"？

太惨了。今天教你一招：给项目写一个"人设文件"，让 AI 自动记住你的规矩。

---

## 1. Claude Code 的 `Claude.md`

在 repo 根目录放一个叫 `Claude.md` 的文件，Claude Code 会自动读取它，相当于每次启动都自动注入一段 system prompt。

内容可以写：
- 技术栈（"我们用 Go 1.22 + Echo + GORM"）
- 代码风格（"函数不超过 50 行，错误必须 wrap"）
- 项目架构（"handlers 在 /api，业务逻辑在 /service"）
- 甚至你的个人偏好（"不要用 fmt.Println，用 slog"）

## 2. Cursor 的 `.cursorrules`

Cursor 用户一样，放个 `.cursorrules` 在根目录。语法更自由，可以写自然语言规则。

## 3. 为什么这很香

- 省掉 30% 的重复废话
- 让 AI 生成的代码风格统一，不用你改
- 新 agent 第一次就能上手，不用你教

## 快速开始

在项目根目录创建 `Claude.md`（或 `.cursorrules`），然后写：

```markdown
# 项目规范

- 语言：Go 1.22
- 框架：Echo v4
- 数据库：PostgreSQL，用 GORM
- 日志：用 slog，不要用 fmt.Println
- 错误处理：所有错误必须 wrap 并返回
- API 规范：RESTful，返回 JSON 格式统一
```

然后打开 Claude Code，它会自动加载。试试看！

---

* tomorrow: 聊聊 Kimi Code 的"一键补全上下文"*
