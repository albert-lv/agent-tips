# 2026-09-12 Agent Tips

## 今日主题：Agent 的「批量手术」—— 一次性改 10 个文件，不翻车

**一句话总结：别再一个个文件改了。直接跟 Agent 说「把所有接口从 REST 改成 GraphQL」，它会自己规划、逐个文件操作、最后给你一份改动清单。**

---

### 技巧 1：Claude Code 的多文件编辑 —— `/edit` 的隐藏玩法

很多人都知道 `/edit` 可以改单个文件，但其实它能**批量操作**：

```
我：/edit 把 src/components 下所有 .vue 文件里的 
    `defineProps<{ title: string }>()` 
    改成带默认值的 `withDefaults(defineProps<...>(), { title: 'Untitled' })`

Claude：正在扫描 src/components...
  - ✅ Button.vue
  - ✅ Modal.vue  
  - ✅ Card.vue
  - ⚠️  Header.vue 已经有默认值，跳过
  
  完成：改了 3 个文件，跳过 1 个，0 个报错。
```

**关键点**：描述要**具体+可验证**。给 AI 明确的搜索模式和替换模式，它就不会瞎改。

---

### 技巧 2：Cursor Composer —— 可视化批量编辑

Cursor 的 Composer 模式（Cmd/Ctrl + I）是批量改代码的神器：

1. **选中多个文件**扔进去，或者直接在 Composer 里描述需求
2. AI 会**生成一个改动计划**，列出要改哪些文件、每处怎么改
3. 你**Review 完点 Apply**，一键应用到所有文件

```
你：帮我给所有 API 调用加上错误重试逻辑，
    用 exponential backoff，最多重试 3 次

Composer：
  📁 计划改动 5 个文件：
     1. api/user.ts    — 包装 fetchUser
     2. api/order.ts   — 包装 fetchOrder
     3. utils/http.ts  — 新增 retry() 工具函数
     4. ...
  
  [查看 Diff]  [Apply All]  [取消]
```

**比单文件编辑强在哪？** AI 会**自动处理文件间的依赖**——比如上面例子，它知道要先建 `retry()` 工具，再去改调用方。

---

### 技巧 3：批量改动不翻车的「安全三件套」

多文件改动最怕啥？**改到一半发现方向错了，回滚都麻烦。**

上车前系好安全带：

| 步骤 | 操作 | 为什么 |
|---|---|---|
| 1. **先存档** | `git add . && git commit -m "before: xxx refactor"` | 改砸了随时 `git checkout .` |
| 2. **小步快跑** | 一次改 3-5 个文件，别一上来就 20 个 | 方便 review，出错也好定位 |
| 3. **让 AI 自检** | 改完后说「跑下测试，看看有没有坏」 | AI 会自动跑测试、修报错 |

> 💀 血泪教训：曾经一次性让 AI 改了 15 个文件，结果第 14 个有个边缘 case 没处理好，回滚时差点把键盘砸了。

---

### 技巧 4：跨语言/跨框架的「迁移手术」

批量编辑最爽的场景——**技术栈迁移**：

```
你：帮我把项目里所有 CommonJS 的 `require()` 
    改成 ES Module 的 `import`

Agent：
  - 扫描到 42 个 `require`
  - 自动识别哪些可以直转 `import`
  - 标记 3 个动态 require 需要手动处理
  - 生成迁移报告 + 待办清单
```

这种「体力活」交给 Agent，你只需处理它标出来的** edge cases**。

---

### 一句话带走

> **批量编辑的核心不是「让 AI 改得多」，是「让 AI 改得准」。**
>
> 今天试试：找一个你要改 3 个以上文件的活儿，用 Claude Code `/edit` 或 Cursor Composer 描述清楚替换规则，感受下「批量手术」的快感。

---

*Generated on 2026-09-12 for 向上 & 小惠* 🤝
