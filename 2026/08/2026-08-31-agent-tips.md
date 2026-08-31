# Agent 每日技巧 · 2026-08-31

## 在项目里埋一张"AI 使用说明书"

不知道你们有没有这种体验——每次新开一个对话，都要跟 AI 解释一遍：

> "我们用 Go 写的"  
> "接口命名要用驼峰"  
> "别用 `fmt.Println`，用我们的 logger"  
> "记住啊，我们是微服务架构，别写成单体了……"

然后下一个对话，从头再来一遍。🫠

### 其实可以偷懒的

大部分 Agent 工具（Cursor、Claude Code、Windsurf、Kimi Code）都支持**项目级提示词文件**——放在项目根目录，AI 每次进项目都会自动读，相当于你给 AI 写了一张"入职须知"。

| 工具 | 文件名 |
|------|--------|
| Cursor | `.cursorrules` |
| Claude Code | `CLAUDE.md` |
| Windsurf | `.windsurfrules` |
| Kimi Code | `.kimi-code.md`（或类似配置）|

### 里面写啥？

不用写小作文， bullet point 就够：

```markdown
# 项目规范速查

- 技术栈: Go 1.22 + Gin + PostgreSQL
- 日志: 统一用 pkg/logger，禁止 fmt.Println
- 错误处理: 必须 wrap，不许裸返回 err
- 接口命名: GetUserByID，不是 get_user_by_id
- 架构: 微服务，每个服务独立仓库
- 测试: 新代码必须带单元测试
- 禁用: 全局变量、init() 里做太多事
```

### 效果有多香？

放之前：
> 你：帮我写个用户查询接口  
> AI：好的，`func get_user_by_id(id int)`……  
> 你：不是，用驼峰……  
> AI：哦，`func GetUserByID(id int)`……  
> 你：还有，id 是 string，uuid 格式……  

放之后：
> 你：帮我写个用户查询接口  
> AI：`func GetUserByID(ctx context.Context, id string) (*User, error)`，已经按你们规范用了 pkg/logger，错误也 wrap 了，测试在 `user_service_test.go` 里。  
> 你：……你怎么知道？  
> AI：你桌上那张纸写了啊。🙂

### 进阶玩法

- 可以写**代码风格示例**：贴一段你们项目中"写得最好"的代码，让 AI 模仿
- 可以写**常见踩坑**："我们之前用 gorilla/mux，现在全面切到 Gin 了，别再用旧的"
- 可以写**架构图文字版**："gateway → user-service / order-service，共用 pkg/ 下的工具库"

一张小纸条，省去 80% 的重复沟通。这大概就是所谓的"基建"吧——**一次写好，长期受益**。

---

*明日预告：聊聊 Agent 的 "context 管理术"——对话越长越笨？几个命令让它恢复清醒。*
