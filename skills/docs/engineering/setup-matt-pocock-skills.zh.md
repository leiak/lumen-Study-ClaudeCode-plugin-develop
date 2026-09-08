## What it does

`setup-matt-pocock-skills` 回答关于一个仓库的三个问题:issues 住在哪里、triage 标签叫什么、领域文档放在哪里。它把答案写成 `docs/agents/` 下的 Markdown 文件。

那些文件是不同仓库之间唯一变化的东西。技能本身在各处都是相同的;它们在运行时读取 `docs/agents/issue-tracker.md` 并照做。这就是为什么这套不绑定到 GitHub,以及为什么没有任何技能文件需要编辑来把它指向别处。用 "link the skills to a custom issue tracker" 来调用它,可以用任何你能以编程方式接入的东西,完全不需要修改这些技能。

它是一个 prompt 驱动的技能,而不是确定性脚本。它读你的 `git remote`、现有的 `CLAUDE.md`、现有的 `CONTEXT.md`,提议它发现的东西,并在写任何东西之前等你确认。

## When to reach for it

你通过输入 `/setup-matt-pocock-skills` 来调用它;[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自行调用它。它被刻意标记为不可调用,所以其他技能也不能替你触发它。

每个仓库调用一次,在第一次使用任何其他工程技能之前。如果 [triage](https://aihero.dev/skills-triage)、[to-spec](https://aihero.dev/skills-to-spec)、[to-tickets](https://aihero.dev/skills-to-tickets) 或 [wayfinder](https://aihero.dev/skills-wayfinder) 开始猜你的 issue 该去哪里,或应用你的 tracker 里没有的标签,说明这里还没被搭建过。一个已经走到一半的仓库同样适合运行它;该技能读已有的东西,前面的工作不会被浪费。

## Prerequisites

它写进你跑它所在的那个仓库:

| 它写 | 写到哪里 |
| --- | --- |
| `issue-tracker.md` | `docs/agents/` |
| `domain.md` | `docs/agents/` |
| `triage-labels.md` | `docs/agents/`,仅当 `triage` 技能已安装 |
| 一个 `## Agent skills` 块 | 现存的 `CLAUDE.md` / `AGENTS.md` 中的任一个 |

这些都是提交的 Markdown。没有用户级或全局模式:配置活在仓库里,所以每个仓库有自己的一份。

## The three decisions

每一节以推荐答案开头,并跳过已经定下来的探索。多数运行是两次确认就完事。

| 决策 | 它提议什么 | 实际什么时候问 |
| --- | --- | --- |
| **Issue tracker** | 匹配你 `git remote` 的那个 | 总是:这才是真正的选择 |
| **Triage 标签** | 保留五个 canonical 名(`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`) | 仅当 `triage` 技能已安装 |
| **领域文档** | 单上下文:根目录一份 `CONTEXT.md` 加 `docs/adr/` | 仅当它嗅到 monorepo 信号时,然后才提供多上下文的 `CONTEXT-MAP.md` |

Tracker 选项:

| 选项 | Issues 住在哪里 | 需要 |
| --- | --- | --- |
| **GitHub** | 仓库的 GitHub Issues | `gh` CLI |
| **GitLab** | 仓库的 GitLab Issues | `glab` CLI |
| **本地 markdown** | 本仓库 `.scratch/<feature>/` 下的文件 | 任何东西:根本不需要 remote |
| **其他** | 你说的任何地方 | 你用一段话描述这个工作流 |

前三种以模板形式随该技能发布,开箱即用。本地 markdown 是一个一等选项,而非 fallback:一个没有 remote 的单人项目被完全支持。值得复述的一条 caveat:如果你在使用 GitHub,就不要用本地 markdown。它们是替代,不是分层。

"Other" 也不是个 stub。这就是 Jira、Linear、Azure DevOps 和 Beads 全部能用的原因:你描述工作流,技能把你的散文记进 `docs/agents/issue-tracker.md`,下游技能跟着那段散文。社区已经做过这些:一个走 [MCP](https://www.aihero.dev/ai-coding-dictionary/mcp) 的 Jira 变体、一个形状像 `gh` 的 Gitea CLI、一个手写的本地面板。

## Common questions

**我必须用 GitHub 吗?**
不必。GitHub、GitLab 和 `.scratch/` 下的本地 Markdown 都作为现成模板提供,其他一切通过 "other" 路径走。这是记录中被重复问得最多的问题,大致是这些措辞:"hard locked to github"、"can I use GitLab / Jira"、"what about Azure DevOps"。每次的答案都是,tracker 是一个 setup 的答案,而不是技能的属性。

**更新 skills 后我需要重跑吗?**
在 v1.1 之后被直接问到时,Matt 说需要。该技能自己的收尾消息更温和:它告诉你,只有当要切换 tracker 或重头开始时才需要重跑。两边都站得住,而差异背后的原因是真实的:种子模板在不同版本之间变化,所以一份由更老版本写出的 `docs/agents/issue-tracker.md` 可能与正在读它的那些技能脱节。如果一个下游技能开始按文档描述不同的方式做事,重跑就是便宜的修法。

**它写进了 `CLAUDE.md`,但我在 Codex 上。**
已知缺口,仍然 open。文件选择规则是 "如果 `CLAUDE.md` 存在就编辑它,否则编辑 `AGENTS.md`":它检查哪个文件存在,而不是哪个 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 在跑。一个留着 Claude Code 时代 `CLAUDE.md` 的仓库会让它的 `## Agent skills` 块落在一个 Codex 从不读的地方。社区里流传两种变通:手工把那块挪到 `AGENTS.md`,或者让 `AGENTS.md` 作 canonical,并让 `CLAUDE.md` 成为指向它的一行指针。如果两个文件都不存在,该技能会问你要创建哪一个,而不是替自己挑,这让那些以为它会直接决定的人感到困惑。

**它没创建我的 triage 标签。**
它不创建。`docs/agents/triage-labels.md` 是一份 *mapping*:它告诉 `/triage` 你 tracker 里哪些字符串对应那五个 canonical 角色。它不跑 `gh label create`。在一个新的 GitHub 仓库上,标签确实还不存在,这件事已经被当作 bug 报过不止一次。两个跟进:

- 如果你的 tracker 已经在用 canonical 名,这张 mapping 就是一张 identity 表,没什么可配的。那是预期中的常见情况,不是一个缺失的步骤。
- [wayfinder](https://aihero.dev/skills-wayfinder) 的 `wayfinder:map` 和 `wayfinder:<type>` 标签也不在这里创建,而 `gh issue create --label <missing>` 直接失败,而不是创建标签。在 GitHub 仓库上首次跑 wayfinder 之前手工创建它们。

**我能在这里配置其他技能的行为([grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 的节奏、问题格式、语气)吗?**
不能。它配置三件事:tracker、标签、文档布局。已经有过直接请求,要求它成为用户偏好的家,而一贯的回答是,技能保持有意见:"Config is death." 偏好属于你 `CLAUDE.md` 里的简单指令,每个技能都已经会读。

**我能不能把配置放在 `~/.claude` 而不是提交到每个仓库?**
今天还不能。有一个正是为此的 open request,出自一个跨很多仓库跑这些技能的人,目前没有用户级模式。每个仓库自己带一份 `docs/agents/`。

**一个用来配置其他技能的技能,这不奇怪吗?**
一个长期存在的抱怨说是的,用这段话:"having a skill to set up the other skill does not feel right to me: that means the LLM is configuring its own skills." 这个权衡真实且被承认:setup 步骤的替代方案是把 tracker 指令复制进每个触碰 issue 的技能。输出是可读、可编辑的 Markdown,这正是缓解措施:你可以读它写的每一份文件并手工改它,而日常调整正好就该这么做,而不是再跑一次。

## It's working if

- `docs/agents/issue-tracker.md` 和 `docs/agents/domain.md` 存在,加上 `triage-labels.md`(如果 `triage` 已安装)。
- 一段 `## Agent skills` 出现在你的 harness 真正读的那份指令文件里,以一句话总结指向每一份上述文件。
- 它提议的 tracker 匹配你真正在用的 remote,而标签字符串匹配你 tracker 里真正存在的标签。
- 在那之后,`/to-tickets` 发布时不再问你 issue 住哪里,而 `/triage` 应用标签而不是凭空发明。
- 技能文件自身没有任何改动。如果 setup 编辑了一份 `SKILL.md`,那就有地方坏了。

## Where it fits

`setup-matt-pocock-skills` 是工程流程的 **一次性搭建**,是其他所有东西假设的前置条件,而不是链中的一个步骤。它的邻居是它的读者:[triage](https://aihero.dev/skills-triage),它会应用这里写下的标签词汇;[to-spec](https://aihero.dev/skills-to-spec) 和 [to-tickets](https://aihero.dev/skills-to-tickets),它们把内容发到在这里命名的 tracker;以及 [wayfinder](https://aihero.dev/skills-wayfinder),它读同一份 tracker 文件的 "Wayfinding operations" 段,以了解 map、子 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)、阻塞边和 frontier 查询是怎么表达的。它记录的领域文档布局正是 [domain-modeling](https://aihero.dev/skills-domain-modeling) 之后填的那一份:它懒创建 `CONTEXT.md` 和 ADR,在术语或决策真正成形时,所以一个空仓库经过 setup 是预期状态。对于下一步该用哪个技能,[ask-matt](https://aihero.dev/skills-ask-matt) 在整套技能中路由。
