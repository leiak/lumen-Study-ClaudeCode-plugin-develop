## What it does

`domain-modeling` 在你做设计时构建并打磨一个项目的 **ubiquitous language**:挑战与术语表冲突的词,逼迫你在含糊词处换上一个精确词,并用一个具体场景压力测试一个关系,直到边界清晰。

它是 **主动的** 纪律,而非被动的。读 `CONTEXT.md` 来借用它的词汇是任何技能都能做到的一行习惯;本技能用于当你 *在改变* 模型的时候。这就是它会打断你的原因。它把已确定的术语在确定的那一刻写进 `CONTEXT.md`,在对话中间,而不是最后产出一份整洁的术语表,因为批处理版本只是对一个[会话](https://www.aihero.dev/ai-coding-dictionary/session)的总结,而内联版本才是该会话的真实产出。

## When to reach for it

输入 `/domain-modeling`,或在任务匹配时 agent 自动调用它。实际上,自动调用是这个技能最薄弱的地方:当 `grill-with-docs` 或 `wayfinder` 说到加载它时,[模型](https://www.aihero.dev/ai-coding-dictionary/model)经常加载 `grilling` 而跳过本技能。如果一个 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 会话跑完了,而 `CONTEXT.md` 在结束时没被动过,就是这种情况;和另一个技能一起按名字调用它吧。

在 *词* 本身就是问题的时候调用它:

| 处境 | 动作 |
| --- | --- |
| 两个人对 "cancellation" 的理解不同 | `domain-modeling`:选 canonical 词,把其他的列在 `_Avoid_` 下 |
| "Account" 在三个文件里做了三件事 | `domain-modeling`:拆成 Customer 和 User |
| 你刚做了一个难以回退的架构选择 | `domain-modeling`:如果该选择过了门槛,它会提议一份 ADR |
| 模块的 *形状* 才是问题:seam 放哪里、接口多深 | [codebase-design](https://aihero.dev/skills-codebase-design) |
| 你想在构建之前让整份计划都被质疑 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs),它在底层驱动本技能 |
| 你只是想查一个术语,而不是改它 | 没有。看 `CONTEXT.md`。它就是一个文件。 |

## Prerequisites

事先不需要。该技能写进两个地方,并按需懒创建:

- 仓库根的 **`CONTEXT.md`**,由第一个确定的术语创建。在仓库根有 `CONTEXT-MAP.md` 的情况下,术语进入 map 指向的 per-context `CONTEXT.md`。
- **`docs/adr/`**,由第一份通过门槛的 ADR 创建。

开始之前什么都不需要存在,任何东西都不是推测式创建的。

## Two artifacts, two bars

术语表与 ADR 的标准不同,把两者混淆是这个技能大多数麻烦的根源。

| | `CONTEXT.md` | `docs/adr/NNNN-slug.md` |
| --- | --- | --- |
| 容纳 | 术语。一个事物 *是什么*,一两句陈述,把被否决的同义词放在 `_Avoid_` 下 | 一项决策,用一到三句话:背景、选择、原因 |
| 写入门槛 | 一个含糊的词变成了 canonical | **三条同时**:难以回退、没上下文会令人意外、来自一个真实的权衡 |
| 写入时机 | 内联,术语被确定的那一刻 | 提议式,不是默认进行 |
| 永不容纳 | 实现细节、一份 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)、随手笔记、通用编程概念 | 本会话每项选择的流水账 |

ADR 三项测试缺一就不写 ADR。一项容易被回退的决策终将被回退;一项不令人意外的决策没人会有疑问;一项没有真正替代物的决策记录的是你做了一件显然的事。

`CONTEXT.md` 的规则才是真正要记住的,因为它在实际中会破。**它就是一份术语表,仅此而已。** 一旦不管,模型会把 "写进 `CONTEXT.md`" 当作把你给的每个答案都持久化的许可,这个文件就变成了一份滚动增长的 spec。这是这个技能被报告最多的问题,跨越多个模型。

## Cross-referencing, and where it stops

让这个技能起作用的那个动作:当你陈述某事如何工作时,它去查代码,并把矛盾摆出来。*"Your code cancels entire Orders, but you just said partial cancellation is possible, which is right?"* 语言和代码在出声后达成一致,在改动其中任何一边之前。

它的边界值得知道。它交叉引用 **代码** 与已提交的 `CONTEXT.md`/ADR,仅此而已。它不搜你的 issue tracker,所以一次命名冲突如果在几个月前某条已关闭的 issue 上被辩论并刻意定下来,它会当作新东西再次摆出来。有一个[open request](https://github.com/mattpocock/skills/issues/717) 想修这个;在那之前,变通方法是把指令放进你自己的 `docs/agents/domain.md`,技能本来就已经读它。

## Common questions

**我的 `CONTEXT.md` 已经 500 行。1,000。3,000。我该怎么办?**
大小是症状,不是病:这个文件吞下了实现细节和从来都不是术语表内容的决策。修法是一条直接的指令:`/grill-with-docs make my CONTEXT.md more concise and remove any implementation details from it`。在一个臃肿的文件上跑这个,大部分会被去掉。只在该文件确实精简、却仍然覆盖两个读者不希望同时持有的领域时,再动用 `CONTEXT-MAP.md` 拆分;拆分一个臃肿文件只会给你几个臃肿文件。该技能在这方面的指引还不够有力,无法从一开始就阻止增长,跟踪这个的 issue 仍然 open。

**为什么叫 `CONTEXT.md` 而不叫 `GLOSSARY.md`?**
这是整个技能集里被争论最多的命名问题,而且没有定论。反对现在这个名字的理由是有道理的:如果它是 "a glossary and nothing else",`GLOSSARY.md` 就说得更明白,而且正如一位读者所说,"with ai agents everything is [context](https://www.aihero.dev/ai-coding-dictionary/context)"。支持它的理由是 map:`CONTEXT-MAP.md` 指向多个 `CONTEXT.md` 读起来自然,而 `GLOSSARY-MAP.md` 不那么自然,且 `context` 是 DDD 里 bounded 区域的标准词。至少有一个人纯粹为了改名而维护着本地 fork。你也可以这么做,但技能集里的其他技能都找 `CONTEXT.md`,所以改名意味着把每一个都打补丁。

**`/ubiquitous-language` 去哪了?**
它被移除了,而且不是被弃用。它的职责并入了 `domain-modeling`,后者持续维护整个模型,而不是在一次对话里倒出一份术语表。词汇强制的负载更重了,而不是减轻:它现在跑在 grilling、triage 和 mapping 之下,而不再是一个你记得去做的独立 pass。

**我如何为一个完全没有术语表的代码库获得一份术语表?**
显式去要,而不是等它慢慢累积。`/grill-with-docs help me scaffold my existing repo with a CONTEXT.md` 是文档化的路径;预期一次很长的盘问:有用户报告在文件成形前被问了 50 多个问题。在一个 brownfield 仓库上,顺带累积的方式会让术语表长得太慢。

**我能不能保留 domain model,但使用自己的 ADR 格式?**
今天还做不到干净。术语表一半和 ADR 一半打包在一个技能里,所以一个有既定 ADR 约定的团队(不同的模板、不同的位置、不同的命名)拿到的指令会和自家风格冲突。当前的选项是把技能本地复制一份并修改,或者在仓库自己的 agent docs 里覆盖 ADR 约定。把这两半拆开是 [open request](https://github.com/mattpocock/skills/issues/557)。

**术语表真的有用吗?它就是多一份要 review 的工件,而且会过期。**
有时确实没用,而诚实地说清楚它的边界也是值得的。DDD 越接近实现就越没那么有用:回报在更上游,在命名和概念对齐,不在 aggregate 和分层仪式。同义词控制在命名边界处才有意义:模块名、表名、状态 enum、issue 标题、CLI 命令。它在普通散文里意义小得多。还有一个活跃的反对意见是:领域词在已经共享它们的人类之间压缩沟通,而对一个 agent 来说,plain-English 描述和术语得到的结果是一样的。按这种解读,术语表的价值在于让你和你的 reviewer 与 agent 的实际做法保持一致,而不是让 agent 更好。在一天的构建上,跳过它。一份未审的、由 agent 编写的术语表比没有更糟:它会变成听起来自信、后续会话当作真理的 lore。

**它能把我含糊的 prompt 替我转成领域语言吗?**
不能,而且目前没有计划做这种技能。一门你自己都不理解的领域语言,一旦写下来就变成无意义的废话。这个技能在你已具备理解之后强制精度;它不制造你没有的词汇。相关的陷阱是用了领域词却没做建模:错误的结构套上正确的名词,产出读着对、其实不对的东西。

## It's working if

- 它在你话说到一半时停下来,问你到底指两件事中的哪一件,而不是挑一个继续。
- `CONTEXT.md` 在对话 *期间* 变更,而不是最后一次性冒出来。
- 它对一件你明天就能撤回的事拒绝写 ADR,并指出三项测试中哪一项没过。
- 新条目用一两句话定义一个事物 *是什么*,并把你要放弃的词列在 `_Avoid_` 下。
- 它在你代码和你说的不一致时,把你的代码回怼给你。
- `CONTEXT.md` 缩短的频率和增长的频率一样高。

## Where it fits

`domain-modeling` 是一个 **模型调用的 reference**,跑在其他技能 *之下* 多于独自跑。[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 驱动它走完一个 grilling 会话;[wayfinder](https://aihero.dev/skills-wayfinder) 在绘制 map 时加载它;[triage](https://aihero.dev/skills-triage) 用它把 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 维持在项目自己的用词里;[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 在决策成形时调用它。它最贴近的同胞是 [codebase-design](https://aihero.dev/skills-codebase-design):两者是其他技能之下的词汇层,本技能针对 *领域*,那个针对模块的 *形状*。它也可以被直接触达,在你想要这种纪律而又不想承诺某个技能会拉它进来的步骤时。当你不确定哪个技能合适,[ask-matt](https://aihero.dev/skills-ask-matt) 会路由。
