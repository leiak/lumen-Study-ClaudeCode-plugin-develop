---
name: to-tickets
description: 把计划、spec 或当前对话拆成一组"示踪弹"工单，每张工单声明自己的阻塞边，并发布到已配置的 tracker（本地是每张工单一文件的文字形式边；真实 tracker 上是原生阻塞链接）。
disable-model-invocation: true
---

# To Tickets

把计划、spec 或对话拆成一组 **tickets**：示踪弹式的垂直切片，每张声明阻塞它的工单。

issue tracker 与 triage 标签词汇应当已经提供给你。如果没有，告诉用户去运行 `/setup-matt-pocock-skills`。

## 流程

### 1. 收集上下文

直接从已经在对话上下文里的东西出发。如果用户在参数里给了一个引用（spec 路径、issue 编号或 URL），把它取回来、读完整正文与评论。

### 2. 探索代码库（可选）

如果你还没探索过代码库，请先去做，以理解代码的当前状态。工单标题与描述应当使用项目的领域词表词汇，并尊重你正在改动区域的 ADR。

寻找机会，对代码做预先的重构，使实现更容易。"先让变更变容易，再做容易的变更。"

### 3. 起草垂直切片

把工作拆成 **tracer bullet** 工单。

<vertical-slice-rules>

- 每一片切出一条窄但**完整**的路径，穿过每一层（schema、API、UI、测试）：是垂直的，不是某一层的水平切片
- 完成的一片本身即可演示或验证
- 每一片大小都适合放进一个全新的上下文窗口
- 任何预先重构应当先做

</vertical-slice-rules>

为每张工单指定它的 **blocking edges**：必须先完成、才能开始它的其他工单。一张没有阻塞的工单可以立刻开始。

**大型重构是垂直切片的例外。** **大型重构** 是一类纯机械的改动（重命名一列、给一个共享符号重新定型），它的**爆炸半径**横跨整个代码库，因此一次编辑会瞬间炸出上千个调用点，没有任何垂直切片能独立保持绿。不要硬塞进示踪弹；按 **expand–contract（扩展-收缩）** 排开。先扩展：在旧形态旁边新增形态，于是没有什么会破。然后按"爆炸半径"分批迁移调用点（按 package、按目录），每一批一张工单、被扩展那张阻塞，每批之间 CI 一直保持绿，因为旧形态还在。最后收缩：当没有任何调用者之后，把旧形态删掉，被每一批迁移工单阻塞。当各批甚至无法独自保持绿时，就保留这套顺序、让它们共享一条集成分支，所有都阻塞一张最终的"整合并验证"工单；只有在那里承诺变绿。

### 4. 考考用户

把拟定的拆分按编号列表呈现给用户。对每张工单，展示：

- **Title**：简短的描述性名称
- **Blocked by**：必须先完成的其他工单（如有）
- **What it delivers**：这张工单让哪条端到端行为跑通

询问用户：

- 粒度感觉对吗？（太粗 / 太细）
- 阻塞边是否正确：每张工单是否只依赖真正卡住它的工单？
- 是否要把任何工单合并或进一步拆分？

迭代，直到用户认可这份拆分。

### 5. 把工单发布到已配置的 tracker

发布已获认可的工单。**怎么做**取决于 `/setup-matt-pocock-skills` 配置的 tracker；工单本身不变，只有阻塞边的形态会变：

- **本地文件** → 在 `.scratch/<feature-slug>/issues/<NN>-<slug>.md` 下每张工单一个文件，按依赖顺序（阻塞在前）从 `01` 起编号。每个文件的 "Blocked by" 列出它依赖的编号 / 标题。使用下面的"每工单文件"模板：一张工单一个文件，绝不是合并到同一份文件。
- **真实 issue tracker（GitHub、Linear、…）** → 按依赖顺序（阻塞在前）一张工单一期 issue 地发布，使每张工单的阻塞边能引用真实的标识符。如果平台有原生阻塞 / 子 issue 关系，就用它；否则把每张工单的 "Blocked by" 设成那些阻塞 issue。除非另有指示，否则打上 `ready-for-agent` triage 标签；这些工单构造出来就是可被 agent 领取的。

沿着 **frontier** 工作：任何"阻塞都已完成"的工单。对纯线性链，那就是从上到下。

不要关闭或修改任何父 issue。

<local-ticket-template>

# <NN>: <Ticket title>

**What to build:** the end-to-end behaviour this ticket makes work, from the user's perspective, not a layer-by-layer implementation list.

**Blocked by:** the numbers/titles of the tickets that gate this one, or "None (can start immediately)".

**Status:** ready-for-agent

- [ ] Acceptance criterion 1
- [ ] Acceptance criterion 2

</local-ticket-template>

<issue-template>

## Parent

A reference to the parent issue on the tracker (if the source was an existing issue, otherwise omit this section).

## What to build

The end-to-end behaviour this ticket makes work, from the user's perspective, not layer-by-layer implementation.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2

## Blocked by

- A reference to each blocking ticket, or "None (can start immediately)".

</issue-template>

无论哪种形式，都要避免具体的文件路径或代码片段：它们很快就会过时。例外：如果原型产出了一段代码片段，它把某项决策编码得比散文更精确（state machine、reducer、schema、type shape），则把它内联到相关决策中，并简短注明它来自一份原型。剪到"富含决策"的部分，而不是一份可工作的 demo，只要那些关键的比特。
