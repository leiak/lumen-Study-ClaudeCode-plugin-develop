# 仅对硬依赖给出显式的 `/setup-matt-pocock-skills` 指针

工程类 skill 依赖"每个仓库一份"的配置（issue tracker、triage 标签词汇、领域文档布局），由 `/setup-matt-pocock-skills` 注入。有些 skill 没有这份配置就根本无法工作：它们必须发布到特定的 issue tracker，或者必须应用一个特定的标签字符串。另一些只是用它来打磨输出（词汇、ADR 意识），没有它也能优雅降级。

我们把它们分成 **硬依赖** 与 **软依赖** 两类：

- **硬依赖**（`to-tickets`、`to-spec`、`triage`）：写一句显式提示：_"…应该已经提供给你了；如果没有，请运行 `/setup-matt-pocock-skills`。"_ 没有这份映射，输出是错的，而不只是模糊。
- **软依赖**（`diagnose`、`tdd`、`improve-codebase-architecture`）：仅在模糊散文中提一句"项目的领域词表"和"你正在改动区域的 ADR"。如果这些文档不在，skill 仍能工作，只是输出没那么锋利。

这一划分让软依赖 skill 保持轻量，避免把 setup 指针四处复制到并非承重的地方。
