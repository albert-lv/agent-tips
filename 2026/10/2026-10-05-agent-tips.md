# 10-05 — Agent 的「搬家术」+「裸奔排障法」：换工具不心虚，出 bug 不抓瞎

## 🤔 周一早上两种经典心态

第一种：周末被安利了 Kimi Code，兴冲冲装完，看着空荡荡的配置突然萎了——
我在 Claude Code 里攒了三个月的 `CLAUDE.md` 家规、自定义 Skills、十几个 MCP 配置……**难道要从头再抄一遍？**

第二种：Claude Code 升级完，突然开始抽风——以前好用的命令报错、工具加载失败、回复风格突变。你盯着满屏配置陷入沉思：**到底哪个插件/Hook 在作妖？**

今天两个技巧，一个负责**无痛搬家**，一个负责**裸奔抓凶**。🚚🕵️

## 🛠️ 技巧一：Kimi Code `/import-from-cc-codex` —— 三个月家规，一条命令搬完

Kimi Code 有个特别懂人性的命令（v0.13.0 起内置）：

```
/import-from-cc-codex
```

作用一句话：**从 Claude Code 和 Codex 里，把你指定的指令文件、Skills、MCP 设置一口气搬进 Kimi Code**。

### 搬什么？

- **指令/家规**：`CLAUDE.md` 里的项目规范、个人偏好
- **Skills**：`.claude/skills/` 下你写好的「肌肉记忆」
- **MCP 配置**：那些辛苦调好的外部工具连接

### 为什么这个命令值得吹

以前换工具 = 大型手工迁移现场，配置文件互相看不懂、路径对不上、格式还要手动转。现在这个命令把「搬家」干成了「搬家公司」——你**勾选**要哪些，它负责格式转换和落位。

> 💡 反过来也一样香：很多团队是「Claude Code 主力 + Kimi Code 备用」，家规只维护一份，两边都用 `/import` 同步，再也不用当「人肉配置同步器」。

## 🛠️ 技巧二：Claude Code `--safe-mode` —— 配置打架？一键裸奔揪元凶

场景：升级之后 Agent 行为诡异，你怀疑是某个 plugin / hook / MCP server 在捣乱，但配置一大堆，总不能全删了吧？

2.1.170 版本给了一个排障核弹：

```bash
claude --safe-mode
# 或者环境变量
CLAUDE_CODE_SAFE_MODE=1 claude
```

### 它干了什么？

启动一个「无菌环境」——**临时禁用 CLAUDE.md、plugins、skills、hooks、MCP 等所有自定义项**，只剩出厂状态的 Claude Code。

### 排障三连

1. `--safe-mode` 下问题消失 → 实锤是某个自定义项在作妖
2. 逐个启用（先开 CLAUDE.md，再开 plugins……）→ 二分法快速定位
3. 找到元凶，修它 / 删它 / 去 GitHub 骂它（bushi）

### 什么时候该想起它

- 升级后行为突变、疑似回归
- 新装的 plugin 之后开始报诡异错误
- 想给别人演示「干净版」的 Claude Code
- CI 里想排除本地配置干扰，跑一个纯纯的原厂 Agent

> 💡 还有个配套小姿势：`/cd <path>`（v2.1.170+）可以在**不打断 prompt cache** 的情况下切换工作目录——跨目录干活时 cache 不炸、账单不跳，safe-mode 排障时切来切去也不心疼。

## 📌 今天记住这个

| 场景 | 一句话操作 |
|---|---|
| Claude Code → Kimi Code 搬家 | `/import-from-cc-codex`，勾选要搬的，完事 |
| 两边都想用同一套家规 | 只维护一份，定期互相 `/import` |
| 升级后 Agent 抽风 | `claude --safe-mode` 裸奔试一次 |
| 锁定元凶 | 裸奔正常 → 逐项启用，二分定位 |
| 跨目录干活不想丢 cache | `/cd <path>`，无缝切换 |

> 周一适合干两件事：把家安顿好，把病因揪出来。搬家不心虚，排障不抓瞎，这一周就顺了。🧘
