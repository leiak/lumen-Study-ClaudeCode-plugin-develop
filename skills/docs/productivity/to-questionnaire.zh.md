## What it does

`to-questionnaire` 把一项你自己定不下来的决策变成一份 **questionnaire**:一份 Markdown 文档,你交给那个持有你缺失的东西的那个人,让他们异步填,或者你们两个在一次会议上过一遍。

它盘问你关于 **send**,而非主题本身。在这里盘问主题是徒劳的:不了解主题正是你写信给别人的原因。所以它问你两件你总能回答的事(这是给谁的,以及你从他们那里需要什么回来),并把文档里的每个问题都对准两者之间的 **gap**。

## When to reach for it

你通过输入 `/to-questionnaire` 来调用它;[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自行调用它。

当一项决策被卡在活在一个别人脑中的知识上时调用它:一个客户、一位领域专家、拥有业务规则的一位 exec、一位你不在一组里的同事。答案实际在的地方决定你想要哪个技能:

| 答案在…… | 调用 |
| --- | --- |
| 你自己的脑子里,还没磨利 | [grill-me](https://aihero.dev/skills-grill-me) |
| 代码库 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 别人的脑子里 | `to-questionnaire` |
| 谁脑子里都还没有,这个问题需要东西来反应 | [prototype](https://aihero.dev/skills-prototype) |

常见情形是一次 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 会话卡住:浮现出来的一些不是由你来答。在同一段对话里跑 `/to-questionnaire` 把那些问题带走离线,然后把答案带回来继续。

## The send, not the subject

盘问是两次交换,然后停止。

- **这是给谁的?** 他们的角色、专长、跟你的关系。这固定语气和这份文档需要承载多少上下文:一个外部客户需要被定向,一个队友不需要。
- **你从他们那里需要什么回来?** 你自己无法解决的具体的决策或事实。这成为成品文档被衡量的清单:你命名的每一项都得到一道对它的题。

那之后一切就是 drafting。文件落在当前目录的 `to-questionnaire-<slug>.md`。没有 setup、没有 workspace,也没什么要配。

## The document

它被框成一份 **discovery questionnaire**(你缺 context,收件人持有它),那个框决定了它的形状:

- 一行 purpose,命名那份决策挂在上面,加一段短 context,给一个从来不在你脑子里的收件人。
- 问题按 **most-important-first** 排序,分组在主题标题之下,因为 async 意味着你可能只过一遍。
- 一个问题一个意思,从不是复合,问题下有一个 answer stub,只在某道题可能被误读时有一行 *why this matters*。
- 显式许可答 "I don't know":一个被标记的不确定性有用;一个读起来像事实的自信猜测没有用。
- 一个收尾的 catch-all:有什么我们没问到但我们应该知道的吗?

两件它刻意不是的。它不是 **branching**:问题是一份扁平的、分组的清单,而不是一棵在你答 A 时跳过 D 的树。它不是 **multi-recipient**:一次跑产出一份文件给一个人。

## Common questions

**它读我的 grilling 会话并从中抽问题吗?**
不是作为一个它自己的步骤。这个技能没有 ingest 阶段:它问 send,然后起草。让它在一次 grilling 会话之后 work 的原因是,你在同一段对话里跑它,所以 [会话](https://www.aihero.dev/ai-coding-dictionary/session) 已经在 [上下文](https://www.aihero.dev/ai-coding-dictionary/context) 里,而 drafting 能从中取用。在一个新会话里启动它,它对那次 grilling 一无所知;当你回答 "what do you need back?" 时,你会在自己重新供给主题。

**缺失的答案不全在同一个人那里。它能按收件人切分吗?**
不能。步骤一问 *the* recipient,单数,整份文档的语气和 context 都对向他们。如果三个人持有三个部分的答案,就为每个人跑一次,共三次。同一份文档内按学科或角色分问题是一项已被请求的扩展,但不是已交付的。

**问题之间有依赖吗:它根据更早的答案跳过段吗?**
没有。依赖型设计被探索过但没发布。输出是一份静态文档:主题分组、most-important-first、每题都活着。反对它的反对意见是公平的:一个 [模型](https://www.aihero.dev/ai-coding-dictionary/model) 比一个真实答案超前规划两到三道题以上就会规划得很糟,而一份 branching 文档不得不在每个答案之前规划所有题。

**如果收件人也不知道呢?**
文档告诉他们说。一份明确要求的 "I don't know" 和部分答案被显式请求,而一个被标记的不确定性比一个猜测更有价值,因为一份模糊的回答和一份自信但错的回答在回到你的 context 后看起来一样。

**它会把它发到任何地方(Slack、issue tracker、邮件)吗?**
不会。它在当前目录写一份 Markdown 文件并告诉你路径。投递是你自己的事:粘进 [ticket](https://www.aihero.dev/ai-coding-dictionary/ticket)、丢进一条 Slack 线程、附到一封邮件,或在一块共享屏幕上打开并现场过完。四种都被人手工连过。

**这不就是 batch 模式下的 `/grill-me` 吗?**
不是,且差别值得抓在手里。`grill-me` 已经以 **rounds** 问:整个 frontier 一次性,然后根据你的回答重新计算,所以 "一次给我所有问题" 这个需求在那里被满足。`to-questionnaire` 是关于另一根轴:不是问题如何投递,而是答案在谁的脑子里。自己更快地回答是 `grill-me`;从别人那里把它们拿出来是这个。

**我不能直接让 agent 做这事而不要一个技能吗?**
能,在它存在之前很多人都这么做了:`OPEN_QUESTIONS.md` 文件、发给客户的电子表格、一张 "needs more info" ticket 对应每个未答的问题。这个技能给你买两件事:盘问永远不会漂到主题上,以及文档以一种非技术收件人实际能填的形状出来。如果你已经有了一种能 work 的内部格式,诚实的答案是你不需要这个。

## It's working if

- 它问收件人和你需要什么回来,然后停止问。一道关于主题本身的问题是该技能脱轨了。
- 你命名为 "what I need back" 的每一项都能追到文件里的一道问题。
- 这些问题读起来像对准 *收件人* 所知道的东西,而不是你自己的 open questions 被原样抄下来。
- 你能把文件交给一个没参与这场对话的人,他们会知道为什么它到了他们那里,以及要在什么时候之前回复。
- 回来的答案是新一轮 grilling 的可用输入,而不是一组新问题。

## Where it fits

`to-questionnaire` 是一个随时可用的独立工具。它坐落在你自己知识的边界上,下一步是另一个人而不是另一个技能,多数时候是在 mid-flow,当规划卡在不是你来决定的事情上。

它的邻居是 [grill-me](https://aihero.dev/skills-grill-me),而两者按答案在谁那里分裂:grilling 挖你,questionnaire 挖别人。回来的东西是原材料:喂给下一轮 grilling,或者工作朝着一次构建走时喂给 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 或 [to-spec](https://aihero.dev/skills-to-spec)。当你不确定哪个技能适合此刻时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
