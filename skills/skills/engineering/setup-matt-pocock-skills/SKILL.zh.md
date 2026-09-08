---
name: setup-matt-pocock-skills
description: "为此仓库配置工程 skill：设置其 issue tracker、triage 标签词汇和领域文档布局。在首次使用其他工程 skill 之前运行一次。"
disable-model-invocation: true
---

# 设置 Matt Pocock 的 Skills

为工程 skill 假定的每个仓库配置搭建脚手架：

- **Issue tracker**：issue 所在位置（默认 GitHub；开箱即用也支持本地 markdown）
- **Triage labels**：用于五个规范化 triage 角色的字符串
- **Domain docs**：`CONTEXT.md` 和 ADR 所在的位置，以及读取它们的使用者规则

这是一个由提示驱动的 skill，而不是一个确定性脚本。探索、呈现你发现的、与用户确认、然后写入。

## 流程

### 1. 探索

查看当前仓库以了解其初始状态。读取已存在的任何内容；不要假设：

- `git remote -v` 和 `.git/config`：这是一个 GitHub 仓库吗？是哪一个？
- 仓库根目录下的 `AGENTS.md` 和 `CLAUDE.md`：是否存在？两者中是否已经有 `## Agent skills` 章节？
- 仓库根目录下的 `CONTEXT.md` 和 `CONTEXT-MAP.md`
- `docs/adr/` 和任何 `src/*/docs/adr/` 目录
- `docs/agents/`：这个 skill 的先前输出是否已存在？
- `.scratch/`：已经在使用 local-markdown issue tracker 约定的标志
- `triage` skill 是否已安装？（此 skill 旁边有一个 `triage` skill 文件夹，或在你的可用 skill 中有 `triage`。）这决定第 B 节是否运行。
- Monorepo 信号：`pnpm-workspace.yaml`、`package.json` 中的 `workspaces` 字段、或带有自己 `src/` 的填充的 `packages/*`。这些仅在真正的大型多包仓库中存在；它们的存在意味着单 context，这几乎涵盖了每个仓库。

### 2. 呈现发现并询问

总结哪些存在、哪些缺失。然后按顺序处理各个部分。一节、一个答案，然后下一节。

在每节开头给出推荐答案，让用户能用一句话接受。仅当选择确实分支时才给出一行解释；当探索已经解决时跳过该节（第 B 节当 `triage` 未安装时，第 C 节当没有 monorepo 时）。

**第 A 节：Issue tracker。**

> 解释："issue tracker" 是此仓库的 issue 所在位置。诸如 `to-tickets`、`triage` 和 `to-spec` 之类的 skill 从中读取并向其写入。它们需要知道是调用 `gh issue create`、在 `.scratch/` 下写一个 markdown 文件，还是遵循你描述的某个其他工作流。选择你实际上在此仓库跟踪工作的位置。

默认立场：这些 skill 是为 GitHub 设计的。如果 `git remote` 指向 GitHub，就推荐它。如果 `git remote` 指向 GitLab（`gitlab.com` 或自托管主机），就推荐 GitLab。否则（或者如果用户更喜欢），提供：

- **GitHub**：issue 存在于仓库的 GitHub Issues 中（使用 `gh` CLI）
- **GitLab**：issue 存在于仓库的 GitLab Issues 中（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **Local markdown**：issue 以 `.scratch/<feature>/` 下的文件形式存在（适合独立项目或没有远程的仓库）
- **Other**（Jira、Linear 等）：请用户用一段话描述工作流；该 skill 会将其记录为自由格式的散文

将选择记录在 `docs/agents/issue-tracker.md` 中。GitHub 和 GitLab 模板带有一个 "PRs as a request surface" 标志，默认**关闭**。保持关闭并且不要提出：一个希望外部 PR 出现在 triage 队列中的用户可以稍后在文件中翻转该标志。

**第 B 节：Triage 标签词汇。** 如果未安装 `triage` skill（探索告诉了你），则完全跳过本节，因为未安装的 skill 不需要标签。

如果已安装，只问一个问题：

> 你想保留默认的 triage 标签吗？（推荐：**是**）

默认值是五个规范化角色，每个标签字符串等于其名称：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。选择**是**时，按原样写入。仅当用户说否时（通常是因为他们的 tracker 已使用其他名称，例如 `bug:triage` 替代 `needs-triage`），才收集覆盖项，以便 `triage` 应用现有标签而不是创建重复。

**第 C 节：Domain docs。** 默认为**单 context**（仓库根目录下有一个 `CONTEXT.md` + `docs/adr/`）。这几乎适合每个仓库；写入而不询问。

仅当探索发现 monorepo 信号时，才提供**多 context**（根目录下有一个 `CONTEXT-MAP.md` 指向每个 context 的 `CONTEXT.md`）。然后确认他们想要哪种布局。

### 3. 确认并编辑

向用户展示草稿：

- 要添加到 `CLAUDE.md` / `AGENTS.md` 中被编辑的那个文件的 `## Agent skills` 块（选择规则见步骤 4）
- `docs/agents/issue-tracker.md`、`docs/agents/domain.md` 和 `docs/agents/triage-labels.md` 的内容（最后一个仅在已安装 `triage` 时）

让他们在写入之前编辑。

### 4. 写入

**挑选要编辑的文件：**

- 如果 `CLAUDE.md` 存在，编辑它。
- 否则，如果 `AGENTS.md` 存在，编辑它。
- 如果都不存在，问用户要创建哪一个；不要为他们挑选。

永远不要在 `CLAUDE.md` 已经存在时创建 `AGENTS.md`（反之亦然）；始终编辑已存在的那个。

如果所选文件中已存在 `## Agent skills` 块，请原地更新其内容而不是附加重复项。不要覆盖周围章节中的用户编辑。

该块：

```markdown
## Agent skills

### Issue tracker

[跟踪 issue 的位置的简明摘要]。参见 `docs/agents/issue-tracker.md`。

### Triage labels

[标签词汇的简明摘要]。参见 `docs/agents/triage-labels.md`。

### Domain docs

[布局的简明摘要："single-context" 或 "multi-context"]。参见 `docs/agents/domain.md`。
```

仅当已安装 `triage` 且第 B 节运行时，才包含 `### Triage labels` 子块并写入 `docs/agents/triage-labels.md`。未安装时，两者都省略。

然后使用此 skill 文件夹中的种子模板作为起点来编写 docs 文件：

- [issue-tracker-github.md](./issue-tracker-github.md)：GitHub issue tracker
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md)：GitLab issue tracker
- [issue-tracker-local.md](./issue-tracker-local.md)：local-markdown issue tracker
- [triage-labels.md](./triage-labels.md)：标签映射（仅当已安装 `triage` 时）
- [domain.md](./domain.md)：领域文档使用者规则 + 布局

对于 "other" issue tracker，使用用户的描述从头编写 `docs/agents/issue-tracker.md`。

### 5. 完成

告诉用户设置已完成，以及哪些工程 skill 现在将读取这些文件。提到他们以后可以直接编辑 `docs/agents/*.md`；仅当他们想要切换 issue tracker 或从头开始时，才需要重新运行此 skill。
