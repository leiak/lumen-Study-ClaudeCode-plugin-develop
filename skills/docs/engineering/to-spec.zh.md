## What it does

`to-spec` 把刚刚发生的那场对话变成一份 **[spec](https://www.aihero.dev/ai-coding-dictionary/spec)**,并把它作为单个 issue 发到你的 issue tracker。

它不盘问你。等到你动用它时,决定已经做完,所以它合成已知的东西(从对话、从代码库、从你的 `CONTEXT.md` 和 ADR),而不是开启一轮新的提问。spec 是已经做出的决定的一份记录,而不是做新决定的地方。

## When to reach for it

你通过输入 `/to-spec` 来调用它;[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自行调用它。

当构建对一个 agent [会话](https://www.aihero.dev/ai-coding-dictionary/session)来说太大,且必须跨越多个会话存活时,使用它。这就是全部触发条件:

| 你在哪 | 该跑什么 |
| --- | --- |
| 你还没决定任何东西 | 先 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 决定完了,而且工作能装进一个[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window) | [implement](https://aihero.dev/skills-implement):跳过 spec |
| 决定完了,而且工作跨多个会话 | `/to-spec`,然后 [to-tickets](https://aihero.dev/skills-to-tickets) |
| 一张 [wayfinder](https://aihero.dev/skills-wayfinder) map 已清 | `/to-spec #<map_issue>` |

## Prerequisites

`to-spec` 把 spec 作为一份 issue 发布,所以 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须先为这个仓库配置好一个 tracker 和 triage-label 词汇。两种都行:像 GitHub 这样的真实 tracker,或者 `.scratch/` 下的本地 Markdown 文件,后者开箱支持。

## The spec is a decision record

Spec 存在的原因是上下文窗口会结束。你在 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 时敲定的所有东西(解法的形状、你争论过的选择、你刻意拒绝的)都在一场即将被清空的对话里。Spec 就是穿越那场清空存活下来的东西。

所以它不验证任何东西,也不决定任何东西。它把已决定的事情捕获下来,用你自己的项目词汇,这样一个干净的会话能在不让你重解释的前提下把工作接过去。Spec 中任何你其实从未说过的断言,都是一个缺陷。

## Seams before prose

在它动笔之前,`to-spec` 先勾勒出该特性将要被测试的 **seams**,并与你核对。它偏好已存在的 seam 而不是新 seam,并取它能取的最高 seam:一段变更上跨所有部分的 seam 理想数是 1。

那些被同意的 seam 接着流转。[tdd](https://aihero.dev/skills-tdd) 只在事前约定的 seam 处工作,而 [code-review](https://aihero.dev/skills-code-review) 相对 spec 审查 diff,所以一个没人同意过的 seam 会在 review 中以一条发现的形式出现。这种绑定是间接的:它穿过这份文档运行,这正好是为什么 seam 对话值得在这里认真对待,而不是推迟到实现时。

## Common questions

**`/to-prd` 去哪了?**
就是这个技能,v1.1 改名。"Spec" 现在是唯一贯穿的术语,旧的 `to-prd` slug 已死;请用新名字重装。替代旧词汇的是 *spec* 和 *tickets*:spec 是目的地和固定它的决策,[tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 是到达那里的执行步骤。如果你要转向,删掉没完成的 tickets,保留 spec。

**为什么 spec 会得到 `ready-for-agent` 标签?我不想让 agent 照着它实现。**
该标签的意思是 "no further triage needed":这份文档足够完整,agent 能从中工作。它是输入的指定,不是工单。但如果你跑轮询 `ready-for-agent` 的 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk) agent,这个区分对它们不可见,它们会高兴地在一次 run 里尝试构建整份 spec,而不是去接 ticket 切片。这是该技能被报告最多的粗糙边。在它改变之前,在你的 AFK agent 的 prompt 里显式排除父 spec,或者在 `/to-tickets` 跑完之后把那个标签剥掉。

**为什么不直接从 grilling 到 `/to-tickets`,跳过 spec?**
多数情况你该这么做;spec 只在多会话工作上挣回它那一步。它值得付钱的地方在于 tickets 是可丢弃的,而 spec 不是:每个 ticket 都被调整成适合装下一个全新上下文窗口,会被删掉或关闭,而 spec 作为这些 ticket 背后推理的单一所在留着。在一次单会话的变更上,这什么都买不到,而且你已经付了一个额外的合成步骤,在那里 [模型](https://www.aihero.dev/ai-coding-dictionary/model) 可能漂移。走 grilling → `/implement`。

**我刚做完一份 wayfinder map。我该喂它什么?**
主 map issue:`/to-spec #<map_issue>`,而不是单独的决策 ticket。[wayfinder](https://aihero.dev/skills-wayfinder) 产出的是决策而非可交付,散布在一张 map 上;`to-spec` 是把那些合并成一份可构建文件的步骤。把 map 直接喂给 [implement](https://aihero.dev/skills-implement) 会丢掉那次合并。只在工作量确实小时,才直接走实现。人们确实跑过这条简化流水线并报告 work;多出来的两步换来一份显式的 spec 工件,审阅者或同事可以读,这在不那么单干时更重要。

**这份 spec 是给我审的,还是只给 agent 的?**
多数情况下是给 agent 的,而且读起来就是那样:完整、密集、引用密集。值得你看的是 seam 和 out-of-scope 段,因为这两处是错判最容易抓、最晚才发现代价最贵的地方。从头到尾通读是一份真实的抱怨,且没有 summary 模式:诚实的答案是,如果 spec 让你惊讶,grilling 太浅,而不是 spec 太长。

**在 tickets 开始之后,我应该把 spec 冻起来,还是让 agent 重写它?**
没有任何东西让它们同步,所以实践上它是你在那一刻所知的一份快照,而它在实现第一次教会你某些东西时就开始过时。在工作上线之后,把它当一次性的。需要超过它的工件是你的 `CONTEXT.md` 和 ADR;如果实现过程中学到的东西值得留下,它属于那里,而不是一份被编辑过的 spec。

**我的工作是重构或模块边界,而不是特性。这个模板合适吗?**
不那么合适,这是个已知局限。模板重度依赖 user story,这对架构工作是错的形状:你最终编出没人要的故事,围绕其实是接口和不变量的决策。改为靠 implementation-decisions 和 testing-decisions 两段,并让那些持久的架构性调用作为 ADR 通过 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 落地,而不是试着让 spec 承载它们。

**它会查 tracker 找相关工作,或引用它遵守的 ADR 吗?**
两个都不会。它读并尊重覆盖它所触及领域的 ADR,但不会链接它们,且在起草前不会在 tracker 里搜重叠的 issue,所以一份 spec 可能悄悄重复别人已经提的工作。如果那个区域很忙,你自己先搜一下 tracker。

**`/to-tickets` 读不到我的 spec:它一直在截断。**
非常大的 spec 可能长得超过一个 tracker issue 能干净返回的东西,没有本地副本可以兜底。修法是上下文卫生:在 `/to-spec` 和 `/to-tickets` 之间不要 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 或 [compact](https://www.aihero.dev/ai-coding-dictionary/compaction)。在同一个窗口里跑它们,spec 根本不需要被重新拉取。

## It's working if

- 它开始动笔,而不是问你一轮新的问题。
- 它在你写之前把 seam 提给你,并尽可能少提。
- 它用你项目的名词回来,而不是通用产品管理模板。
- 它里面的每项决定都是你能记得自己做过的。没有为了填段而凭空捏造的东西。
- Out-of-scope 段里有真实内容:你拒绝的那些通常是这个页面最有用的几行。

## Where it fits

`to-spec` 是主构建链中的一步,且只在它的多会话分支上:

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review
```

它的上游邻居是 [grill-with-docs](https://aihero.dev/skills-grill-with-docs),做这个技能只负责记录的决定;[wayfinder](https://aihero.dev/skills-wayfinder),其完成的 map 在这里合流到链上。下游,[to-tickets](https://aihero.dev/skills-to-tickets) 把 spec 切成供 [implement](https://aihero.dev/skills-implement) 构建的 tracer-bullet tickets。当你不确定哪个技能或流程合适时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
