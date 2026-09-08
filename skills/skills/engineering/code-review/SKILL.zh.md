---
name: code-review
description: "沿两个轴对一个固定点（commit、branch、tag 或 merge-base）以来的变更进行 review：Standards（代码是否遵循该仓库文档化的编码规范？）和 Spec（代码是否与发起它的 issue / spec 一致？）。以并行 sub-agent 形式运行两个 review，并排展示它们的报告。在用户希望 review 一个分支、PR、进行中的变更，或要求 'review since X' 时使用。"
---

对 `HEAD` 与用户给定的固定点之间的 diff 进行两轴 review：

- **Standards**：代码是否符合该仓库文档化的编码规范？
- **Spec**：代码是否忠实地实现了发起的 issue / spec？

两个轴都以**并行 sub-agent** 形式运行，这样它们不会污染彼此的上下文，然后这个 skill 汇总它们的发现。

issue tracker 应该已经被提供给你。如果 `docs/agents/issue-tracker.md` 缺失，告诉用户运行 `/setup-matt-pocock-skills`。

## 流程

### 1. 钉住固定点

无论用户说的是什么固定点（commit SHA、branch 名称、tag、`main`、`HEAD~5` 等）。如果他们没有指定，就问。

一次性捕获 diff 命令：`git diff <fixed-point>...HEAD`（三点，因此与 merge-base 进行比较）。也通过 `git log <fixed-point>..HEAD --oneline` 记录 commit 列表。

在继续之前，确认固定点可以解析（`git rev-parse <fixed-point>`）且 diff 非空。一个错误的引用或空的 diff 应该在此处失败，而不是在两个并行 sub-agent 内部失败。

### 2. 识别 spec 来源

按以下顺序寻找发起的 spec：

1. commit 消息中的 issue 引用（`#123`、`Closes #45`、GitLab `!67` 等），通过 `docs/agents/issue-tracker.md` 中的工作流获取。
2. 用户作为参数传入的路径。
3. `docs/`、`specs/` 或 `.scratch/` 下匹配 branch 名称或功能的 spec 文件。
4. 如果什么都没找到，问用户 spec 在哪里。如果他们说没有，**Spec** sub-agent 将跳过并报告 "no spec available"。

### 3. 识别规范来源

仓库中任何记录代码应如何编写的文件，比如 `CODING_STANDARDS.md` 或 `CONTRIBUTING.md`。

在仓库记录的内容之上，Standards 轴始终承载下面的 **smell baseline**：一组固定的 Fowler 代码异味（_Refactoring_, ch.3），即使仓库没有文档也适用。两条规则约束它：

- **仓库优先。** 有据可查的仓库规范始终胜出；在它认可 baseline 会标记的内容时，抑制该 smell。
- **始终是判断题。** 每个 smell 都是一个有标签的启发式（"可能的 Feature Envy"），从来不是硬性违反。像这里任何其他规范一样，跳过任何工具已经强制的内容。

每个 smell 读作 *它是什么* → *如何修复*；将其与 diff 对照：

- **Mysterious Name**：函数、变量或类型的名字没有揭示它的作用或内容。→ 重命名它；如果想不出诚实的名字，那么设计本身就不清晰。
- **Duplicated Code**：相同的逻辑形态出现在变更中的多个 hunk 或文件里。→ 抽出共享形态，从两处调用它。
- **Feature Envy**：一个方法访问另一个对象的数据多于自己的数据。→ 把方法移到它所羡慕的数据上。
- **Data Clumps**：相同的几个字段或参数总是一起出现（一个想要诞生的类型）。→ 把它们捆绑成一个类型，传递它。
- **Primitive Obsession**：用原始类型或字符串来代表一个值得拥有自己类型的领域概念。→ 给这个概念它自己的小类型。
- **Repeated Switches**：相同的 `switch` / `if` 级联在同一类型上反复出现。→ 用多态替换，或两处共享一个映射。
- **Shotgun Surgery**：一个逻辑变更迫使在 diff 中的许多文件里做分散的编辑。→ 把一起变化的内容集中到一个模块中。
- **Divergent Change**：一个文件或模块因为多个不相关的原因而被编辑。→ 拆分，让每个模块只因为一个原因而变化。
- **Speculative Generality**：为 spec 没有的需求添加了抽象、参数或钩子。→ 删除它；内联回去直到出现真正的需求。
- **Message Chains**：调用方不应依赖的 `a.b().c().d()` 长链导航。→ 把这趟走动隐藏在第一个对象的一个方法背后。
- **Middle Man**：一个类或函数主要只是委派给其他对象。→ 删掉它，直接调用真正的目标。
- **Refused Bequest**：一个子类或实现者忽略或覆盖了它继承的大部分内容。→ 去掉继承，改用组合。

### 4. 并行派生两个 sub-agent

**Standards sub-agent 提示**应包括：

- 完整的 diff 命令和 commit 列表。
- 步骤 3 中找到的规范来源文件列表，**外加步骤 3 中完整的 smell baseline**（sub-agent 无法以其他方式访问它）。
- 提示词："按相关文件 / hunk 报告：(a) diff 中违反文档化规范的每个位置：引用规范（文件 + 规则）；以及 (b) 你发现的任何 baseline smell：命名它并引用 hunk。区分硬性违反和判断题：文档化规范的违反可以是硬性，但 baseline smell 始终是判断题，文档化的仓库规范优先于 baseline。跳过任何工具强制的内容。400 字以内。"

**Spec sub-agent 提示**应包括：

- diff 命令和 commit 列表。
- spec 的路径或获取的内容。
- 提示词："报告：(a) spec 要求但缺失或不完整的需求；(b) diff 中未被要求的行为（scope creep）；(c) 看起来已实现但实现看起来不对的需求。对每个发现引用 spec 中的行。400 字以内。"

如果 spec 缺失，跳过 Spec sub-agent 并在最终报告中注明。

### 5. 汇总

将两份报告放在 `## Standards` 和 `## Spec` 标题下，原样或稍作清理。**不要**合并或重排发现，因为两个轴是刻意分开的（参见 _Why two axes_）。

最后用一行总结：每个轴的发现总数，以及该轴内最严重的问题（如果有）。不要在轴之间挑一个赢家：那就是分离要防止的重排。

## 为什么是两个轴

一项变更可以在一轴上通过而在另一轴上失败：

- 遵循每条规范但实现了错误的东西的代码 → **Standards pass，Spec fail。**
- 准确地完成 issue 要求但破坏了项目约定的代码 → **Spec pass，Standards fail。**

分别报告它们可防止一轴掩盖另一轴。
