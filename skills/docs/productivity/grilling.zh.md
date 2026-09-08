## What it does

`grilling` 是压力测试一份计划、一项决策、或一个想法的盘问循环,在任何人据此行动之前。它把对象映射为一份 **design tree**:每项决策分出挂在它身上的决策,然后一枝一枝地盘问你,直到没有什么被默默假设。

它不一次问一个问题,也不一次性问所有事。每一 **round** 问整个 **frontier**:每项其先决条件都已经定了的决策,仅此而已。如果一道题依赖于另一道还没开的答案,这两道题永远不在同一 round 里;一道依赖于仍然开放答案的题属于更靠后的 round。你的回答敲定决策,frontier 向外移动,下一 round 问它解除了什么。一次典型情况下十三个问题会跨大约三个 round,而不是十三个。

## When to reach for it

输入 `/grilling`,或在任务匹配时 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 自动调用它。它是 grilling 家族中唯一一个模型调用的 [技能](https://www.aihero.dev/ai-coding-dictionary/skill),这就是为什么你很少输入它:通常是你 *输入* 的某个技能在替你跑它。

直接输入 `/grilling` 拿到的是朴素的盘问,没有别的。想要更多的话:

| 你拥有 | 调用 |
| --- | --- |
| 你不在工作目录里 | [grill-me](https://aihero.dev/skills-grill-me):同一个[会话](https://www.aihero.dev/ai-coding-dictionary/session),名字起得让 agent 永远不自己触发它 |
| 你在一个工作目录里 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs):同一个会话,且边进行边写 `CONTEXT.md` 和 ADR |
| 一项大到无法塞进一个会话的工作 | [wayfinder](https://aihero.dev/skills-wayfinder):它绘制一张 map,并在决策 tickets 里跑 grilling |
| 一个谈论无法定夺的问题:某东西应该长什么样或感觉如何 | [prototype](https://aihero.dev/skills-prototype):搭出用完即弃的版本,然后回来 |
| 一个你自己的技能需要盘问 | 从它里面调用 `/grilling`,而不是另写一个盘问 |

## The round, the frontier, and who decides

三个概念承载整个技能。

**design tree** 是对象的模型:决策之下挂着决策。**frontier** 是其先决条件都已经敲定的决策集合:目前唯一能诚实问出来的问题。一个 **round** 是一整个 frontier,完整地问、完整地答。

在一轮之内,每个问题以固定的形状到达:编号加标题,跟在 `❓` 后面,然后是正文,然后是 agent 的推荐答案单独占一行 `➡️`。这就是让一轮可以用编号回答的原因("1 yes, 2 the second option, 3 no, here's why"),而不是引用问题。该格式有一个已知的粗糙边:那条推荐有时 *反对* 问题的措辞,所以同意推荐意味着对问题答 "no"。那种情况下,按推荐答并明说。

另一半设计是事实与决策的切分。事实是技能自己的事:当一道 frontier 题需要 [environment](https://www.aihero.dev/ai-coding-dictionary/environment) 能定夺的东西时,它派一个 [sub-agent](https://www.aihero.dev/ai-coding-dictionary/subagent) 去找出来,而不是来问你。它不会被那阻塞;只有依赖那次正在跑的探索的问题才会等。决策是你的,它必须等。一个跑着 `grilling` 的 agent 如果回答了自己的决策,就破坏了技能,而不是在发挥它的弹性。会话在 frontier 为空时结束,并且在它按你同意的内容行动之前,需要你确认已达成分享的理解。

诚实的限制:frontier 是 agent 的判断,而不是计算出来的图。它可以把两道题放同一 round,然后才发现一个回答本来应该改变另一道。对此没有超出告诉它之外的护栏,而那会在下一 round 重新打开受影响的分支。

## What lives here and what lives in the wrappers

本页覆盖机制。人们最常想要的那些事在一层之上被记录。

| 问题 | 在哪里被回答 |
| --- | --- |
| 树、frontier、rounds、问题格式、事实 vs 决策 | 这里 |
| 一个会话应该跑多久、面对一道谈论无法回答的问题怎么办、如何避免顺口应承 | [grill-me](https://aihero.dev/skills-grill-me) |
| 什么被写到 `CONTEXT.md`、什么成为 ADR | [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |

## Common questions

**我能不能回到一次一道题?**
可以,很大一部分受众这么做。把它加到你的全局 `CLAUDE.md` 里:

```
When grilling, ask one question at a time.
```

Round-based 默认是真正有争议的。慢慢阅读、用第二语言工作、或把顺序格式当作专注脚手架的实践者都报告说,一次一道的节奏对他们更好,而这种 opt-out 是被支持而不是被容忍的。

**`/batch-grill-me` 去哪了?**
进了这个技能。Round-based 提问曾作为独立技能短暂发布,然后并入 `grilling` 自身,所以所有基于原语构建的东西(`grill-me`、`grill-with-docs`、`triage`、`wayfinder`)一次获得。没有 `batch-grill-me` 要装,也没有独立的顺序式技能;上面那条 `CLAUDE.md` 就是回到一次一道的方式。

**一次问整个 round 必然丢掉我之前回答会引发的问题,不是吗?**
这是对 round 设计最常见的反对意见,而 frontier 就是答案:一轮只包含互相不依赖的问题,所以一轮内的任何回答都不可能让本轮内另一道题失效。回答仍然重塑下游:下一 round 被重新计算,而不是预写。你丢掉的比 "一次问所有问题" 这个说法暗示的要少,比什么都没有要多:见上面 frontier 的限制。

**它跑完问题然后开始构建。**
确认门正是为此存在:技能在前沿清空时并未完成,而是当你声明理解已经共享时才完成。更弱、更快的 [models](https://www.aihero.dev/ai-coding-dictionary/model) 仍然会破坏它;这在低 effort 或非前沿模型上最常被报告,这些模型会把 "盘问直到共享理解" 塌成几个问题加一份大纲。如果你的模型这么干,可靠的修法是在你自己的 `AGENTS.md` 或 `CLAUDE.md` 里放一行告诉 agent 不许未经许可就实现。

**它回答了自己的问题,而不是来问我。**
那是这次 run 的一个 bug,不是预期行为,而且是技能文本中把事实和决策分开的原因。它在另一种技能在"解决这张 ticket"框架里跑 `grilling` 时最常出现,在那里周围任务读起来像继续的许可。同样的约束也是为什么这里没有 async 模式:人们请求过那种变体,读一个 GitHub issue 然后发一份合并的 decision memo,而那是另一个技能,因为一场无人回答的 grilling 会话产出的只是 agent 的意见而不是你的。

**我能不能给问题数加个上限?**
不能,而且一个上限被刻意排除在外。有些计划要三个问题,有些要五十;一个固定上限要么截掉硬骨头,要么在容易的那个上显得武断。以自然语言引导才是预期控制:告诉它收尾,或停下并在当前点接受计划。如果一次会话跑得很长,原因通常是 scope 太大;把工作拆开,再 grill 各个部分。

**我只装了 `grill-me`,什么都没发生。**
`grill-me` 是一个一行技能,其整个正文就是 "run a `/grilling` session",所以它也需要这个技能装上。同样的事也适用于 `grill-with-docs`,它额外需要 [domain-modeling](https://aihero.dev/skills-domain-modeling)。装整套避免这个问题;选择性装意味着把原语也装上。

**`grill-with-docs` 跑了,但它没加载 `grilling`。**
一个真实且未修的粗糙边,在多种 [harnesses](https://www.aihero.dev/ai-coding-dictionary/harness) 和模型上被报告:一个命名另一个技能的技能并不稳定地让那个技能被加载,而 `grill-with-docs` 命名了两个。特征是一次会话一次把所有问题抛出,没有附推荐:那是模型在即兴发挥一段盘问,而不是在跑这一段。直接问 agent 是否加载了 `grilling` 和 `domain-modeling`,通常能恢复。

## It's working if

- 一轮以编号列表到达,每道题后面跟一条单独占 `➡️` 的推荐,而你能用编号答完整轮。
- 一轮里没有任何东西需要同一轮的另一道题先被回答。
- 后续的 round 问的是第一 round 没法问的东西。
- 它自己去查事实(读文件、派 sub-agent),而不是来问你它本来可以找出来的东西。
- 在后台跑的 research 不会拖住 round;只有依赖它的题才等。
- 它在结尾停下,要求你确认理解已共享,而不是开始工作。
- 问题数保持高,而 round 数保持低。

## Where it fits

`grilling` 是一个 **原语**,而不是你排期的一个步骤:盘问技巧的唯一真源,放在一处,让每个需要盘问的技能伸向它而不是各自发明。[grill-me](https://aihero.dev/skills-grill-me) 和 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 是它的两个用户调用前门,而 `grill-with-docs` 是主构建链开始的地方,在 [to-spec](https://aihero.dev/skills-to-spec) 之前。[wayfinder](https://aihero.dev/skills-wayfinder) 跑它去解决决策 tickets,[triage](https://aihero.dev/skills-triage) 跑它把一份模糊的报告磨成可工作的报告,[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 在你挑了一个候选者深化时跑它走决策树。当你不确定哪个入口合适时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
