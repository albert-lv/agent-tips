# 🕵️ Agent 每日一技 · 2026-06-12

> 周五早，今天聊两个「让 Agent 当你的代码质量守门员」的玩法

早上好，向上和小惠！前面几天聊了 Agent 怎么写代码、怎么规划任务、怎么管理上下文。今天换个角度——**让 Agent 帮你查代码、写测试**，你负责验收就行。

---

## 技巧一：把 Agent 当成「24小时在线的代码审查员」

很多人用 Agent 写代码，但写完就合并了，没利用它「读代码」的能力。其实 Agent 看代码的视角和人类 reviewer 不一样——它不会累、不会漏看、不会不好意思指出问题。

### 怎么玩？

**Claude Code 里：**

```bash
claude
```

然后丢一段代码或 diff 给它：

> "帮我 review 这段代码，重点看：是否有并发安全问题、错误处理是否完整、有没有魔法数字、变量命名是否清晰"

Claude 会逐行扫描，给出类似这样的反馈：

- 🚨 `user_id` 这里用的是 string 比较，如果前端传了 `"123"` 和 `"0123"` 会被当成不同用户
- ⚠️ 第 47 行 `time.Sleep(3)` 是魔法数字，建议抽成常量或配置项
- 💡 `fetchData()` 的错误直接 `log.Printf` 掉了，调用方感知不到失败，建议返回 error 让上层决定

**Kimi Code 里：**

直接粘贴代码或 `@file.py` 引用文件：

> `@auth.py 帮我 review 这个模块，重点关注安全漏洞和边界情况处理`

Kimi 会结合整个项目的上下文，甚至能发现「这个函数在另一个文件里被调用时没做参数校验」这种跨文件问题。

### 什么时候用？

- **PR 前自检**：合并前让 Agent 先看一遍， catching 80% 的低级问题
- **Review 别人的代码**：先让 Agent 过一遍，标记出重点需要关注的行，你自己再看核心逻辑
- **Legacy 代码摸底**：接手老项目时，让 Agent 帮你标出「这里可能有坑」的地方

### Pro tip

不要只说 "review 一下"，要**给审查维度**：

```
请从以下角度 review：
1. 安全性（SQL 注入、XSS、越权）
2. 性能（是否有 N+1 查询、大循环里是否有 IO）
3. 可维护性（命名、注释、函数长度）
4. 边界情况（空指针、零值、超时）
```

Agent 越清楚你要什么，输出越精准。

---

## 技巧二：让 Agent 帮你写「让人放心的测试」

写测试是开发者都知道重要、但经常偷懒的事情。Agent 写测试有天然优势：它不会嫌烦，能覆盖你懒得想的 edge case，而且格式规范。

### 怎么玩？

**Claude Code 里：**

> "给 `calculateDiscount` 函数写完整的 pytest 测试，要覆盖：正常情况、零值、负数、超大数值、浮点数精度"

Claude 会生成：

```python
def test_calculate_discount_normal():
    assert calculateDiscount(100, 20) == 80

def test_calculate_discount_zero_price():
    assert calculateDiscount(0, 20) == 0

def test_calculate_discount_negative_discount():
    with pytest.raises(ValueError):
        calculateDiscount(100, -10)

def test_calculate_discount_huge_values():
    # 测试大数是否溢出或精度丢失
    result = calculateDiscount(1e15, 50)
    assert result == 5e14
```

**Cursor 里：**

选中一个函数，按 `Ctrl+K`（或 `Cmd+K`），输入：

> "generate unit tests for this function with edge cases"

Cursor 会直接在当前文件或 `*_test.go` / `test_*.py` 里生成测试代码。

### 进阶玩法：让 Agent 先分析再测试

不要直接说"写测试"，先让 Agent 分析函数：

> "先分析 `parseUserInput` 函数可能的输入类型和边界情况，然后为每种情况写测试"

这样 Agent 会先列出：
- 正常字符串
- 空字符串
- 超长字符串（> 1024 字符）
- 包含特殊字符（emoji、中文、null byte）
- 数字字符串 vs 纯文本
- 并发调用场景

然后针对每种情况生成测试。**覆盖率直接拉满**，而且你学到了自己没想到的 edge case。

### 什么时候用？

- **新功能开发**：写完功能函数，顺手让 Agent 把测试补上
- **补技术债**：老项目没测试，让 Agent 批量生成 baseline 测试，后续再迭代
- **面试准备**：向上和小惠不是在准备面试吗？让 Agent 给你写测试，然后你检查它漏了哪些 case——这本身就是一道好题。

---

## 🧠 今日思考

Agent 最被低估的能力不是「写新代码」，而是**「读现有代码」和「系统性思考」**。

人类 reviewer 看 100 行代码就累了，Agent 看 1000 行也一视同仁。人类写测试时容易忽略 "用户可能输入 emoji" 这种奇怪边界，Agent 会老老实实全列出来。

**把 Agent 当作你的「质量放大器」**：你负责判断什么是重要的，Agent 负责帮你覆盖全面性和细节。

周五了，祝大家周末愉快，下周继续挖 Agent 的隐藏功能 👋

---

*P.S. 向上和小惠如果这周试了哪个技巧，欢迎反馈效果，下周可以针对你们的实际场景再深挖 🫡*
