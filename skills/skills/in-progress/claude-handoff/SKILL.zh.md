---
name: claude-handoff
description: 将当前对话交接给一个全新的后台 agent，让其立即接手工作。
argument-hint: "下一次会话将用于什么？"
disable-model-invocation: true
---

为当前对话编写一份交接总结，以便一个新的 agent 能够继续这项工作。无需将其保存，而是启动一个后台 agent，以该总结作为其 prompt：`claude --bg --name "<descriptive name>" "<handoff summary>"`。它会在当前工作目录中启动并立即返回；用户可以通过 `claude agents` 来管理它。

始终使用 `-n`/`--name` 传递一个具描述性的名称（例如 `--name "Fix login bug"`）；它会在任务列表、会话选择器和终端标题中显示为该名称。

在总结中包含一个 "suggested skills" 部分，列出下一个 agent 应当通过 Skill 工具调用的 skills。

不要重复已经被其他产物（specs、plans、ADRs、issues、commits、diffs）所捕获的内容。改用路径或 URL 来引用它们。

由于总结会成为 agent 的 prompt，请隐去任何敏感信息，例如 API key、密码或个人身份信息。

如果用户传入了参数，请将其视为对下一次会话将聚焦内容的描述，并相应地调整总结。
