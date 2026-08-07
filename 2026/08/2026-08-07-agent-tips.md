# 🤖 Agent 小抄 · 2026-08-07

> 向上、小惠，早！今天聊一个**Cursor 里容易被忽略的隐藏 Buff**。

---

## 💡 今日技巧：Cursor 的 `.cursorrules` 不只是「人设文件」

很多人把 `.cursorrules` 当 Prompt 用，写一堆"你是一个资深工程师…"，其实它更强大的用法是 **项目级上下文注入**。

### 具体怎么玩？

在 `.cursorrules` 里写这些内容，Cursor 会自动把它们注入到**每次 AI 对话**中：

```text
# 技术栈锁定
- 项目使用 Python 3.11 + FastAPI，不要生成 3.9 兼容代码
- 数据库操作统一用 SQLAlchemy 2.0 async 风格
- 测试框架：pytest + pytest-asyncio

# 代码风格
- 函数签名必须带类型注解
- 优先返回 TypedDict / dataclass，不要裸 dict
- 异常处理：业务异常用自定义 exception，不要裸 raise

# 项目结构
- API 路由在 /routes/
- 业务逻辑在 /services/
- 不要在路由层直接操作数据库
```

### 效果是什么？

Cursor 生成的代码**不再需要你反复纠正风格**。你打 `/fix` 或者让 AI 补全函数时，它会自动带上这些约束。

> 以前：写个函数 → AI 生成裸 dict → 你："改 dataclass" → AI 重改  
> 现在：AI 直接按你的规矩出代码，一次到位。

---

## 🎁 加餐：Kimi Code 的一个冷门但好用的技巧

Kimi Code 的 `@file` 引用支持**模糊匹配**，但很多人不知道它其实支持**行号范围**。

```
@src/main.py:45-80
```

这样 Kimi 只会读取那 35 行，**不会吃光你的上下文窗口**。在处理大文件时，这比 `@file` 全文引用精准得多。

---

*今日份 tips 就到这里。有用的话，明天继续 👋*
