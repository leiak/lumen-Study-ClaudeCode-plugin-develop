# 不为 `setup-matt-pocock-skills` 添加 verify/check 模式

本项目不会为 `setup-matt-pocock-skills` 添加专门的 verify/check 模式（也不会单出一份 verify skill）。

## 为什么不属于范围

为"检查 `docs/agents/*.md` 产物是否仍匹配种子模板 schema"这件事再加一份 skill（或加一个 `--verify` 标志），等于把现有 setup skill 在对话里已经能做的事再重复一遍。

预期的工作流是：**运行 `/setup-matt-pocock-skills`，并告诉它去 verify 当前 setup。**该 skill 由 prompt 驱动，因此维护者可以把它限定在一次 verify 流程（"别重写任何东西，只把我现有的文件对照当前种子模板检查一下、报告 drift"），而无须额外的代码路径。加一个标志或兄弟 skill 只会把一项已经能用自然语言入口表达的功能的表面切碎。

让"配置管理"只活在同一份 skill 里，也免去了"两份 skill 因种子模板演化而漂移"的维护成本。

## 以往的请求

- #106：Feature request: verify/check mode for setup-matt-pocock-skills
