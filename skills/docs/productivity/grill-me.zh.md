## What it does

`grill-me` 接受一个 **loose idea** 并盘问你,直到你能 commit 它。你不需要一份已打磨的计划来开始;产出它正是 [会话](https://www.aihero.dev/ai-coding-dictionary/session) 的目的。它以 **rounds** 提问:每一轮是整个 **frontier**(每道其先决条件你已经敲定了的问题),所以你永远不会被问到一个依赖于一个它还没听到答案的问题。

它是 **[stateless](https://www.aihero.dev/ai-coding-dictionary/stateless)** 的。它不写文件,也不留 workspace。它唯一留下的是这个 idea 的一个更锐利的版本,在你自己脑子里。

## When to reach for it

你通过输入 `/grill-me` 来调用它;[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自己调用它。在一段 **新对话** 中启动它,而不是叠在一个你已经让 agent 写过的计划之上。

一旦你有一个值得认真对待的想法(一项功能、一个产品方向、一项业务决策、一段写作),且远远在你想清楚它涉及什么之前,就该使用它。含糊不是等的理由;含糊是这个会话吃的东西。如果你已经能精确地说明那个东西,你不需要 grill 它。

要选三种 grilling 技能中的哪个,取决于你面前的东西:

- **任何地方,任何东西**:`grill-me`。它不需要仓库,不写文件,而且主题不必是代码。
- **一个要对齐的代码库**:[grill-with-docs](https://aihero.dev/skills-grill-with-docs)。同样的盘问,但 [stateful](https://www.aihero.dev/ai-coding-dictionary/stateful):它读你的代码,并把它学到的东西留在 `CONTEXT.md` 和 ADR。
- **大到无法塞进一个会话**:[wayfinder](https://aihero.dev/skills-wayfinder)。它把这项工作绘制为一张 map,并在其中跑 grilling 会话。

不要开 [plan mode](https://www.aihero.dev/ai-coding-dictionary/agent-mode)。Plan mode 把 agent 推向急于产出一份计划,而那与留在盘问中正好相反。

## It's a conversation, not an interview

技能问问题,但 **你** 拥有 scope。那部分是人们漏掉的,也是把一段想法变成决策的会话和产出自信废话的会话分开的地方。

失败模式是 **passivity**:四十道题顺口答 "agreed, agreed, agreed",带着一份 agent 写而你点头的计划走出来。它感觉有产出,因为它很长。其实什么也没决定,而结果背负了一份它没挣来的确定性。

主动意味着引导。对一道问到低于你需要的细节的问题,把它顶回去。Scope 漂移时说出来。回答 "I don't know" 并当真。这个技能被建来辅助工程师,而不是替代他:产出物跟踪你回答的质量,而不是被问的问题数。

相反的错误真实但更罕见:停留在盘问里太久,永远到不了代码。

## Grillable and ungrillable

一些问题可以通过谈论回答。另一些不能,无论多少 grilling 都到不了那里。

"一份长表单还是三页?" 和 "这种交互应该感觉如何?" 是 **ungrillable**:它们需要东西来反应。当你撞上其中一个,停止 grilling。用 [prototype](https://aihero.dev/skills-prototype) 搭出用完即弃的版本,看一眼,然后用一句话回答。

在不可 grill 的问题上谈过去,是会话膨胀的地方。Agent 一直换措辞,你一直猜,scope 增长以填满不确定性。

## It's working if

- 你不同意某件事。一场你没有反对的会话是你不需要的会话。
- 问题以少数几轮到达,而不是一次长长的滴灌,且后续轮明显基于你早先说的内容。
- 你最终到达了一个你没预料到的地方,因为一道题浮现出一项你一直在隐含做出的决定。
- 结尾时你能向一个不在场的人辩护每项选择。

## Common questions

**我应该预期多少道题,我怎么知道它什么时候结束?**
数 rounds,不是题。四十六题跨四轮是一场普通的会话。它在 frontier 为空时结束:每条分支都到过,没有东西被默默假设。

**它问了我两百道题。哪里出错了?**
通常是 scope 太大。让 agent 先把工作拆成更小的块,然后 grill 每一块。很长的会话也会漂进 **[dumb zone](https://www.aihero.dev/ai-coding-dictionary/smart-zone)**,在那里 [上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window) 已经满到问题开始变差。

**我能不能回到一次一道题?**
可以。把它加到你的全局 `CLAUDE.md` 里:

```
When grilling, ask one question at a time.
```

**如果我真的不知道答案呢?**
说出来。"I don't know" 是一个真实的回答,而一道你答不出来的题通常是一个信号,该做 prototype,而不是去猜。

**我在写 spec 之前要开一个新会话吗?**
不要。这次会话的价值就是你刚刚构建的[上下文](https://www.aihero.dev/ai-coding-dictionary/context)。把同一段对话直接交给 [to-spec](https://aihero.dev/skills-to-spec)。

**模型有关系吗?**
比大多数技能更有关系。Grilling 靠在 [模型](https://www.aihero.dev/ai-coding-dictionary/model) 自己的"系统如何崩溃"的直觉上,所以用你最好的那个。Implementation 多数情况下跟住上下文,对更便宜的模型也能容忍。

## Where it fits

`grill-me` 是一个 **standalone 你可以在任何地方、对任何东西跑**。Stateless 是它可移植的原因:无仓库、无 workspace、无 setup,而且不假设这个想法甚至是关于软件的。人们把它指向业务决策、写作、以及下一步做什么:任何在他们脑子里坐不住的东西。

那种可移植性是和 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 的全部区别,后者跑同样的盘问但读一个代码库来对齐,并把它学到的记录为 `CONTEXT.md` 和 ADR。两者都建在 [grilling](https://aihero.dev/skills-grilling) 原语上;`grill-me` 是用户调用的、什么也不带的前门。

如果你 grill 的东西最终确实是软件,你可以把同一段对话交给 [to-spec](https://aihero.dev/skills-to-spec) 并继续进 build flow(一个选项,而不是这个技能的目的)。当你不确定哪个 flow 合适时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
