# Productivity（生产力）

通用工作流工具，与编码无关。

## 用户调用（User-invoked）

只有当你键入它们时才可达（Claude Code：`disable-model-invocation: true`；Codex：在 `agents/openai.yaml` 中设置 `policy.allow_implicit_invocation: false`）。

- **[grill-me](./grill-me/SKILL.md)**：针对计划或设计进行不依不饶的访谈，直到设计树的每个分支都被解决。
- **[handoff](./handoff/SKILL.md)**：把当前对话压缩成一份交接文档，以便另一个 agent 接手继续。
- **[teach](./teach/SKILL.md)**：跨多个会话教会用户一项新技能或新概念，使用当前目录作为有状态的教学工作区。
- **[to-questionnaire](./to-questionnaire/SKILL.md)**：把"你独自回答不了"的决策，变成一份 Markdown 问卷交给那个能回答的人，可异步填写，也可以一起开会时填。
- **[wait-what](./wait-what/SKILL.md)**：在对方一句话没接住的瞬间触发。Agent 会用你缺失的上下文、用通俗英语重新讲一遍，并使用你 `CONTEXT.md` 里的词汇。

## 模型调用（Model-invoked）

模型或人都可触达（描述里保留丰富的触发措辞，让模型能自动抓取）。

- **[grilling](./grilling/SKILL.md)**：针对计划、决策或想法对用户进行不依不饶的访谈，直到设计树的每个分支都被解决。
- **[writing-for-agents](./writing-for-agents/SKILL.md)**：如何为 agent 写文档：skills、AGENTS.md/CLAUDE.md，以及任何 agent 通过指针取到的文档。
