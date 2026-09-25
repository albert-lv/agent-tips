# 09-25 — 写 20 行 Python，给 Agent 造一只「自定义外挂」

## 🤔 一个高频尴尬

Agent 啥都会，但一到**你的内部系统**就抓瞎：

- "帮我查一下 staging 环境上次部署的版本" —— 它不知道你有 deploy dashboard
- "那个 Jira 单的状态变了吗" —— 它只能去网页上瞎猜
- "把这批数据灌进 BI 系统" —— 它连入口都找不到

不是 Agent 不行，是它**没有你的手**。09-11 我们讲过怎么「用」别人的 MCP 外挂（数据库、浏览器），今天要更进一步——**自己造一个**。

## 🛠️ 技巧一：FastMCP —— 一个函数，就是一件工具

Python 生态里有个 FastMCP，写 MCP server 就像写普通函数，零仪式感的：

```python
from fastmcp import FastMCP
import subprocess

mcp = FastMCP("deploy-tools")

@mcp.tool
def deploy_status(env: str) -> str:
    """查询指定环境的部署版本和状态。env 可选 staging / production"""
    out = subprocess.run(
        ["deploy-cli", "status", "--env", env],
        capture_output=True, text=True
    )
    return out.stdout

@mcp.tool
def rollback(env: str, version: str) -> str:
    """把指定环境回滚到某个版本。危险操作，执行前必须确认"""
    ...

if __name__ == "__main__":
    mcp.run()
```

就这么点。注意一个细节：**docstring 就是 Agent 看到的说明书**——参数含义、可选值、危险边界都写进去，Agent 才知道什么时候该调、怎么调。

注册给 Claude Code 只要一条命令：

```bash
claude mcp add deploy -- python /path/to/deploy_server.py
```

之后对话里直接说："查一下 staging 现在跑的是哪个版本" —— Agent 自己就会发现 `deploy_status` 这件工具，并调用它拿到真实答案。

> 💡 造外挂的黄金标准：**跳过 Agent 本来就会的（读文件、跑 git），只封装它"够不着的东西"**——内部 API、SSO 后面的系统、私有 dashboard、公司自研 CLI。别造 `read_file` 这种轮子，纯浪费。

## 🔌 技巧二：一个 Server，四处插 —— MCP 是开放协议

MCP 最香的一点：**它不是 Claude Code 的私有格式，是开放协议**。同一个 server，各家 Agent 都能插：

```jsonc
// ~/.cursor/mcp.json（或项目级 .cursor/mcp.json）
{
  "mcpServers": {
    "deploy": {
      "command": "python",
      "args": ["/path/to/deploy_server.py"]
    }
  }
}
```

Windsurf、Kimi Code 的 MCP 配置结构基本一样。也就是说：**花一个下午写一个 server，Claude Code、Cursor、Windsurf 全员解锁同一个内部工具**。在公司里推广 Agent 的场景，这就是标准答案——造一次，全员复用，再也不用每个人手把手教"我们部署系统在哪看"。

> ⚠️ 安全提醒：把「写操作」（rollback、发邮件、删数据）和「读操作」分成不同的 server，或者给写操作加确认环节。Claude Code 里还能配合 09-19 讲的 Hooks，对危险命令做自动拦截，双保险。

## 📝 今天记住这个

| 场景 | 解法 |
|---|---|
| Agent 够不着公司内部系统 | FastMCP 把内部 CLI / API 包成 `@mcp.tool`，20 行起步 |
| Agent 不知道工具怎么用 | docstring 写清楚参数和边界，那就是说明书 |
| 全团队都要用同一个工具 | MCP 是开放协议，一个 server 插进多家 Agent |
| 危险操作怕误触 | 读写分离 + Hooks 拦截（见 09-19） |

一句话总结：**别人的外挂是锦上添花，自己造的外挂才是核心竞争力——Agent 的上限，取决于你给它的手有多长。** 🦾
