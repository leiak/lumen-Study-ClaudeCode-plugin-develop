---
name: improve-codebase-architecture
description: 扫描代码库以寻找加深机会，将其呈现为可视化 HTML 报告，然后对你挑选的那个进行 grilling。
disable-model-invocation: true
---

# 改进代码库架构

暴露架构摩擦，并提出**加深机会**：将浅层模块转变为 deep module 的重构。目标是可测试性和 AI 可导航性。

这个命令由项目的领域模型提供信息，并建立在共享的设计词汇之上：

- 使用 Skill 工具调用 "codebase-design" 以获取架构词汇（**module**、**interface**、**depth**、**seam**、**adapter**、**leverage**、**locality**）及其原则（删除测试、"接口就是测试面"、"一个 adapter = 假设 seam，两个 = 真实"）。在每个建议中精确使用这些术语，并且不要漂移到 "component"、"service"、"API" 或 "boundary"。
- `CONTEXT.md` 中的领域语言为好的 seam 命名；`docs/adr/` 中的 ADR 记录该命令不应重新审视的决策。

## 流程

### 1. 探索

**扫描之前先界定范围：YAGNI。** 加深一个模块的回报是让它将来的变更更容易，所以把额外权重放在代码库最近更改过的部分。在你观察之前决定*看哪里*：

- 如果用户指明了一个方向（一个模块、一个子系统、一个痛点），就采用它，并跳过下面的推断。
- 否则，回溯一段良好的 commit 历史（`git log --oneline`）以找到代码库的热点，那些反复出现的文件和区域，让这些路径首先吸引你的注意力。如果变更分散且没有明确的热点，则扩大搜索范围。

首先阅读项目的领域词汇表（`CONTEXT.md`）以及你正在触及区域的任何 ADR。

然后派生一个 sub-agent 来走查代码库。不要遵循僵化的启发式；有组织地探索并注意你在哪里遇到摩擦：

- 在哪里理解一个概念需要在许多小模块之间反复跳转？
- 在哪里模块是**浅层**的，接口几乎和实现一样复杂？
- 在哪里为了可测试性抽取了纯函数，但真正的 bug 隐藏在它们如何被调用中（没有**locality**）？
- 在哪里紧耦合的模块跨其 seam 泄漏？
- 代码库的哪些部分未经测试，或很难通过当前接口进行测试？

对任何你怀疑是浅层的东西应用**删除测试**：删除它会集中复杂度，还是只是移动它？"是的，集中" 是你想要的信号。

### 2. 将候选呈现为 HTML 报告

将一个自包含的 HTML 文件写入 OS 临时目录，这样仓库里不会留下任何东西。从 `$TMPDIR` 解析临时目录，回退到 `/tmp`（或在 Windows 上 `%TEMP%`），并写入 `<tmpdir>/architecture-review-<timestamp>.html` 以便每次运行都得到一个新文件。为用户打开它（Linux 上 `xdg-open <path>`，macOS 上 `open <path>`，Windows 上 `start <path>`）并告诉他们绝对路径。

该报告使用 **Tailwind via CDN** 进行布局和样式，并在关系图 / 流程图 / 序列图可靠地传达结构时使用 **Mermaid via CDN**。将 Mermaid 与手工制作的 CSS / SVG 视觉混合：当关系是图状（调用图、依赖、序列）时使用 Mermaid，当你想要更具编辑性的东西（质量图、横截面、折叠动画）时使用手工构建的 div / SVG。每个候选都获得一个**before / after 可视化**。要可视化。

对于每个候选，渲染一个包含以下内容的卡片：

- **Files**：涉及哪些文件 / 模块
- **Problem**：当前架构为什么引起摩擦
- **Solution**：将要发生什么的简明英语描述
- **Benefits**：以 locality 和 leverage 解释，以及测试将如何改进
- **Before / After diagram**：并排的、自定义绘制的，阐释浅层性和加深
- **Recommendation strength**：`Strong`、`Worth exploring`、`Speculative` 之一，作为徽章渲染

报告末尾是一个 **Top recommendation** 部分：你首先要处理哪个候选以及为什么。

**对领域使用 CONTEXT.md 词汇，对架构使用 `/codebase-design` 词汇。** 如果 `CONTEXT.md` 定义了 "Order"，请谈 "the Order intake module"，而不是 "the FooBarHandler"，也不是 "the Order service"。

**ADR 冲突**：如果某个候选与现有 ADR 相矛盾，仅在摩擦真实到足以值得重新审视该 ADR 时才浮现它。在卡片中清楚地标记它（例如一个警告框：_"contradicts ADR-0007, but worth reopening because…"_）。不要列出 ADR 禁止的每个理论重构。

参见 [HTML-REPORT.md](HTML-REPORT.md) 以获得完整的 HTML 脚手架、图表模式和样式指导。

不要提出接口。文件写入后，问用户："你希望探索这些中的哪一个？"

### 3. Grilling 循环

一旦用户挑选了一个候选，使用 Skill 工具调用 "grilling" 来与他们一起走决策树：约束、依赖、深化模块的形状、seam 背后是什么、哪些测试存活。

副作用在决策具体化时内联发生；使用 Skill 工具调用 "domain-modeling" 以在你推进时保持领域模型为最新：

- **以 `CONTEXT.md` 中不存在的概念命名一个加深后的模块？** 将该术语添加到 `CONTEXT.md`。如果它不存在则懒创建该文件。
- **在对话中打磨一个模糊的术语？** 立即更新 `CONTEXT.md`。
- **用户以承重的原因拒绝该候选？** 提供一个 ADR，措辞为：_"Want me to record this as an ADR so future architecture reviews don't re-suggest it?"_ 仅在该原因实际上会被未来的探索者需要以避免重新建议同一件事时提供；跳过短暂的原因（"现在不值"）和显而易见的原因。
- **希望为深化模块探索替代接口？** 使用 Skill 工具调用 "codebase-design" 并使用其 design-it-twice 并行 sub-agent 模式。
