# 2026-08-29 Agent Tips：别让 AI 在垃圾文件里游泳

等下，你的 AI agent 最近是不是有点"脑内十开页"内味儿了？💀

明明只让它改个函数，它非要先读一遍 `node_modules` 里的 CHANGELOG，再帮你分析一下 `.git` 的提交历史。最后 token 爆了，任务还没干完。

今天分享两招，专治这种**"文件夹黑洞"**和**"上来就写综合征"**。

---

## 1. 给你的 agent 配个"赛博过滤器"

Cursor 有 `.cursorignore`，Claude Code 也会尊重项目里的 ignore 规则。这玩意儿就是告诉 agent：**"这些文件夹，别碰，别看，别读。"**

在项目根目录丢一个，把垃圾文件筛掉：

```text
node_modules/
.git/
dist/
build/
.openclaw/
*.log
```

效果立竿见影：agent 不再把你的依赖库当圣经读，速度变快，幻觉减少，token 账单也更亲切了。🤝

---

## 2. "先计划，后动手" 咒语

很多人跟 agent 说话像喊外卖："给我把这个 bug 修了！" 然后 agent 开启热血码农模式，一顿输出，改完发现引入三个新 bug。

下次试试这句：

> "请先分析问题并给出修改计划，**等我确认后再执行代码修改**。"

英文版：

> "Analyze the issue and draft a plan. Do not write any code until I approve."

这能把 agent 从"手比脑子快"切回"靠谱架构师模式"。毕竟，方向错了，码得越快，回滚越惨。

---

两招拿下，让你的 agent 从"无头苍蝇"变回正经赛博助理。明天见 👋
