# 以原生 Claude Code 插件形式发布 skill 集；原生 Codex 插件暂缓

这些 skill 一直可通过 [skills.sh](https://skills.sh/mattpocock/skills) 安装（`npx skills add mattpocock/skills`），它会把可编辑的 skill 文件复制到用户的项目里，覆盖 Claude Code、Codex 以及其他遵循 Agent-Skills 标准的 harness。一个反复出现的需求是 **即插即用** 的分发方式：把这套 skill 当作只读、始终最新的 bundle 订阅，而不是 fork 后自己维护。这正是原生插件系统所提供的。

我们发布一个原生 **Claude Code 插件**，并**暂缓**一个原生 **Codex 插件**。这个拆分是被两个生态系统的插件清单"如何挑选 skill"逼出来的，相对于本仓库的桶式布局。

## 约束：桶式 skill 与单路径选择

Skill 位于 `skills/` 下的桶文件夹里：`engineering/` 与 `productivity/` 是**已推广**的（随插件发布）；`misc/`、`personal/`、`in-progress/` 与 `deprecated/` **不**推广。一个插件只能暴露已推广的那部分，它们横跨两个桶文件夹。

- **Claude Code**：`.claude-plugin/plugin.json` 的 `skills` 接受**一组显式的 skill 目录路径**。我们把已推广的 skill 一一列出，毫无歧义地把其他一切排除，再补一份 `.claude-plugin/marketplace.json`，让本仓库自己成为单一插件市场。端到端验证：`claude plugin validate . --strict` 通过，`marketplace add` → `install` 能解析所有已推广 skill。
- **Codex**：`.codex-plugin/plugin.json` 的 `skills` 只能接受**单个路径字符串**（数组会被 `missing or invalid plugin.json` 拒绝），Codex 会在该路径下递归发现 `SKILL.md`。没有方法从一个路径里指名两个桶文件夹、或挑选一个子集。两种绕路都被测试过并被否决：
  - 指向 `./skills/` 会把 `deprecated/`、`in-progress/`、`personal/` 与 `misc/` 也一起发布：已退役、草稿、与个人用的 skill，这些是我们故意不推广的。
  - 由一组**软链接**指向各桶的"扁平策展目录"在安装时活不下来：Codex 会把插件树复制到缓存里、**丢掉软链接**，于是 skill 到达时是空的。

要让 Codex 拿到"只含已推广 skill"的单一路径，唯一稳妥的办法只有：(a) **重组** `skills/` 让它只包含已推广 skill（把未推广的桶搬走 —— 牵连 `CLAUDE.md`、`scripts/link-skills.sh`、桶的 README，以及依赖 `in-progress/` 与 `personal/` 的本地开发工作流，影响面巨大），或 (b) **提交一份重复副本** —— 把已推广 skill 复制到一个扁平目录里（这带来同步负担与第二份真相之源）。两者都是结构性决策，不能塞进"发布 Claude 插件"一起做。这很可能就是"以前为什么没发插件"的最初那个、记得不清的原因：清单格式无法干净地表达"从一个桶式仓库里挑一个子集"。

## 决定

- 现在发布 **Claude Code 插件**（`.claude-plugin/plugin.json` + `.claude-plugin/marketplace.json`），策展成已推广集合，作为 v1.2 的头条交付物。
- 保留 **skills.sh** 作为通用安装器：它今天已经覆盖 Codex 与其他 harness，因此没有 Codex 用户会被留下无路可装。
- **暂缓**原生 Codex 插件，直到我们在"重组 `skills/` 为只含已推广"与"提交一份生成的扁平副本"之间做出抉择。等 Codex 要么支持 `skills` 数组 / include 列表，要么在安装时保留软链接，再回来重提。

## 这条决定所建立的不变式

- 每份已推广 skill 在 `.claude-plugin/plugin.json` 的 `skills` 数组中都有一条目（这本来就是 `CLAUDE.md` 的规则；现在它也守住了插件的内容）。
- `.claude-plugin/plugin.json` 的 `version` 与 `package.json` 的 version 同步：发版时一起 bump。Claude 用插件的 `version` 决定已安装用户何时看到更新。

## 更新，2026-08-05

`mattpocock-skills` 已被 **Claude Code 官方市场**（配置名 `claude-plugins-official`，源仓库 `anthropics/claude-plugins-official`）接纳，每一份 Claude Code 安装都默认带该市场。`claude plugins install mattpocock-skills` 现在是文档化的路径，上面那条 `marketplace add` → `install` 已被取代。安装措辞住在 [.agents/install-block.md](../install-block.md)。

官方收录指向本仓库的 git URL 并直接读取 `.claude-plugin/plugin.json`，因此它并不依赖 `.claude-plugin/marketplace.json`。后者仅作为"直接安装本仓库"的兜底（未发布的 commit，或 fork）被保留。

2026-08-05 在 Claude Code 2.1.222 上对照实时收录做了验证：

- `claude plugins install mattpocock-skills` 无需预先添加任何市场即可解析，并报告 `mattpocock-skills@claude-plugins-official`。
- `claude plugin details mattpocock-skills` 接着报告版本 1.2.0 并加载已推广 skill。
- 收录的 `source` 是 `{"source": "url", "url": "https://github.com/mattpocock/skills.git", "sha": …}`：**sha 是钉死的**，因此一次发布要等那个 pin 移动之后才能到达已安装用户，而不是我们打 tag 的那一刻。写这段文字时，pin 落后 `main` 两个 commit，这就是它列出 22 条 skill 而非 `plugin.json` 中 24 条的原因。
- 没有实测会话内 `/plugin install mattpocock-skills`：`/plugin` 在 headless（`claude -p`）会话中不可用。它跑的是和 CLI 一样的解析器，文档示例形式是 `/plugin install <name>@claude-plugins-official`。
