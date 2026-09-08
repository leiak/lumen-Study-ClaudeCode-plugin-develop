Skills 按桶文件夹组织，位于 `skills/` 目录下：

- `engineering/`：日常编码工作
- `productivity/`：日常非编码工作流工具
- `misc/`：保留但很少使用，不予推广
- `in-progress/`：测试版：故意公开，欢迎反馈，但尚未随插件发布
- `deprecated/`：已停止使用

位于 `engineering/` 或 `productivity/`（即**已推广**的桶）中的每个 skill，都必须在顶层 `README.md` 中有引用，并在 `.claude-plugin/plugin.json` 的 `skills` 数组中有一条目（Claude Code 插件仅打包已推广的 skill）。`misc/`、`in-progress/` 和 `deprecated/` 中的 skill 不得出现在上述两处。

安装命令直接从 [.agents/install-block.md](./.agents/install-block.md) 复制而来。`.claude-plugin/marketplace.json` 将本仓库自身声明为一个单一插件市场（这是安装说明中提及的备用方案，并非文档化的标准方式）。修改任一清单文件后，请运行 `claude plugin validate . --strict`。关于"为什么采用 Claude 插件但暂不做 Codex 插件"的原因，记录在 [.agents/adr/0002-ship-as-a-claude-code-plugin.md](./.agents/adr/0002-ship-as-a-claude-code-plugin.md) 中。

顶层 `README.md` 中的每个 skill 条目，都必须将 skill 名称链接到其 `SKILL.md`。

每个桶文件夹下都有一个 `README.md`，列出该桶中所有 skill 并附一行简介，skill 名称链接到其对应的 `SKILL.md`。已推广桶的 `README.md` 与顶层 `README.md` 一致，会将条目按**用户调用（User-invoked）** 和 **模型调用（Model-invoked）** 分组；而非推广桶（`misc/`、`in-progress/`）的 `README.md` 则使用平铺列表。

`engineering/` 和 `productivity/` 中的 skill 还会在 `docs/<bucket>/<skill-name>.md` 下拥有一个面向用户的文档页面（docs 目录结构与 `skills/` 下的这两个桶目录保持镜像）。发布后的 URL 始终为 `https://aihero.dev/skills-<skill-name>`，与所在桶无关：docs 路径仅用于仓库内部组织。当你在 `engineering/` 或 `productivity/` 中新增、重命名或修改某个 skill 的行为时，请按照 [.agents/writing-docs.md](./.agents/writing-docs.md) 创建或重新同步其文档页面。一个完成的页面包含四个章节：**它做什么（What it does）**、**何时使用它（When to reach for it）**、**常见问题（Common questions）** 和 **如何判断它正常工作（It's working if）**。`writing-docs.md` 中给出了模板、章节顺序以及常见问题的素材来源。非推广桶（`misc/`、`in-progress/`、`deprecated/`）中的 skill **不**需要文档页面。

每个 `SKILL.md` 要么是用户调用型（在 `agents/openai.yaml` 中设置 `disable-model-invocation: true` 并设置 `policy.allow_implicit_invocation: false`，仅可由用户触发），要么是模型调用型（可由模型或用户触发）。详见 [.agents/invocation.md](./.agents/invocation.md)。

[`ask-matt`](./skills/engineering/ask-matt/SKILL.md) 是一个路由 skill，它将所有用户可触达的 skill 以及它们之间的关系进行了映射。与重新同步文档页面相同的触发条件也适用于它：无论何时你新增、重命名、移除或改变了某个用户可触达 skill 的流程归属，都应重新阅读 `ask-matt` 的 `SKILL.md` 并更新它，以保持映射的准确性：路由中从未提及的新 skill，或仍在路由中指向的过期 skill，都会让这个路由失真。

要将 `deprecated/` 和 `misc/` 之外的所有 skill 重新链接到本地 harness 的 skill 目录（`~/.claude/skills`、`~/.agents/skills`），请运行 `scripts/link-skills.sh`。每个条目都是指向本仓库的软链接，因此 `git pull` 之后安装的 skill 也会保持最新；在新增、移除或重命名 skill 之后请重新运行该脚本。

本仓库的所有散文文本（`SKILL.md` 文件、docs、`README.md`、`CHANGELOG.md`、ADR、changeset、代码注释）中一律不允许出现 em-dash（破折号 `—`）。当句子原本要使用破折号时，请改用逗号、冒号、句号、括号或连词等真正契合语义的标点；切勿盲目地做字符替换。
