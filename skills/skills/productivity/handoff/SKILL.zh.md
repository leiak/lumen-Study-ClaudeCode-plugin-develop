---
name: handoff
description: 将当前会话压缩成一份交接文档,以便另一个 agent 接手。
argument-hint: "下一次会话将用于什么?"
disable-model-invocation: true
---

撰写一份交接文档,总结当前对话,以便一个新启动的 agent 能够继续工作。保存到用户操作系统的临时目录,而非当前工作目录。

文档中应包含一个 "suggested skills" 章节,列出下一个 agent 应当通过 Skill 工具调用的技能。

不要复制已经被其他产物(规格、计划、ADR、issue、commit、diff)记录的内容。改为通过路径或 URL 引用它们。

脱敏所有敏感信息,例如 API key、密码或个人身份信息。

如果用户传入了参数,请将其视为下一次会话重点内容的描述,并据此调整文档内容。
