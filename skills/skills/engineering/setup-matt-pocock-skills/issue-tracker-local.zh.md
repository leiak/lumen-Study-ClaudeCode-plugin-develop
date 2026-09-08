# Issue tracker: Local Markdown

此仓库的 issue 和 spec 作为 markdown 文件存在于 `.scratch/` 中。

## 约定

- 每个 feature 一个目录：`.scratch/<feature-slug>/`
- spec 是 `.scratch/<feature-slug>/spec.md`
- 实现 issue 是每个 ticket 一个文件，位于 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`，从 `01` 编号，绝不是单个合并的 tickets 文件
- triage 状态作为 `Status:` 一行记录在每个 issue 文件顶部附近（角色字符串参见 `triage-labels.md`）
- 评论和对话历史附加到文件底部 `## Comments` 标题之下

## 当 skill 说 "publish to the issue tracker"

在 `.scratch/<feature-slug>/` 下创建一个新文件（如果需要则创建目录）。

## 当 skill 说 "fetch the relevant ticket"

读取引用路径处的文件。用户通常直接传递路径或 issue 编号。

## Wayfinding operations

由 `/wayfinder` 使用。**地图**是一个文件，每个 ticket 一个**子**文件。

- **Map**：`.scratch/<effort>/map.md`（Notes / Decisions-so-far / Fog 主体）。
- **Child ticket**：`.scratch/<effort>/issues/NN-<slug>.md`，从 `01` 编号，正文为问题。`Type:` 一行记录 ticket 类型（`research` / `prototype` / `grilling` / `task`）；`Status:` 一行记录 `claimed` / `resolved`。
- **Blocking**：顶部附近的 `Blocked by: NN, NN` 一行。当它列出的每个文件为 `resolved` 时，ticket 解除阻塞。
- **Frontier**：扫描 `.scratch/<effort>/issues/` 寻找未结、未阻塞且未被认领的文件；按编号排第一的获胜。
- **Claim**：在任何工作之前将 `Status: claimed` 并保存。
- **Resolve**：将答案附加到 `## Answer` 标题下，将 `Status: resolved`，然后将上下文指针（概述 + 链接）追加到 `map.md` 中地图的 Decisions-so-far。
