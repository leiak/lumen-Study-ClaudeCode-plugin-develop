## What it does

`ask-matt` 是本仓库所有技能之上的路由器。你描述自己所处的处境(一个你无从下手的主意、一堆涌入的 bug 报告、一个已经跑得很长的[会话](https://www.aihero.dev/ai-coding-dictionary/session)),它给出适合的技能或技能序列,并标出那个序列中需要人做决策的位置。

它给出建议并停止。它不进行 grilling,不写[规范](https://www.aihero.dev/ai-coding-dictionary/spec),不开文件,也不触发它刚刚命名的那个技能;你拿到的是接下来要输入的命令,由你来输入。它还是一份手写的本仓库技能地图,而不是对你已安装技能的扫描,所以它不会把你自己的技能或别的作者的技能纳入路由。

## When to reach for it

你通过输入 `/ask-matt` 来调用它;agent 不会自己调用它。

| 你的处境 | 路由器返回的内容 |
| --- | --- |
| 有一个主意,但不知道从哪里开始 | 主流程的起点,以及构建是否小到可以跳过 spec |
| 来自其他人的 bug 和请求 | [triage](https://aihero.dev/skills-triage) 这个 on-ramp,以及为什么你自己生成的[tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)不该走它 |
| 两个看起来可以互换的技能 | 它们之间的边界,通常是一项具体的测试而不是口味问题。[grill-me](https://aihero.dev/skills-grill-me) 还是 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 取决于你是否在一个工作目录中;[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 还是 [wayfinder](https://aihero.dev/skills-wayfinder) 取决于工作量是否适合一个会话 |
| 一个跑得很长的会话,需要决定[上下文](https://www.aihero.dev/ai-coding-dictionary/context)的去向 | 在阶段边界处五种选项的有序决策树 |
| 你已经选定了一个技能 | 没什么用。直接调用那个技能 |

## Prerequisites

路由器只是命名技能,并不安装它们。它指向的每个技能都必须已经安装好,推荐才真正可用,而且它只认识本仓库中已经晋升的技能。

那些依赖 tracker 的路由(triage、`to-spec`、`to-tickets`、`implement`)假设 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 已经为该仓库配置好了 issue tracker。即使那一步还没做,路由器也会照常推荐。

## Flows, not skills

这个技能给你的思维词是 **flow**:一条 *穿过* 技能的路径,而非单个技能。命名你所在的处境,就把你放在某条 flow 的某个步骤上,这是一个与 "这里是匹配你关键词的技能" 完全不同的答案。共有四类路由,这个技能自身完整地承载着它们:

- **主流程**,从主意到上线。Grill、spec、tickets、implement、review,中间有两个分支:在需要可运行代码才能定夺的问题上绕道 prototype;以及 spec-and-tickets 的拆分,只有当构建跨多个会话时才赚回它的成本。
- **On-ramps**,用于产生工作、然后并入主流程的处境:涌入的 bug 报告、出故障的东西、或一项过于模糊、过于庞大而无法塞进一个会话的工作。
- **Standalones**,在所有流程之外,按各自的逻辑单独触达:the prototype、the questionnaire、你已经坐在其中的 merge conflict。
- **底层的词汇层**,当问题在于词汇而非流程时,这两个 reference 是其他技能所引用的。

## The phase boundary

它交给你的另一个概念是 **phase boundary**(阶段边界)。一个 phase 是一个会话内部的一段工作([grilling](https://www.aihero.dev/ai-coding-dictionary/grilling)、implementation、QA),两个 phase 之间的边界才是 "这份 context 怎么处置?" 这个问题唯一该出现的地方。阶段之中没有可决策的事:继续,或者把剩下的拆成 [subagents](https://www.aihero.dev/ai-coding-dictionary/subagent)。

| 选项 | 何时选择 |
| --- | --- |
| **Continue** | 下一个阶段想要这一阶段的逐字内容,或者你还有 [smart zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone) 可用。这是唯一把会话保留为 [primary source](https://www.aihero.dev/ai-coding-dictionary/primary-source) 的动作,所以先排除它 |
| **`/clear`** | 身后的一切都可丢弃。棋盘上最便宜的走法,而且如果你判断错了就是单向的 |
| **[handoff](https://aihero.dev/skills-handoff)** | 有东西必须迁移:换一个新的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness)、换一个新目录、交给同事、或在阶段中分叉出一个侧任务 |
| **Subagent** | 任务足够内聚,可以在你 [away from the keyboard](https://www.aihero.dev/ai-coding-dictionary/afk) 时单独跑 |
| **`/compact`** | 以上都不是。默认值,经常落在这里 |

其中两个经常被用错,这就是路由器承载顺序而非清单的原因。`/handoff` 看起来像不同窗口之间的通用桥梁,而它不是:可携带性就是它所买的一切。`/compact` 位于决策树的底部而非首选,因为它上面的四个选项每一个都更便宜或更精确。

## Common questions

**不就是把技能按正确顺序排成一个清单吗?**
人们一直在 README 里要求这个东西。这个技能就是那份清单:它存在就是为了这个。一张静态表会写 `wayfinder → to-spec → to-tickets → implement → code-review`,而这在多数情况下都是错的,因为有意思的部分是分支:有没有代码库、构建是否跨会话、这个问题是交谈就能定夺的。诚实的代价是路由器是手工维护的,滞后于仓库。`/grilling` 和 `/resolving-merge-conflicts` 上线远早于路由器给它们命名。

**它告诉我有一半的技能没装。**
这是个已知 bug,尚未修复。路由器路由的多数技能都设置了 `disable-model-invocation: true`,意味着 harness 把它们从注入到 agent context 的技能列表中剔除。agent 把那份列表当作全量,然后报告它们缺失。一次报告的会话让它宣布整个 spec-and-tickets 流程都不存在,改路由到裸的 `/grilling` 和 `/tdd`。插件的 22 个技能中有 13 个带这个 flag,所以这是常态而非边缘。它们都装着。直接输入斜杠命令就行,或者查看 `.claude-plugin/plugin.json`,那是判断哪些技能已安装的权威依据。

**它描述的某个技能的行为,实际上那个技能并不那么做。**
也是真的,也没修。路由器回答的依据是自己给每个技能写的一句话摘要,而不是技能本身。一份详细报告在同一个会话中追踪到三处此类问题,其中包括凭借 "把这条对话线程变成一份 spec" 这句摘要建议跳过 [to-spec](https://aihero.dev/skills-to-spec):`to-spec/SKILL.md` 从未被打开。每一次它都只在用户提出异议之后才去核实,从未主动核实。在那里跳过 `to-spec` 意味着损失了一次真正的 seam 检查,而产出的 tickets 少算了工作量。当路由器对另一个技能做出有承重的断言时,让它先打开那个 `SKILL.md`。同样的原则适用于这张地图完全没有覆盖的问题,比如是否使用 [plan mode](https://www.aihero.dev/ai-coding-dictionary/agent-mode):那个答案是[模型](https://www.aihero.dev/ai-coding-dictionary/model)的推断,不是这里写下的东西。

**为什么它是散文,而不是一个编号清单?**
这是个合理的批评,已被作为一个 open issue 提出,理由是大部分路由是确定性的,而叙述形式让它难以扫读。没有任何东西阻止你要求压缩形式:"就给我那条序列"就给你那条序列。散文承载的是条件那一半:分支、在何处需要人做决策、以及步骤之间何时该 clear 或 compact。一张平铺的清单恰好把这一半丢掉了。

**它能把我自己的技能或别的作者的技能纳入路由吗?**
不能。已经有过三个独立的提案,要求路由器读本地的 `skills/` 目录,从已安装的技能里推荐。`ask-matt` 不是这种东西。它是一份手工维护的、覆盖一套技能的地图,对你写的或从别处装的技能一无所知。

**它让我去编辑一个 SKILL.md。**
这种建议常常是对的,却很少经得起持久化。有人问它如何让 [implement](https://aihero.dev/skills-implement) 关闭 ticket,得到的回答是给技能加一行,然后立刻发现了问题:`npx skills update` 会覆盖文件,而插件安装是只读的。把长期行为放进你自己的 `CLAUDE.md` 或 `AGENTS.md`,或者在调用时说。prompt 层的适配能挺过更新:把流程指向 Linear 而不是 GitHub、或者问它哪些 open ticket 可以并行,都是人们这样做的例子。

**它命名了一个我没有的技能,或者漏掉了我有的。**
先看看 changelog 里有没有改名再判断它消失了没有。`writing-great-skills` 改名为 [writing-for-agents](https://aihero.dev/skills-writing-for-agents) 且没有别名,`to-prd` 改名为 [to-spec](https://aihero.dev/skills-to-spec),`pathfinder` 改名为 [wayfinder](https://aihero.dev/skills-wayfinder)。有四个技能被直接退役,被吸收它们的技能接管:`ubiquitous-language`、`design-an-interface`、`qa` 和 `request-refactor-plan`。反过来就是上面提到的路由器自身的滞后。

## It's working if

- 它以命名下一步要输入的命令收尾,然后停在那里,而不是自己启动工作。
- 它给出的路由会提到在哪里 clear 或 compact context,以及在哪里需要你 review,而不是只列一份技能名清单。
- 在两个相近的技能之间,会指明选哪一个、为什么另一个不适合你。
- 它对另一个技能行为所做的任何承重断言,在 trace 里表现为它读了那个技能的 `SKILL.md`。
- 你在它返回的内容里认出了自己所处的处境,而不是最接近的通用场景。

## Where it fits

`ask-matt` 是一个 **standalone router**,位于整套技能之上。它从来不是任何一条链中的一个步骤;它指向每一条链,也是其他文档页链回的节点,这样它们都不必重画那张图。从这里你最常落地到 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)(主流程的入口),或 [triage](https://aihero.dev/skills-triage)(针对到达而非启动的工作的 on-ramp)。

在它所描述的技能之上,它是一个 [secondary source](https://www.aihero.dev/ai-coding-dictionary/secondary-source)。当路由器和某份 `SKILL.md` 冲突时,以 `SKILL.md` 为准。
