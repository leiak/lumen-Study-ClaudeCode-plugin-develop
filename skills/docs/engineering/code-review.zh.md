## What it does

`code-review` 沿着两个轴审查 `HEAD` 与你指定的一个固定点(一个 commit、一个分支、一个 tag、`main`、`HEAD~5`)之间的差异。**Standards** 检查代码是否遵循本仓库的写法。**Spec** 检查代码是否做了发起它的 issue 或 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) 所要求的事。每个轴跑在各自的 [sub-agent](https://www.aihero.dev/ai-coding-dictionary/subagent) 里,所以两者互不可见对方的推理。

这两个轴从不被合并,也从不被重新排序。报告以 *每个轴* 最严重的问题收尾,并拒绝在两个轴之间命名单一赢家,因为一段改动可能在一个轴上通过、在另一个轴上失败:一段遵循所有规范却实现了错误事情的代码通过 Standards 但在 Spec 上失败;一段完全按 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket) 去做却打破仓库规范的代码反过来。一个混合的判定会掩盖失败的轴。

## When to reach for it

输入 `/code-review`,或者当你要审查一个分支、PR、进行中的工作、或任何 "since X" 时,agent 会自动调用它。

| 你的处境 | 使用 |
| --- | --- |
| 存在一段 diff,你想知道它是否构建得对 *并且* 是对的事情 | `code-review` |
| 你想在 diff 中找 bug:空路径、竞争条件、off-by-one | Claude Code 自带的内置 review,不是这个(见下面的命名冲突) |
| 还没有写任何东西,你希望先写测试 | [tdd](https://aihero.dev/skills-tdd) |
| 整个 spec 需要构建,review 也包含在内 | [implement](https://aihero.dev/skills-implement),它自己就会调用本技能 |
| 整个代码库已经跑偏,而不是某段 diff | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) |
| 某样东西坏了,你不知道为什么 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |

你必须提供固定点。如果你没提供,该技能会询问而不是猜测;然后它会检查这个 ref 能解析、diff 不为空,然后才生成任何子 agent,所以分支名拼错会在你面前暴露,而不是发生在两个 sub-agent 的内部。

## Prerequisites

Standards 轴不需要任何东西。它读取仓库中已记录的任何东西(`CODING_STANDARDS.md`、`CONTRIBUTING.md` 等),在仓库什么都没记录时退回到内置的基线。

Spec 轴需要 spec 存在且可被找到。它按以下顺序寻找:

1. commit message 中的 issue 引用(`#123`、`Closes #45`、GitLab 的 `!67`),通过 `docs/agents/issue-tracker.md` 获取。
2. 你作为参数传入的路径。
3. `docs/`、`specs/` 或 `.scratch/` 下与分支或特性名匹配的 spec 文件。
4. 问你。

步骤 1 依赖 `docs/agents/issue-tracker.md`,而这份文件是 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 写的。没有它,只要你手工给一个路径,Spec 轴照样工作。在完全没有 spec 的情况下,Spec 子代理被跳过,报告直接说 "no spec available",而不是凭空编造需求。

## The two axes

| | Standards | Spec |
| --- | --- | --- |
| 问题 | 它构建得对吗? | 它是对的事情吗? |
| 读取 | 仓库记录的规范,加上 smell 基线 | 发起的 issue 或 spec |
| 报告 | 已记录的违规(可以是硬性的),以及 smells(永远是判断) | 缺失或部分需求、scope creep、错误实现的需求 |
| 每条发现引用 | 规范文件及其规则,或命名的 smell 加上 hunk | spec 的那一行 |

一个不知道你规范的通用 review 技能,正是这个设计要避免的东西:它会标记出你代码库里刻意的写法,漏掉你代码库实际依赖的不变量。因此仓库自身的文档是 Standards 轴的 [primary source](https://www.aihero.dev/ai-coding-dictionary/primary-source),**仓库永远凌驾**。

**smell baseline** 是它底部的地板,十二个来自 _Refactoring_ 第 3 章的 Fowler code smells:Mysterious Name、Duplicated Code、Feature Envy、Data Clumps、Primitive Obsession、Repeated Switches、Shotgun Surgery、Divergent Change、Speculative Generality、Message Chains、Middle Man、Refused Bequest。每一条都是带标签的启发("possible Feature Envy"),从来不是硬性违反,并且每一条都以 *它是什么* → *怎么修复* 的形式陈述,所以一条发现会附带着动作出现,而不是一句抱怨。你的 linter 已经强制执行的任何东西,两个轴都会跳过。

## Common questions

**它和 Claude Code 自带的 `/code-review` 冲突了。我该怎么办?**
这是该技能被报告最多的问题,而且还没修复。Claude Code 自带一个 `/code-review`,功能不同:它在 diff 中寻找 bug,而这个检查 spec 合规性和仓库规范。安装这个库意味着其中一方胜出,而哪一方胜出取决于你的安装方式。通过插件市场安装,所有东西都被以 `mattpocock-skills:` 为前缀别名化,内置的那个在裸名上就很难触达;通过普通 skills 安装,本地文件胜出,这个技能就遮蔽了内置的那个。一个干净的处理方法是彻底移除 Claude Code 自带的 skills:节省大量[上下文](https://www.aihero.dev/ai-coding-dictionary/context),冲突也不再相关。遮蔽本身可以视为 Claude Code [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 的一个 bug(技能作者应该可以任意命名),所以另一种做法是给本地副本改名。编辑 frontmatter 或重命名目录会被 `npx skills update` 撤销;用户报告的可靠做法是把该技能 fork 成新名字,从被管理的集合里把 `code-review` 删掉,并记下 fork 时的 commit 以便手工重新同步。

**它的 sub-agent 反复调用 `/code-review` 并生出更多 agent。**
已知的 open bug,被好几个人在多于一种 harness 上复现。Standards 和 Spec 的 prompt 都没有禁止委派,所以 sub-agent 可以重新发现这个技能并再次扇出:一份报告达到了 50 多个 agent。人们在自己 fork 上采用的修复是在两份 sub-agent 简介末尾追加一行:"Do not invoke `/code-review` or spawn additional agents: perform this review directly." 有些人倾向于在 harness 层处理这个事,让每个技能都继承这道护栏。两种都没进到已发布的技能里。如果你无人值守地跑这个,留意 agent 数量。

**应该在写出代码的那个[会话](https://www.aihero.dev/ai-coding-dictionary/session)里跑它吗?**
建议另开一个。一位读者的说法:"Same context reviewing itself isn't review, it's confirmation bias with a slash command." 写作会话里的 reviewer 持有塑造这段代码的全部假设,这正是一个独立 reviewer 不会拥有的上下文。这也是为什么有人调用 [implement](https://aihero.dev/skills-implement) 时不要它的内置 review 步骤:它会把 review 跑在刚写出 diff 的那个会话里。在一个干净的会话里自己调用 `/code-review` 才是诚实的做法。

**每个 ticket 后跑一次,还是在最后跑一次?**
两种都行,这个技能不会替你决定。按 ticket 跑让每个 diff 小到 Spec 轴有一个明确的 spec 可对照,这正是 `implement` 用的模式。在分支末尾批量跑能抓到 per-ticket 通过但相互之间交互遗漏的情况。如果你不确定,那就按 ticket 跑,并在分支点上跑最后一次整体复审。

**能相信它的发现吗?**
不能不看就信。sub-agent 输出是一种假设而不是证据:一个团队报告说基于散文式 review 被放过了十几个 break change。这个技能把两份报告原样或轻度清理后汇总,而不是把每条声明重新对照文件去核实,所以一条发现可能引用错位置或夸大影响。在按它行动之前,读每条发现的引用。强制每条发现必须带一份引用(一条规范规则、一个 smell 加 hunk、或一行 spec),正是让它至少能被核查的关键。

**为什么每次跑它都找出新问题?**
因为修复创造了新表面,而且 Standards 轴里靠判断的那一半在多次运行之间并不确定。一位读者直白描述了这个循环:"/code-review and /improve-code-architecture always find new stuff every time. I implement fixes, rerun these skills, and again and again." 没有收敛保证。把一次跑当作一份线索清单,只处理那些背后有引用规则支撑的,然后停:不要陷入循环跑直到它返回干净,它不会的。

**它会审查我未提交的工作吗?**
不会。它对 `<fixed-point>...HEAD` 做 diff,即三点语法,从 merge-base 测量,排除 staged 和 working-tree 的变更。如果 `implement` 没有做过中途 commit,即将被提交的工作对 review 来说是不可见的。先 commit,再 review,然后用 amend 或加一个 fixup。

## It's working if

- 在任何 sub-agent 生成之前,它在错误的 ref 或空的 diff 上拒绝启动。
- 报告以 `## Standards` 和 `## Spec` 两个分开的区块出现,而不是一份合并清单。
- 每条 Standards 发现都指向你仓库某份文件中的一条规则或十二个 smells 中的某一个,并引用了 hunk;每条 Spec 发现都引用 spec 的一行。
- 收尾的总结给出每个轴的最严重问题,并拒绝给出总体赢家。
- 在没有 spec 时,Spec 区块直接说没有,而不是列出它从代码推断出来的需求。

## Where it fits

`code-review` 是构建链尾部的 review 步骤:`grill-with-docs → to-spec → to-tickets → implement → code-review`。它也可以独立运行,作用于你指向的任何分支或 PR。

- [implement](https://aihero.dev/skills-implement) 是最近的邻居:它驱动构建,并把本技能作为它在 commit 之前的收尾 review 调用。
- [to-spec](https://aihero.dev/skills-to-spec) 和 [to-tickets](https://aihero.dev/skills-to-tickets) 生成 Spec 轴所对照的那份文档;spec 含糊,这条轴就含糊。
- [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 是整个代码库的对应物:这个技能永远只看一段 diff。

[ask-matt](https://aihero.dev/skills-ask-matt) 在你拿不准情境需要哪个技能时,在整套技能之间路由。
