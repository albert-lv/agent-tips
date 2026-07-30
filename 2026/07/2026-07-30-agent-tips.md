# 🎯 让 Claude Code 真正"懂你"：CLAUDE.md 保姆级教程

> **日期**：2026-07-30  
> **难度**：⭐⭐（有手就行）  
> **适用**：Claude Code 用户，其他 agent 工具也有类似机制

---

## 先说痛点 💀

你是不是也经常这样——

每次开一个新对话，Claude 就开始瞎猜：
- "要用 `npm` 还是 `yarn`？" 🤔
- "这个项目的测试框架是 vitest 还是 jest？" 🤷
- "变量命名到底用 camelCase 还是 snake_case？" 😵‍💫

更离谱的是，你让它改个函数，它顺手把项目的单引号全改成双引号，还觉得自己干得很漂亮。

**这不是 Claude 的锅，是你没给它"入职培训"。**

---

## 大招：CLAUDE.md 🔥

Claude Code 其实支持**项目级系统提示词**，放在项目根目录就行：

```
你的项目/
├── .claude/
│   └── CLAUDE.md          ← 放这儿
├── src/
├── package.json
└── ...
```

**文件内容示例**（直接抄，按需改）：

```markdown
# 项目规范

## 技术栈
- 前端：Next.js 14 + TypeScript + Tailwind CSS
- 测试：Vitest（不是 jest！别装错！）
- 包管理：pnpm（禁止用 npm/yarn）

## 代码风格
- 单引号优先
- 函数组件用箭头函数
- 类型定义放 `types/` 目录
- 不要加 any，宁可写 unknown

## 常用命令
- 跑测试：`pnpm test`
- 类型检查：`pnpm typecheck`
- 格式化：`pnpm format`

## 注意事项
- 改代码前先跑测试，别搞崩 CI
- 不要修改不属于本次改动的文件
- UI 改动要截图确认
```

---

## 效果有多离谱？

**以前**：
> 你："加个大写转换函数"  
> Claude："好的，我在 `src/utils/` 新建了 `helpers.js`，用了 CommonJS 导出，还装了 lodash 的 capitalize..."  
> 你：😭

**现在**：
> 你："加个大写转换函数"  
> Claude："已在 `src/utils/capitalize.ts` 添加，用了 TypeScript，单引号，顺手补了单元测试。pnpm test 全过。"  
> 你：🫡

---

## 其他 Agent 怎么玩？

| 工具 | 配置文件 | 路径 |
|------|---------|------|
| **Claude Code** | `CLAUDE.md` | `.claude/CLAUDE.md` |
| **Kimi Code** | `KIMI.md` | `.kimi/KIMI.md`（或类似）|
| **Cursor** | `.cursorrules` | 项目根目录 |
| **Windsurf** | `.windsurfrules` | 项目根目录 |

**规律**：基本都是 `{工具名}.md` 或 `.{工具名}rules`，丢项目根目录或 `.工具名/` 文件夹里。

---

## 进阶骚操作 🧠

1. **分环境配置**：`.claude/CLAUDE.md` + `.claude/CLAUDE.prod.md`，不同场景切换
2. **团队共享**：把 CLAUDE.md 提交到仓库，全队共用一套"入职手册"
3. **动态更新**：发现 Claude 老犯同一个错？直接写进 CLAUDE.md，下次它就会了
4. **多项目通用**：放个全局的 `~/CLAUDE.md`，所有项目继承基础规范

---

## 一句话总结

> Claude 不是不会，是你没告诉它规矩。**CLAUDE.md 就是 AI 的入职培训手册**，写一次，爽半年。

快去给你的项目配一个，回来你会感谢我的。🫡

---

*明天见～*
