## What it does

`implement` 构建已经被决定的工作。你把它指向一个 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket)、一份 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)、或你刚刚在对话里同意的计划,它就写代码、在 seam 处驱动 [tdd](https://aihero.dev/skills-tdd)、一边走一边跑类型检查、最后跑一次 [code-review](https://aihero.dev/skills-code-review)、并 commit 到当前分支。

它从不再开计划。没有盘问、没有澄清、没有提议别的方案。无论上游定下来的是什么,就是输入,而这个技能的全部工作就是把它变成一个 commit。这就是它和向一个全新的 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 输入 "build this" 的区别,后者会乐呵呵地在构建过程中重新设计它。

## When to reach for it

你通过输入 `/implement` 自己调用:agent 不会自行调用它。它带着 `disable-model-invocation: true` 发布,所以其他技能也不能调用它。无论 [ask-matt](https://aihero.dev/skills-ask-matt) 还是 [to-tickets](https://aihero.dev/skills-to-tickets) 说 "then `/implement` per ticket",那是对你说的指令,而不是 agent 会未经提示就做的事。

工作目前所在的位置,决定了它是不是合适的技能:

| 工作是…… | 调用 |
| --- | --- |
| 一个 tracker 上的 ticket | `/implement #42`,一个 ticket 一个[会话](https://www.aihero.dev/ai-coding-dictionary/session),ticket 之间 [clearing](https://www.aihero.dev/ai-coding-dictionary/clearing) 上下文 |
| 一份 spec,还没拆分,构建跨会话 | 先 [to-tickets](https://aihero.dev/skills-to-tickets),然后每个 ticket 跑 `/implement` |
| 一份 spec,构建规模小 | 直接对 spec 跑 `/implement` |
| 只在刚刚的对话里,而且仍然小 | 就在同一窗口里跑 `/implement` |
| 还没写在任何地方 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs),或者没有代码库时用 [grill-me](https://aihero.dev/skills-grill-me) |
| 一个具体行为,你想 test-first,而且没有 spec | 直接 [tdd](https://aihero.dev/skills-tdd) |
| 已经构建,你想检查 | 直接 [code-review](https://aihero.dev/skills-code-review) |

值得专门说一下"同一会话"那种情况,因为这个技能自己的首行没覆盖。`SKILL.md` 上写的是 "the spec or tickets",这会把[模型](https://www.aihero.dev/ai-coding-dictionary/model)推向去找一个不存在的文件。如果计划只活在对话里,在调用时说明。

## Prerequisites

`implement` commit 到你所在的分支。它不会创建分支,也不会询问。在开始之前确认你在你希望工作落到的那个分支上。

如果 tickets 来自 [to-tickets](https://aihero.dev/skills-to-tickets),承载它们的 tracker 是由 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 配置的。`code-review` 读取同一份配置来在收尾时找到发起它的 spec。

## What one run does

一次运行是五拍,按顺序:

1. 读 ticket 或 spec,确定 seam。
2. 在事前约定的 seam 处驱动 [tdd](https://aihero.dev/skills-tdd),一片红绿一片红绿。
3. 频繁跑类型检查,以及单个测试文件。
4. 在最后跑完整套测试一次。
5. 跑 [code-review](https://aihero.dev/skills-code-review),然后 commit 到当前分支。

一次运行覆盖一个 ticket。[to-tickets](https://aihero.dev/skills-to-tickets) 产出的 tickets 是 tracer-bullet 纵向切片,每个大小正好装进一个全新的[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window),所以预期的节奏是:清上下文,implement 一个 ticket,commit,再清。每个 ticket 是自包含的,这正是让上一个 ticket 的上下文成为可丢弃的原因。

## Pre-agreed seams

这个技能跑起来所依据的概念是 **seam**:你观察行为所穿越的公共边界,无需伸到里面去。测试活在 seam 处。在任何代码被写之前就约定好在某个 seam 上工作,是测试保持耐久的保证,因为底下的实现可以被重写而不需要动测试。

"事前约定"这个词做着真正的工作,它也是这个技能最弱的关节。`implement` 内部没有任何东西约定 seam。`tdd` 是那个会去问的技能,并拒绝在未确认的 seam 上写测试。所以实践中约定发生在 spec 上游,或者在运行的第一次对话里。如果约定没在任何地方发生,前置条件永远不满足,这次运行悄悄变成 "就写代码"。在 spec 里把 seam 命名清楚,是阻止这件事的关键。

## Common questions

**它跑完了,我的 ticket 仍然开着,验收标准仍没勾上。**
正确,也是预期。`implement` 没有收尾步骤。它在 commit 处结束,从不碰这个工作项,无论在 GitHub Issues 还是在本地 markdown tracker 上都已确认,所以这不是一个 tracker 集成的问题。它也不会按 `code-review` 的发现采取行动,也不会在源 issue 上把 `- [ ]` 框勾上。关掉 ticket 和 reconcile 这些标准是你自己的事。这在依赖链上咬得最狠,因为 `to-tickets` 把 frontier 定义为 blocker 全关闭的 tickets。如果没人关 ticket,也就没人变得可见地 unblocked。

**我能不能一次指向所有 ticket,或者并行跑多个?**
不能。一次调用,一个 ticket。批量分派一个 ticket 队列、以及 [subagent](https://www.aihero.dev/ai-coding-dictionary/subagent) 扇出都被反复请求过,两者都不存在。在同一个 checkout 里并行跑多个 `/implement` 会话比"不支持"更糟:一份现场报告描述了一次 `git commit --amend` 在一个会话里落在了另一个会话的 commit 上,一次 stash 从 `refs/stash` 里消失,commits 落到错误的分支上,这些都发生在单个下午、跨三个 issue。这些会话共享一个工作目录、一个 index 和一个 HEAD。Git worktrees 是社区的变通方案,注意 `refs/stash` 也跨 worktree 共享,所以单靠 worktree 修不了 stash 那个问题。今天你想要并行,得自己拼装。

**它能不能开一个 PR 而不是直接 commit?**
内置没有。它直接 commit 到当前分支,有些人觉得这太激进了:代码在你有机会验证它工作之前就落地了。没有配置开关,也没有 PR 模式。人们在调用里改写("commit to a branch and open a PR")或编辑他们本地的技能副本。

**`code-review` 说它看不到我的改动。**
`code-review` 审查 `git diff <fixed-point>...HEAD`,这排除了 staged 和 working-tree 的变更。`implement` 在 commit 前跑它,所以除非已经存在一个中途 commit,这个 diff 里就没东西可审。好几个人报告过这点,在两侧都未修。先 commit,再相对于你分叉的那一点去 review。

另外,有人故意希望整个 run 都不带 review,因为刚写完代码的 agent 在审自己刚写的代码时偏向自己的解法。在一个干净会话里相对一个固定点跑 [code-review](https://aihero.dev/skills-code-review) 是一个合法的替代,也是为什么这个技能把它的两个轴跑在不同的 sub-agent 里。

**一个 ticket 烧了 150k token。我用错了吗?**
很可能是 ticket 太大,而不是技能用错。一次 run 要做代码库探索、每个 seam 的红绿循环、跑完整套测试、以及 review,所以一个不平凡的 ticket 超过 100k [tokens](https://www.aihero.dev/ai-coding-dictionary/token) 是正常,不是哪儿坏了。杠杆在上游:在 [to-tickets](https://aihero.dev/skills-to-tickets) 里把 tickets 调成正好装下一个全新窗口的大小。如果单个 ticket 一直爆,拆它而不是升 [effort](https://www.aihero.dev/ai-coding-dictionary/effort) 等级。

**在干净会话里 `/implement #2` 去做了完全不相关的事。**
`#2` 会对照 agent 能看到的任何编号列表解析,在干净会话里可能是一个 todo 文件、一份 checklist、或另一份工作清单,而不是配置好的 tracker。解析是 confident 而非 fail-closed,所以错在它开始之前都不明显。传完整引用、issue URL 或 `owner/repo#2`,并让它在开始之前把 title 回读一遍确认。

## It's working if

- 会话以读 ticket 或 spec 并复述它要构建什么开场,而不是问你要构建什么。
- 你能在 trace 里看到一个实际的 `/tdd` 调用,而不是仅仅看到 diff 里冒出来一堆 test。
- 类型检查和单个测试文件在 run 期间反复运行,完整测试套件在临近结尾跑一次。
- 这次 run 在当前分支上到达一个 commit,而你不需要提示它继续。
- diff 是一个 ticket 的工作量:一条贯穿每一层的纵向切片,而不是几张 ticket 扫到一处。

## Where it fits

`implement` 是主链的构建步骤,倒数第二:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review
```

它的邻居是 [to-tickets](https://aihero.dev/skills-to-tickets),后者产出它消费的 tickets 并声明决定顺序的阻塞边;[tdd](https://aihero.dev/skills-tdd),它在每个 seam 内部驱动;以及 [code-review](https://aihero.dev/skills-code-review),它在 commit 前跑。它位于规划技能的下游并信任它们。它不重新验证它拿到的东西的形状,所以一份糟糕结构化的 map 或一张水平分层的 ticket 会按写下来的那样被构建。

这份信任就是为什么 [wayfinder](https://aihero.dev/skills-wayfinder) 在 [to-spec](https://aihero.dev/skills-to-spec) 处合流到这条链,而不是把它自己的 map 直接喂进 `implement`。只有当工作量确实小时,才从一份 map 直接走 `implement`。

[ask-matt](https://aihero.dev/skills-ask-matt) 在你拿不准你处在哪条 flow 时,在整套技能之间路由。
