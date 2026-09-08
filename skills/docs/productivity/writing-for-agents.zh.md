## What it does

`writing-for-agents` 是你为面向 agent 的文档所写的 reference:一个技能、一份 `AGENTS.md` / `CLAUDE.md`、一份 [spec](https://www.aihero.dev/ai-coding-dictionary/spec)、一个 runtime prompt、一份 README、任何一份 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 读的文档。打包方式不同;写作不:同样的杠杆让每一个都可预测,所以 agent 每次跑取的是同一份 *process*,而不是产出同一个输出。

它的默认动作是 deletion,而不是 explanation。让 agent 给另一个 agent 写指令,它把大多数词花在解释 [模型](https://www.aihero.dev/ai-coding-dictionary/model) 已经知道的东西上。这些行中的每一行都是一个 **no-op**,付出 [上下文](https://www.aihero.dev/ai-coding-dictionary/context) 而不改变任何行为。这份 reference 是找到它们的 lens,而这就是为什么它在一份你已经有的文档上挣回的价值,跟在一份空文件上一样多。

在 v1.1 之前它叫 `writing-great-skills`。这次改名追的是它一直是的东西:几乎没有东西是技能专有的。仅关于技能的那些机制(frontmatter、模型调用 vs 用户调用、router 技能)在一份被链接的 `SKILL-MECHANICS.md` 里披露,你只在面前的文档是一份技能时才读它。

## When to reach for it

输入 `/writing-for-agents`,或者当你在创建或编辑一个技能、或修改 `AGENTS.md` 或 `CLAUDE.md` 时,agent 自己调用它。

agent 读的其他一切手工调用它:你的 docs、specs 和 [tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)、系统与 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk) prompts。测试是一道问题:agent 读这份吗?而它怎么出现在它面前并不重要,无论是一个指针命名它、一个人粘贴它、还是它就坐在仓库里,都没关系。对于弄清楚一个代码库实际上包含什么,用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs);这份 reference 治理的是一份文档读起来如何,而不是它知道什么。

## The two loads

整份 reference 转动的想法是一对每个文档和指针都要花的预算:

- **Context load**:总是加载的材料在 agent 窗口上的成本:一条 `AGENTS.md`、一份技能描述、任何无论是否触发每[轮](https://www.aihero.dev/ai-coding-dictionary/turn)都坐在 context 里的东西。
- **Cognitive load**:在你身上的成本,也就是哪些文档存在以及何时该触达哪个。你是 index。不是要最小化的成本:它是 human agency 的代价。

一旦你用这两份负载想,大多数写作决定(拆还是不拆、内联还是披露、指针还是 push)变成在不同地方做的同一个权衡。

## The levers

- **[Context pointers](https://www.aihero.dev/ai-coding-dictionary/context-pointer)**:留在 context 里那份 reference,它命名 context 之外的材料并编码何时去取。一份技能描述和一份 `AGENTS.md` 里命名文档的行是同一对象;指针的 *措辞*,而非它的目标,决定 agent 有多可靠地顺着它去取。
- **Information hierarchy**:从文件内步骤、到文件内 reference、到一份指针之后的披露 reference 的梯子。**[Progressive disclosure](https://www.aihero.dev/ai-coding-dictionary/progressive-disclosure)** 是沿梯子往下走的那一步,让顶层保持清晰。
- **Completion criteria**:每一步 done-condition 的清晰度和要求,以及那份要求驱动的 **legwork**;对 **premature completion** 的防御。
- **Leading words**:一个已经在模型预训练里的紧凑概念(*tight*、*red*、*tracer bullet*),agent 在跑那份文档时用它来思考。它锚定两次:在正文里的执行,在指针里的调用。
- **Pruning**:单一真相源、相关性、以及按句对 **duplication**、**sediment** 和 **sprawl** 应用的 no-op 测试。

## Common questions

**`/writing-great-skills` 去哪了?**
就是这个技能,v1.1 改名。实践者早就把它指向 `AGENTS.md`、docs、specs、tickets 和 runtime prompts,在名字追上之前;结构、leading words 和 pruning 结果被证明是任何一份 agent 读的文本的工艺。没有别名。用新名字重装。

**"Writing for agents":所以是 agent 在写?**
反过来。你是作者;agent 是读者。这就是这个 genre 全部的难处:你在为一个已经读过一切的读者写,所以解释是浪费,而精度是全部工作。

**我不能直接让 agent 替我写吗?**
可以,而且它会产出某种冗长的东西。放任的话模型会解释它已经知道的东西,且它不会自己应用 no-op 测试或伸手去拿 leading word。把这份 reference 用在那份草稿上:一个 review pass 是它大部分价值的落点。

**我让 agent 修剪一份文档,它砍掉了功能。**
被告之 "streamline" 的 agent 优化长度,因为长度是它们看得到的东西。No-op 测试是行为上的,不是审美上的:删掉那行,然后问 agent 的行为是否变了。当一句话失败,把整句删掉,而不是从它里面剪词,而通过运行那份文档而不是争论来解决关于它的分歧。

**我怎么知道它什么时候 done?**
当它 work,而你再也找不到 duplication、sediment 或 no-ops 时。这里没有自动 eval;检查是一次手工运行加一份失败模式词汇作诊断。当一份文档行为不端时,那份词汇同样是修复工具:先命名失败模式,然后修那个。

**这应该放在 `CLAUDE.md` 还是别处?**
问你想付哪份 load。`CLAUDE.md` 无条件地加载进每个[会话](https://www.aihero.dev/ai-coding-dictionary/session);指针背后的材料只有指针自己的那行成本,直到它触发。十次里只一次用得上的东西,在另外九次就在付 context load。

**我需要为每个新模型重写我的文档吗?**
通常不必,而过度拟合到一个模型本身就是陷阱。为新模型更新通常也是一次 no-op pass,而不是重写。

**我的技能只在我当初搭它的那个精确任务上 work。**
常见路径(做一次工作,然后让 agent 把它写成技能)在那个 run 上过索引,而 exemplars 出来太具体。把那次 run 留作证据,然后有意识地抽象:剥掉属于那个仓库和那些文件的东西,然后为那一类任务写。

**英语不是我的母语。我会失去 leading-word 的优势吗?**
不会。找出那个把最多的行为塞进最少的 [tokens](https://www.aihero.dev/ai-coding-dictionary/token) 的词,是这份 reference 替你做的事。这是它为之存在的事情之一。

## It's working if

- 文档越变好越变短,而你会惊讶怎么剩那么少。
- 你能指着一条 leading word,看它在不止一个地方做事。
- 任何东西都不被说两遍,以任何形式。Duplication 是一份文档从未被测试的最可靠征兆。
- 只有一条分支需要的 reference 坐在一份指针之后,而不是在主文件里。

## Where it fits

这是一份随时可用的独立 reference。它在链里没有邻居,因为它坐落在整套之下而不是在任何单一技能旁边:这里的每个技能都是对着它写的,而其他技能留下的文档(一份 `CONTEXT.md` 和它的 ADR、一份 spec、一张 ticket)正是它一旦 agent 必须读它时所治理的文本。当你不确定哪个技能或 flow 适合一项任务时,[ask-matt](https://aihero.dev/skills-ask-matt) 在整套技能中路由。
