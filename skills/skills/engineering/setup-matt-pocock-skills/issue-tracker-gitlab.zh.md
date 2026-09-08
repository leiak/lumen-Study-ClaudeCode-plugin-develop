# Issue tracker: GitLab

此仓库的 issue 和 spec 作为 GitLab issue 存在。所有操作使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。

## 约定

- **创建 issue**：`glab issue create --title "..." --description "..."`。对多行描述使用 heredoc。传递 `--description -` 以打开编辑器。
- **读取 issue**：`glab issue view <number> --comments`。对机器可读输出使用 `-F json`。
- **列出 issue**：`glab issue list -F json` 与适当的 `--label` 过滤器一起使用。
- **评论 issue**：`glab issue note <number> --message "..."`。GitLab 将评论称为 "notes"。
- **应用 / 移除标签**：`glab issue update <number> --label "..."` / `--unlabel "..."`。多个标签可以逗号分隔或重复该标志。
- **关闭**：`glab issue close <number>`。`glab issue close` 不接受关闭评论，因此先用 `glab issue note <number> --message "..."` 解释，然后关闭。
- **Merge requests**：GitLab 将 PR 称为 "merge requests"。使用 `glab mr create`、`glab mr view`、`glab mr note` 等，与 `gh pr ...` 形状相同，但用 `mr` 替换 `pr`，用 `note` / `--message` 替换 `comment` / `--body`。

从 `git remote -v` 推断仓库；在 clone 内运行时，`glab` 会自动执行此操作。

## 将 Merge Request 作为 triage 表面

**MRs as a request surface: no。** _（如果此仓库将外部 merge request 视为功能请求，请设置为 `yes`；`/triage` 读取此标志。）_

当设置为 `yes` 时，MR 通过与 issue 相同的标签和状态运行，使用 `glab mr` 等价物：

- **读取 MR**：`glab mr view <number> --comments` 和 `glab mr diff <number>` 获取 diff。
- **列出用于 triage 的外部 MR**：`glab mr list -F json`，然后只保留作者不是项目成员 / 所有者（即贡献者的 MR，而不是维护者进行中的工作）的 MR。
- **评论 / 标签 / 关闭**：`glab mr note`、`glab mr update --label` / `--unlabel`、`glab mr close`。

与 GitHub 不同，GitLab 分别为 issue 和 MR 编号，因此一旦你知道维护者指的是哪个表面，`#42` 就是明确的。

## 当 skill 说 "publish to the issue tracker"

创建一个 GitLab issue。

## 当 skill 说 "fetch the relevant ticket"

运行 `glab issue view <number> --comments`。

## Wayfinding operations

由 `/wayfinder` 使用。**地图**是一个 issue，**子**issue 作为 ticket。

- **Map**：一个标签为 `wayfinder:map` 的单个 issue，包含 Notes / Decisions-so-far / Fog 主体。`glab issue create --label wayfinder:map`。（在具有原生 epics 的 GitLab tier 上，epic 可以改为容纳地图；标签化的 issue 在任何地方都有效。）
- **Child ticket**：在其描述顶部带 `Part of #<map>` 并标签 `wayfinder:<type>`（`research` / `prototype` / `grilling` / `task`）的 issue。一旦被认领，该 ticket 就会被分派给驱动 dev。
- **Blocking**：GitLab 的**原生 blocking 链接**，即权威、UI 可见的表示。使用 `/blocked_by #<n>` quick action 添加它，作为 note 发布（`glab issue note <child> --message "/blocked_by #<blocker>"`）。原生 blocking 链接是 Premium / Ultimate 功能；在免费 tier（或不可用的地方）回退到描述顶部的 `Blocked by: #<n>, #<n>` 一行。当每个 blocker 都关闭时，ticket 解除阻塞。
- **Frontier 查询**：`glab issue list -F json` 限定为地图的子项，去掉任何带有未结 blocker 的项：指向未结 issue 的原生 `blocked_by` 链接（`glab api projects/:id/issues/:iid/links`），或 `Blocked by` 行中的未结 issue，或受让人；按地图顺序的第一个获胜。
- **Claim**：`glab issue update <n> --assignee @me`，会话的第一次写入。
- **Resolve**：`glab issue note <n> --message "<answer>"`，然后 `glab issue close <n>`，然后将上下文指针（概述 + 链接）追加到地图的 Decisions-so-far。
