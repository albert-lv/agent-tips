# 🎯 Agent Tips 2026-08-12

## 今日技巧：Claude Code 的 `/compact` 命令 —— 别让上下文把你憋死

用过 Claude Code 的都知道，一个 session 聊久了，上下文越来越膨胀，响应越来越慢，最后直接 "context limit reached" 给你一巴掌。

**解决方案：`/compact`**

这是 Claude Code 内置的上下文压缩命令。执行后，它会把你和它的整个对话历史总结成一份精简的摘要，然后把原始消息清掉，腾出空间继续聊。

### 怎么用？

直接在 Claude Code 里敲：

```
/compact
```

它会问你要不要压缩，确认后：
- ✅ 保留核心结论和代码片段
- ✅ 保留文件修改记录
- ✅ 丢弃中间推理过程和已解决的错误堆栈
- ✅ 上下文窗口瞬间清爽

### 什么时候用？

- 调试了 20 轮终于 fix 了一个 bug → `/compact` 一下，总结成功经验
- 讨论了半天的架构设计定型了 → `/compact`，把结论固化
- 感觉 Claude 开始 "失忆" 或响应变慢 → 立刻压缩，重获新生

### Pro Tip 💡

压缩前可以手动保存关键代码块到文件，`/compact` 后会保留文件引用关系，但具体的 inline code 可能会被简化。重要代码先 `cat > file.py` 存好再压缩。

---

*明日预告：Cursor Agent 模式的 "Yolo Mode" 到底能有多勇？*
