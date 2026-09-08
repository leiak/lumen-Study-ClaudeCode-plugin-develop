# 面向真实工程师的 Skills

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

这是我每天都在用的、用来做真正的工程（而不是 vibe coding）的 agent skills。

开发真正的应用是件难事。GSD、BMAD、Spec-Kit 这类方法试图通过把整个流程"接管"来帮助我们，但这样做会夺走你的控制权，让流程中的 bug 难以解决。

这些 skills 被设计得小巧、易于适配、可组合。它们与任何模型都能配合。它们基于数十年的工程经验。随便改、随便玩，把它们变成你自己的样子。祝你玩得开心。

如果你想跟进这些 skills 的变更，以及我创建的任何新 skill，可以加入我的 newsletter，与大约 60,000 名其他开发者一起：

[订阅 Newsletter](https://www.aihero.dev/s/skills-newsletter)

## 安装（30 秒搞定）

两种入口，两种理念。**[Claude Code 插件](https://code.claude.com/docs/en/plugins)** 把整套 skills 作为一个托管的、只读的 bundle 安装，我一发布你就能自动更新，所以你是"订阅"而不是"fork"。**[skills.sh](https://skills.sh/mattpocock/skills)** 则会把可编辑的 skill 文件复制到你的项目里，因此你可以动手改、把它们变成你自己的。选一种即可：两个都装会让你每条 skill 都装两遍。

### 1. 获取 skills

<details>
<summary><strong>Claude Code</strong></summary>

```bash
claude plugins install mattpocock-skills
```

或者，在一个会话内：

```
/plugin install mattpocock-skills
```

它已经收录在 Claude Code 官方市场中，所以无需先添加任何源，更新会自动推送。

</details>

<details>
<summary><strong>Codex 以及其他 agent</strong></summary>

```bash
npx skills@latest add mattpocock/skills
```

挑选你想要的 skill，以及要安装到哪些编码 agent 上。**安装器允许你选择要拿哪些 skill，请确保其中包含 `setup-matt-pocock-skills`。**

原生 Codex 插件已在路线图上（见 [`.agents/adr/0002-ship-as-a-claude-code-plugin.md`](./.agents/adr/0002-ship-as-a-claude-code-plugin.md)）。

</details>

<details>
<summary><strong>想折腾的人</strong></summary>

任意 agent（包括 Claude Code）都可以用同一个安装器：

```bash
npx skills@latest add mattpocock/skills
```

它会把 skills 以"普通文件"的形式写入你的仓库，归你所有、可以编辑。不会有任何悄悄发生的更新；想要我的最新改动时，用 `npx skills update` 拉取即可。

</details>

### 2. 运行 `/setup-matt-pocock-skills`

在你的 agent 里，每个仓库运行一次即可。它会：

- 询问你想用哪个 issue tracker（GitHub、Linear 或本地文件）
- 询问你在分诊（triage）工单时会打什么标签（`/triage` 会用到这些标签）
- 询问你希望把生成的任何文档保存在哪里

### 3. 搞定，可以开工了

## 这些 Skills 存在的理由

我打造这些 skills，是想解决我在 Claude Code、Codex 以及其他编码 agent 上看到的一些常见失败模式。

### #1：Agent 没有做我想要的事

> "没有人确切知道自己想要什么"
>
> David Thomas & Andrew Hunt，《The Pragmatic Programmer》

**问题**。软件开发中最常见的失败模式就是"不一致"。你以为开发者知道你要什么。然后你看到他们做出来的东西 —— 你才意识到他们其实根本没理解你。

在 AI 时代这件事依然如此。你和 agent 之间存在一道沟通鸿沟。解法是**访谈会（grilling session）** —— 让 agent 针对你正在做的东西向你提一系列细致的问题。

**解法**就是使用：

- [`/grill-me`](./skills/productivity/grill-me/SKILL.md) —— 用于非编码场景
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) —— 与 [`/grill-me`](./skills/productivity/grill-me/SKILL.md) 相同，但额外加了更多好东西（见下文）

这是我最受欢迎的几个 skill。它们帮助你在开工前就和 agent 对齐，并认真思考你正在做的变更。**每次**想做变更时都用它们。

### #2：Agent 太啰嗦

> 使用一门通用语言，开发者的对话和代码中的表达，都源自同一个领域模型。
>
> Eric Evans，《Domain-Driven-Design》

**问题**：项目伊始，开发者与软件所服务的人（领域专家）通常说的不是同一种语言。

我在与 agent 协作时也感受到了同样的张力。Agent 通常被丢进项目里、被要求自己去搞懂那些 jargon。于是它们用了 20 个词，而 1 个词就够。

**解法**是建立一套共同语言。它是一份帮助 agent 解码项目中 jargon 的文档。

<details>
<summary>
示例
</summary>

下面是一个 `CONTEXT.md` 的示例，来自我的 `course-video-manager` 仓库。哪一份更容易读？

- **改之前**："There's a problem when a lesson inside a section of a course is made 'real' (i.e. given a spot in the file system)"
- **改之后**："There's a problem with the materialization cascade"

这种简洁是一次次会话中累积出来的红利。

</details>

这套东西已经内建在 [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md) 里。它是一场访谈会，同时帮你与 AI 建立共同语言，并把那些难以解释的决策记成 ADR。

它到底有多强大，很难用语言说清。它可能是这个仓库里最酷的一项技术。试试看就知道了。

> [!TIP]
> 共同语言除了"减少啰嗦"之外还有许多好处：
>
> - **变量、函数、文件都按共同语言来命名**，命名保持一致
> - 因此，**代码库对 agent 来说更容易导航**
> - Agent 也**在思考上花更少的 token**，因为它掌握了一门更精炼的语言

### #3：代码跑不起来

> "始终走小步、谨慎的步。反馈的速率就是你的速度上限。永远不要接手一个太大的任务。"
>
> David Thomas & Andrew Hunt，《The Pragmatic Programmer》

**问题**：假设你和 agent 已经就"要做什么"对齐了。但当 agent **仍然**产出了糟糕的代码时，怎么办？

这时候就得看看你的反馈环了。如果对自己生成的代码究竟如何运行没有反馈，agent 就是在蒙眼飞行。

**解法**：你需要一套常见的反馈环：静态类型、浏览器访问、自动化测试。

说到自动化测试，红-绿-重构循环至关重要。先让 agent 写一个会失败的测试，然后再修测试。这能给 agent 一致的反馈水平，从而产出好得多的代码。

我写了一个 **[`/tdd`](./skills/engineering/tdd/SKILL.md) skill**，可以嵌入到任何项目里。它鼓励红-绿-重构，并给 agent 大量关于好测试与坏测试的指引。

在调试方面，我还写了一个 **[`/diagnosing-bugs`](./skills/engineering/diagnosing-bugs/SKILL.md) skill**，把最佳调试实践封装成一个逐阶段把关的纪律性循环。

### #4：我们搭出了一团乱麻

> "每天都要投入系统的设计。"
>
> Kent Beck，《Extreme Programming Explained》

> "最好的模块是深的。它们让大量功能可以通过一个简单接口来访问。"
>
> John Ousterhout，《A Philosophy Of Software Design》

**问题**：用 agent 搭出来的大多数应用都复杂、难以改动。因为 agent 能极大地加快编码速度，它们同样在加速软件熵增。代码库以空前的速度变得更复杂。

**解法**是一条通向 AI 驱动开发的全新路径：关心代码的设计。

这件事内建在这些 skills 的每一层里：

- [`/to-spec`](./skills/engineering/to-spec/SKILL.md) 会在生成 spec 之前先考考你，到底要碰哪些模块

更关键的是，[`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) 会扫描代码库、找出"深化（deepening）机会"、并把候选交到你手上。我推荐每隔几天就对你的代码库跑一次。它是一份"勘测"，而不是"救援"：在真正陈旧的代码库上它能给出真实的候选，但不会替你解开那团乱麻。

### 小结

软件工程的基本功比以往任何时候都重要。这些 skills 是我把这些基本功浓缩成可重复实践的最好尝试，帮助你发布职业生涯中最好的应用。祝你玩得开心。

## 参考索引

这些 skills 按一条轴划分：**谁能调用它们**。**用户调用（User-invoked）** 的 skill 只有当你手动键入（如 `/grill-me`）时才会被触发；它们的工作是"编排"。**模型调用（Model-invoked）** 的 skill 既可以由你手动触发，也可以在任务契合时被 agent 自动抓取；它们承载着可复用的纪律。用户调用的 skill 可以调用模型调用的 skill，但不能再调用其他用户调用的 skill。

### Engineering（工程）

我每天用来做编码工作的 skills。

**用户调用**

- **[ask-matt](./skills/engineering/ask-matt/SKILL.md)**：询问哪种 skill 或流程最适合你的情境。是本仓库"用户可触达"skills 的路由总览。
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)**：访谈会话，同时构建项目的领域模型 —— 就术语进行打磨，并就地更新 `CONTEXT.md` 与 ADR。
- **[triage](./skills/engineering/triage/SKILL.md)**：让 issue 沿着一个分诊角色状态机前进。
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)**：扫描代码库中的"深化"机会，以可视化 HTML 报告呈现，然后针对你选中的那一个开展访谈。
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)**：为本仓库的工程 skills 做配置（issue tracker、triage 标签、领域文档布局）。每个仓库在使用其他工程 skill 之前运行一次。
- **[to-spec](./skills/engineering/to-spec/SKILL.md)**：把当前对话整理成 spec，并发布到 issue tracker 上。不做访谈，只把你已经讨论过的内容合成出来。
- **[to-tickets](./skills/engineering/to-tickets/SKILL.md)**：把任何计划、spec 或对话拆成一组"示踪弹"工单，每张工单声明自己的阻塞边 —— 以本地文件形式，或在真实 tracker 上以原生阻塞链接形式存在。
- **[implement](./skills/engineering/implement/SKILL.md)**：根据 spec 或一组工单构建产物，在预先约定的接缝处驱动 `/tdd`，并在提交前以 `/code-review` 收尾。
- **[wayfinder](./skills/engineering/wayfinder/SKILL.md)**：规划一块巨大的、超过单个 agent 会话所能容纳的工作，以 issue tracker 上的一张"决策工单地图"展开，逐个解决，直到通往目的地的路径清晰可见。

**模型调用**

- **[prototype](./skills/engineering/prototype/SKILL.md)**：构建一个一次性的原型来回答某个设计问题 —— 状态/逻辑类问题用一份可共享的 HTML 文件，UI 类问题用同一路由下可切换的几个截然不同的 UI 变体。
- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.md)**：针对棘手 bug 与性能回归的纪律性诊断循环：构建一个会因本 bug 而变红的反馈环 → 最小化 → 提出假设 → 插桩 → 修复 → 加回归测试。
- **[research](./skills/engineering/research/SKILL.md)**：针对高可信度的"主源"调查一个问题，并产出一份带引用的 Markdown 文件写入仓库，由后台 agent 执行。
- **[tdd](./skills/engineering/tdd/SKILL.md)**：基于红-绿-重构循环的测试驱动开发。一次构建一个垂直切片，要么搭功能，要么修 bug。
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.md)**：积极地构建并打磨项目的领域模型：用词表挑战术语，用边界场景做压力测试，并就地更新 `CONTEXT.md` 与 ADR。
- **[codebase-design](./skills/engineering/codebase-design/SKILL.md)**：用于设计"深模块"的共同纪律与词汇：小接口背后藏大量行为，放置在干净的接缝处，可通过该接口测试。
- **[code-review](./skills/engineering/code-review/SKILL.md)**：自某个固定基线以来 diff 的两轴评审：**Standards**（是否符合仓库编码规范，外加一套 Fowler "坏味"基线）与 **Spec**（是否忠实地实现了来源 issue/spec），由并行子 agent 执行，以避免相互污染。
- **[resolving-merge-conflicts](./skills/engineering/resolving-merge-conflicts/SKILL.md)**：逐 hunk 处理进行中的 git merge 或 rebase 冲突，依据两侧"主源"回溯到意图来化解，然后收尾操作（绝不 `--abort`）。
- **[wizard](./skills/engineering/wizard/SKILL.md)**：生成一份交互式 bash 向导，带人走过那些只有人能完成的步骤：provisioning 基础设施、配置凭据或 CI secret、走过陌生的第三方 dashboard、运行一次性迁移或切换。

### Productivity（生产力）

通用工作流工具，与编码无关。

**用户调用**

- **[grill-me](./skills/productivity/grill-me/SKILL.md)**：针对计划或设计进行不依不饶的访谈，直到设计树的每个分支都被解决。
- **[handoff](./skills/productivity/handoff/SKILL.md)**：把当前对话压缩成一份交接文档，以便另一个 agent 接手继续。
- **[teach](./skills/productivity/teach/SKILL.md)**：跨多个会话教会用户一项新技能或新概念，使用当前目录作为有状态的教学工作区。
- **[to-questionnaire](./skills/productivity/to-questionnaire/SKILL.md)**：把"你独自回答不了"的决策，变成一份 Markdown 问卷交给那个能回答的人，可异步填写，也可一起开会时填。它访谈的是"发送动作"（寄给谁、需要拿回什么），而不是主题本身。
- **[wait-what](./skills/productivity/wait-what/SKILL.md)**：在对方一句话没接住的瞬间触发。Agent 会用你缺失的上下文、用通俗英语重新讲一遍，并使用你 `CONTEXT.md` 里的词汇。

**模型调用**

- **[grilling](./skills/productivity/grilling/SKILL.md)**：针对计划、决策或想法对用户进行不依不饶的访谈，直到设计树的每个分支都被解决。这是 `grill-me`、`grill-with-docs`、`triage`、`wayfinder` 与 `improve-codebase-architecture` 背后的可复用访谈原语。
- **[writing-for-agents](./skills/productivity/writing-for-agents/SKILL.md)**：如何为 agent 写文档：skills、AGENTS.md/CLAUDE.md，以及任何 agent 通过指针取到的文档。
