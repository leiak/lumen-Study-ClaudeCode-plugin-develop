# Issue tracker: GitHub

此仓库的 issue 和 spec 作为 GitHub issue 存在。所有操作使用 `gh` CLI。

## 约定

- **创建 issue**：`gh issue create --title "..." --body "..."`。对多行正文使用 heredoc。
- **读取 issue**：`gh issue view <number> --comments`，通过 `jq` 过滤评论并获取标签。
- **列出 issue**：`gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'` 与适当的 `--label` 和 `--state` 过滤器一起使用。
- **评论 issue**：`gh issue comment <number> --body "..."`
- **应用 / 移除标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭**：`gh issue close <number> --comment "..."`

从 `git remote -v` 推断仓库；在 clone 内运行时，`gh` 会自动执行此操作。

## 将 Pull Request 作为 triage 表面

**PRs as a request surface: no。** _（如果此仓库将外部 PR 视为功能请求，请设置为 `yes`；`/triage` 读取此标志。）_

当设置为 `yes` 时，PR 通过与 issue 相同的标签和状态运行，使用 `gh pr` 等价物：

- **读取 PR**：`gh pr view <number> --comments` 和 `gh pr diff <number>` 获取 diff。
- **列出用于 triage 的外部 PR**：`gh pr list --state open --json number,title,body,labels,author,authorAssociation,comments` 然后只保留 `authorAssociation` 为 `CONTRIBUTOR`、`FIRST_TIME_CONTRIBUTOR` 或 `NONE` 的项（去掉 `OWNER` / `MEMBER` / `COLLABORATOR`）。
- **评论 / 标签 / 关闭**：`gh pr comment`、`gh pr edit --add-label` / `--remove-label`、`gh pr close`。

GitHub 在 issue 和 PR 之间共享一个编号空间，因此一个裸露的 `#42` 可能是其中之一：先用 `gh pr view 42` 解析，回退到 `gh issue view 42`。

## 当 skill 说 "publish to the issue tracker"

创建一个 GitHub issue。

## 当 skill 说 "fetch the relevant ticket"

运行 `gh issue view <number> --comments`。

## Wayfinding operations

由 `/wayfinder` 使用。**地图**是一个 issue，**子**issue 作为 ticket。

- **Map**：一个标签为 `wayfinder:map` 的单个 issue，包含 Notes / Decisions-so-far / Fog 主体。`gh issue create --label wayfinder:map`。
- **Child ticket**：作为 GitHub sub-issue 链接到地图的 issue（在 sub-issues 端点上使用 `gh api`）。在未启用 sub-issues 的地方，将子项添加到地图主体中的任务列表中，并将 `Part of #<map>` 放在子主体顶部。标签：`wayfinder:<type>`（`research` / `prototype` / `grilling` / `task`）。一旦被认领，该 ticket 就会被分派给驱动 dev。
- **Blocking**：GitHub 的**原生 issue 依赖关系**，即权威、UI 可见的表示。使用 `gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>` 添加边，其中 `<blocker-db-id>` 是 blocker 的数字**数据库 id**（`gh api repos/<owner>/<repo>/issues/<n> --jq .id`，*不是* `#number` 或 `node_id`）。GitHub 报告 `issue_dependencies_summary.blocked_by`（仅未结的 blocker，活动门控）。在依赖关系不可用时，回退到子主体顶部的 `Blocked by: #<n>, #<n>` 一行。当每个 blocker 都关闭时，ticket 解除阻塞。
- **Frontier 查询**：列出地图的未结子项（`gh issue list --state open`，限定为地图的 sub-issues / 任务列表），去掉任何带有未结 blocker（`issue_dependencies_summary.blocked_by > 0`，或 `Blocked by` 行中的未结 issue）或受让人的项；按地图顺序的第一个获胜。
- **Claim**：`gh issue edit <n> --add-assignee @me`，会话的第一次写入。
- **Resolve**：`gh issue comment <n> --body "<answer>"`，然后 `gh issue close <n>`，然后将上下文指针（概述 + 链接）追加到地图的 Decisions-so-far。
