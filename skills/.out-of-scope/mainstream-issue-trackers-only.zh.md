# Issue tracker 集成仅限主流工具

`setup-matt-pocock-skills` 仅对**主流** issue tracker 提供一等支持。要求新增对小众、新型、或单一厂商实验性 tracker 的支持属于范围外。

## 为什么不属于范围

每一个 issue-tracker 后端都会把 CLI 形态硬编码到 skill 里（命令、标志、输出解析）。每多一个后端就是一份永久的维护开销 —— 因为它必须随着该工具 CLI 的演进而继续工作，也必须继续接受 `/to-spec`、`/to-tickets`、`/triage` 等的测试。这份开销只在"有意义比例的用户真的在用"时才值得承担。

"主流"是一项判断，而不是一条数值门槛：

- GitHub、GitLab、Backlog.md 算得上主流：广为人知、被广泛使用、早已过了实验阶段。
- 一个面向 agent 的、全新的、仅有几百颗 GitHub star 的工具，哪怕设计再有新意，也算不上。

Star 数、年龄、下载量在判断时是有用的信号，但都不是规则本身。规则是：一位典型工程师看到这个名字，是否认得、并很可能已经为自己的团队选用过它？

针对非主流 tracker 的逃生口其实已经有了：

- `local markdown`，用于轻量的仓库内追踪。
- `other/custom`，用于希望自行接入一份东西的用户。

这两条都不需要核心 skill 知道具体那个工具。

## 以往的请求

- #99："Add dex as an issue tracker backend"（请求时 dex 才约 3 个月大、约 300 颗 star）
