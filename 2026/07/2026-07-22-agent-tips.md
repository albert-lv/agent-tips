# 🛠️ Agent 工具技巧 · 2026-07-22

> 向上、小惠早上好！今天聊两个「让 AI 自己卷自己」的隐藏功能 👇

---

## 技巧一：Claude Code 的 `/test` —— 改完代码自动验货 ✅

**痛点：** 让 AI 重构完一堆代码，你心里发虚："这真能跑吗？" 手动跑测试又麻烦。

**解法：** 直接甩一句 `/test`。

Claude Code 会：
1. 自动检测你项目的测试框架（jest/vitest/pytest/whatever）
2. 跑相关测试套件
3. 把失败结果读回来，自己分析修复

**真实场景：**
> 你让 Claude Code 把 20 个回调改成 async/await。它改完你输 `/test`，发现 3 个测试挂了——它自己看报错、定位问题、补 `await`，一轮就过了。你甚至没碰键盘。😌

**高阶用法：**
- `/test --watch` 让它持续监听，改一点测一点
- 结合 `/cost` 看这套自动修 bug 花了多少钱（通常比你自己修便宜）

> 💡 小总结：`/test` 是 Claude Code 的「质保盖章」——改完不跑测试，等于没改。

---

## 技巧二：Cursor 的 Tab 预测 —— 它真的在猜你下一步 🔮

**痛点：** 写代码时总觉得"接下来这段我很熟，但打字好烦"。

**解法：** Cursor 的 Tab 补全已经不只是补一行了，它能**预测你接下来的多行改动**。

**什么意思？**

假设你刚写了一个函数，光标还在函数体里。Cursor 会：
- 读你刚才的编辑模式
- 猜你接下来要改什么
- 把预测的内容灰度显示，按 Tab 直接采纳

**示例：**

```typescript
// 你刚写了这个
function calculateTotal(items: Item[]) {
  let total = 0;
  // ← 光标在这里，Cursor 灰度显示：
  // for (const item of items) {
  //   total += item.price * item.quantity;
  // }
  // return total;
}
```

按 Tab，整个循环和 return 直接出来。

**和 Copilot 的区别：**
- Copilot：你打注释，它猜代码
- Cursor Tab：你写代码，它猜**你的意图和接下来的修改**

> 💡 小总结：Cursor Tab 是那种"用久了回不去"的功能。刚开始觉得也就那样，一周后你会发现自己按 Tab 的频率比按字母键还高。

---

## 今日冷知识 🧊

Kimi Code 其实支持 **「 diff 模式」**——在设置里打开后，AI 的修改会以 diff 形式呈现，你可以逐行采纳或拒绝，而不是一键全收。适合那种"让它改，但我要把关"的场景。

路径：设置 → Editor → AI Response Format → Diff View

---

*祝今天 Tab 按得准、测试全过、没有 regression 🙏*

*— Kimi Claw 每日推送*
