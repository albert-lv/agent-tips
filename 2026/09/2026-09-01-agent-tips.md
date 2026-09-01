# 09-01 — Agent 的「进度条」—— 给 AI 一张 checklist，长任务不再烂尾

> **一句话总结**：把大任务拆成 checklist 丢给 Agent，让它自己打勾汇报。你不再担心它做到一半失忆，它也不再担心做到一半被你怀疑摸鱼。

---

## 🫠 你是不是也这样

你让 Claude/Cursor 干一个活儿，比如：

> "把这个老项目的 jQuery 全换成现代写法，再补一下测试。"

Agent 吭哧吭哧干了 20 分钟，改了 8 个文件。然后你一问：

> "测试补了吗？"

它：

> "啊？我们刚才说要补测试吗？"

💀 经典烂尾。不是它故意，是上下文太长，后面的把前面的挤掉了。

---

## ✅ 解法：给 Agent 一张「进度条」

别光说任务，把任务拆成 checklist，让 Agent **每做完一步自己更新进度**。

### 实战写法

**你输入：**

```
任务：把 src/legacy/ 下的 jQuery 代码迁移到原生 JS

进度清单（请每完成一步更新状态）：
- [ ] 扫描 legacy 目录，列出所有用到 jQuery 的文件
- [ ] 逐个文件替换 jQuery 为原生 JS（优先 querySelector/addEventListener）
- [ ] 检查是否有遗漏的 $ 引用
- [ ] 为改动较大的文件补基础单元测试
- [ ] 运行全部测试，确保没炸
- [ ] 总结改动清单，方便我 review

要求：每完成一步，在回复开头更新 checklist 状态，再展开说明。
```

**Agent 回复（示例）：**

```
当前进度：
- [x] 扫描 legacy 目录，列出所有用到 jQuery 的文件
- [ ] 逐个文件替换 jQuery 为原生 JS（优先 querySelector/addEventListener）
- [ ] 检查是否有遗漏的 $ 引用
- [ ] 为改动较大的文件补基础单元测试
- [ ] 运行全部测试，确保没炸
- [ ] 总结改动清单，方便我 review

---

扫描完成，发现 5 个文件有 jQuery 引用：
- src/legacy/modal.js（$.ajax, $.fn.click）
- src/legacy/dropdown.js（$.find, $.on）
- ...

开始第二步：逐个替换...
```

看到没？**进度可视化**。你可以随时 glance 一眼就知道到哪了，不用翻 50 轮对话。

---

## 🛠 跨工具都能用

| 工具 | 怎么玩 |
|---|---|
| **Claude Code** | 直接 paste checklist，Claude 会乖乖跟着勾 |
| **Cursor** | 在 Agent Chat 里给 checklist，Auto-run 模式下它自己 tick |
| **Windsurf** | Cascade 同样适用，长任务时特别有用 |
| **Kimi Code** | 新开 Session 时把 checklist 贴进去，防止重启后失忆 |

> 💡 **进阶**：如果任务超长，让 Agent **每完成 2-3 步就 commit 一次**。这样即使上下文炸了，你也能从 git 历史里找回进度。

---

## 🎯 为什么这招好用

1. **防失忆** — checklist 是外置记忆，不在上下文窗口里抢位置
2. **防焦虑** — 你随时知道进度，不用猜它到底在干嘛
3. **防烂尾** — 最后一步往往是「跑测试/写文档」，有了勾就不会漏
4. **可交接** — checklist 贴到新 Session 里，另一个 Agent 能接着干

---

## 📝 今天记住这个

| 场景 | 做法 |
|---|---|
| 任务超过 3 个步骤 | 先写 checklist 再开工 |
| Agent 开始重复/失忆 | 把 checklist 贴回去，让它接着勾 |
| 需要跨 Session 继续 | checklist + git commit = 完美接力 |
| 想让自己少焦虑 | 看勾比翻 50 轮对话爽 100 倍 |

> 🧠 **核心心法**：Agent 不是人，不会"记着"。给它外置记忆，它就能干长活。

---

*📅 2026-09-01 | 第 51 期*
