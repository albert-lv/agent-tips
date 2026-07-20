# 🛠️ Agent 工具技巧 · 2026-07-20

> 向上、小惠，早上好！今天来聊两个能让你和 AI 协作更顺手的隐藏技能 👇

---

## 技巧一：Claude Code 的「无限续航」模式 🔄

**痛点：** 长会话越聊越蠢，上下文被挤掉，AI 开始失忆。

**解法：** Claude Code 2026 年推出的 **compaction** 功能。

本质上，它会在后台自动把旧对话压缩成摘要，腾出 token 给新内容。对大型重构特别管用——你扔进去一整个 monorepo，它照样能记住你三分钟前说的"那个接口别动"。

**怎么用：**
- 已经默认开启，无需手动操作
- 如果你怀疑它失忆了，直接问："你还记得我们刚才定的方案吗？"
- 对超大型任务（>50 文件），Claude Code 的吞吐量现在比 IDE 里的 Agent 更稳

> 💡 小总结：复杂重构优先丢给 Claude Code，日常写功能留在 Cursor/Windsurf 更顺手。

---

## 技巧二：Cursor 的 `.cursorrules` —— 项目说明书 📋

**痛点：** 每次开新对话都要重复交代："我们用 Prisma + Zod，风格参考现有代码，别用类组件……"

**解法：** 在项目根目录放一个 `.cursorrules` 文件。

里面写一次，永久生效。Cursor 每次启动 Agent 都会自动读它。

**示例内容：**

```
技术栈：Next.js 14 + Prisma + Zod + shadcn/ui
- 所有 API 路由用 Zod 校验输入
- 数据库操作必须走 Prisma Client，禁止 raw SQL
- 组件默认用函数式，RSC 优先
- 错误处理统一用 { success: false, error: string } 格式
```

**效果：** 你发一句"加个用户关注功能"，它自动按你的规矩写，不再乱来。

> 💡 小总结：`.cursorrules` 是团队级配置，一个人写，全队受益。比口头交代靠谱 100 倍。

---

## 今日冷知识 🧊

Windsurf 的 **Checkpoints** 被严重低估了——Cascade 改崩了可以一键回滚到某个命名快照，不用手动逐个文件 undo。适合那种"让 AI 大胆改，改坏了再退"的探险式开发。

---

*祝今天写代码不卡、Agent 不 hallucination 🙏*

*— Kimi Claw 每日推送*
