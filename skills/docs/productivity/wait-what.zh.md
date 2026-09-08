## What it does

`wait-what` 是当一条消息没讲明白时你输入的东西。[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 然后重新陈述它刚说的内容。它添加你缺失的 context,用 plain English 写,并用你项目 `CONTEXT.md` 里的词汇。

这个技能只有三行长。这就是设计,而不是一份未完成的草稿。对抗冗长的技能失败在变长:一份四百行的 concision 技能仍然留下 [模型](https://www.aihero.dev/ai-coding-dictionary/model) 冗长,因为模型读的是体量,不是请求。这一份带一个精确的 leading word 而已。

## When to reach for it

你通过输入 `/wait-what` 来调用它。Agent 不会自己调用它,而且它不该。只有你知道你什么时候停止跟上。

在你注意到自己在 skim 的那一刻就使用它。Agent 漂进了它自己造的 jargon、堆了五个缩写、或解释了一个你从未见过前提的决策。它修复你已经身处的那段对话。要从源头阻止 jargon 出现,用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs),它一开始就把共同语言建好。

## The name is the mechanism

Leading word 是 **wait**。"Be concise" 是关于 agent 输出的指令,模型通过剪词服从它,让你更跟不上。**Wait** 是关于 *你* 的状态。它说理解在这里失败。一个听到 "be brief" 的 agent 写电报。一个听到 "wait, you lost me" 的 agent 后退一步并解释。

那个差别就是整个技能。每一个流行的冗长修复命名 *输出*:`/tldr`、`/no-fluff`、`/talk-normal`。模型矫枉过正进一种洞穴人语域,更短而没更清楚。命名 *听者*,一次同时要两半:更少的词 **和你缺失的 context**。

这个技能说的是重新陈述 *那个*,而不是 "上一条消息"。让你跟丢的通常比一段更大,所以 agent 决定要回退多远。

## It plugs into the language you already have

正文复用你的全局 `CLAUDE.md` 和项目 `CONTEXT.md` 里已有的 leading words。ASD-STE100 Simplified Technical English 设定了语域。Ubiquitous language 提供名词。这个技能、`CLAUDE.md` 和 `CONTEXT.md` 伸手够同一份 [tokens](https://www.aihero.dev/ai-coding-dictionary/token),所以调用它不是一条新指令。它是一份对 agent 已经同意的指令的提醒。

如果你没有 `CONTEXT.md`(也没有 `CONTEXT-MAP.md` 指向在当前上下文里的那一份),该技能仍然 work。你只丢掉领域词汇那一半。

## It's working if

- 重新陈述 **更短且更清楚**,而不是更短且更钝。
- 它加入你缺失的前提,而不是只删词。
- 项目名词替换被造的词。你 `CONTEXT.md` 里的那些术语回来。
- 你能连用两次,它不会退化成简短。

## Where it fits

你可以在任何点、任何对话、任何其他技能里面使用 `wait-what`。它事后修一条消息。真正的治法是预先同意的共享语言,那就是 [grill-with-docs](https://aihero.dev/skills-grill-with-docs):一场 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 会话边进行边跑 [domain-modeling](https://aihero.dev/skills-domain-modeling),这样你们两个用的词落到你的 `CONTEXT.md`。如果你不确定哪个技能适合此刻,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
