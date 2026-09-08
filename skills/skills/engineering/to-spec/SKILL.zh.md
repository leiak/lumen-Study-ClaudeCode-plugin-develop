---
name: to-spec
description: "把当前对话整理成 spec，并发布到项目 issue tracker：不访谈，只把你已经讨论过的内容合成出来。"
disable-model-invocation: true
---

这个 skill 把当前对话上下文和代码库理解整合为一份 spec。不要访谈用户；只把你已经知道的合成出来。

issue tracker 与 triage 标签词汇应当已经提供给你。如果没有，告诉用户去运行 `/setup-matt-pocock-skills`。

## 流程

1. 探索仓库以理解代码库的当前状态，如果你还没做的话。在整份 spec 中使用项目的领域词表，并尊重你正在改动区域的任何 ADR。

2. 草拟你打算在哪些接缝上测试这个特性。优先使用已有的接缝，而不是新增。使用能用的最高接缝。如果需要新接缝，请在你能提的最高点提议。代码库中的接缝越少越好 —— 理想数量是 1。

   与用户核对：这些接缝符合他们的预期吗。

3. 用下面的模板写 spec，然后发布到项目 issue tracker。打上 `ready-for-agent` 的 triage 标签 —— 无需再做额外的 triage。

<spec-template>

## Problem Statement

用户面对的问题，从用户的视角。

## Solution

对问题的解决方案，从用户的视角。

## User Stories

一份很长的、带编号的用户故事列表。每条用户故事应当采用下面的格式：

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

这份用户故事列表应当极为详尽，覆盖这个特性的所有方面。

## Implementation Decisions

一条已做出的实现决策清单。可以包括：

- 将要新建/修改的模块
- 这些模块将被修改的接口
- 开发者给出的技术澄清
- 架构决策
- schema 变更
- API 契约
- 具体的交互

不要包含具体的文件路径或代码片段。它们可能很快就会过时。

例外：如果原型产出了一段代码片段，它把某项决策编码得比散文更精确（state machine、reducer、schema、type shape），则把它内联到相关决策中，并简短注明它来自一份原型。剪到"富含决策"的部分，而不是一份可工作的 demo，只要那些关键的比特。

## Testing Decisions

一条已做出的测试决策清单。包括：

- 什么是好测试的描述（只测外部行为，不测实现细节）
- 哪些模块会被测试
- 测试的现成范式（即代码库中类似的测试）

## Out of Scope

对这份 spec 而言，范围之外的事项的描述。

## Further Notes

关于这份特性的任何进一步笔记。

</spec-template>
