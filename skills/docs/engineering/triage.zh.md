## What it does

`triage` 处理你项目 tracker 上的 issues,把每一个穿过一个由 **triage roles**(一个 category role 和一个 state role)组成的小型状态机,并留下一个 agent-ready 的简介、一个给 reporter 的具体问题、或一条带记录原因的已关闭 issue。

它只用于 **你没创建的 issues**。原始 bug 报告、涌入的功能请求、一份突然送来的外部 PR:从外部、以 reporter 留下的任何形状进入 tracker 的工作。[Tickets](https://www.aihero.dev/ai-coding-dictionary/ticket) 由 [to-tickets](https://aihero.dev/skills-to-tickets) 产出的已经在构造上对 agent 就绪,对它们跑 `triage` 在最好的情况下也是浪费。规则是扁平的:`/triage` 只用于涌入的 issues,不用于你自己创建的 issues。

把它和手工打标签区分开的是第二件事:它推荐并等待。它会告诉你它的 category 和 state 决定以及理由,加上它在代码库中发现的东西,在你指示之前什么都不应用。

## When to reach for it

你通过输入 `/triage` 并以自然语言描述你想要什么来调用它。[agent](https://www.aihero.dev/ai-coding-dictionary/agent) 不会自己调用它。"Show me anything that needs my attention","let's look at #42","move #42 to ready-for-agent"。

| 你拥有 | 该去哪里 |
| --- | --- |
| 一个满是别人原始报告的 tracker | `/triage` |
| 自己的一点想法,什么都还没写下来 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 一段已定的对话要转成 [spec](https://www.aihero.dev/ai-coding-dictionary/spec) | [to-spec](https://aihero.dev/skills-to-spec) |
| 一份 spec 要拆成对 agent 就绪的 tickets | [to-tickets](https://aihero.dev/skills-to-tickets) |
| 一个已确认的 bug,需要根本原因,而不是一个标签 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |

## Prerequisites

`triage` 读写你的 issue tracker,所以 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须先配置好该 tracker 和它的标签词汇。下面的 role 名是 **canonical** 的;你 tracker 里的标签字符串可能不同,而 mapping 是 setup 提供的。如果你的 tracker 已经在用 canonical 名,那就没东西要映射,也没东西要配置。

Tracker 配置还决定外部 pull request 是否算一个请求面,以及谁算外部。这个 flag 默认关,不再是一个 setup 问题,所以如果你想让 PR 纳入,在 `docs/agents/issue-tracker.md` 里翻它。

## The state machine

每个被 triage 的条目最终带恰好一个 category role 和一个 state role。两个 categories:`bug`(某样东西坏了)和 `enhancement`(新特性或改进)。五个 states:

| State | 意味着 |
| --- | --- |
| `needs-triage` | 你需要评估它。未打标签的 issue 通常最先落在这里。 |
| `needs-info` | 等待 reporter。他们回复时回到 `needs-triage`。 |
| `ready-for-agent` | 完整指定,附一份 agent brief。一个 [AFK](https://www.aihero.dev/ai-coding-dictionary/afk) agent 可以接它。 |
| `ready-for-human` | 同样的 brief,加上为什么它不能被委派:判断、外部访问、手工测试。 |
| `wontfix` | 关闭,记录原因。 |

这就是全部词汇,"恰好一个 state role" 这个不变式是让查询保持简单的原因。它也是该 [技能](https://www.aihero.dev/ai-coding-dictionary/skill) 被请求最多的区域:用户为"已指定但阻塞在另一个 issue 上"的工作、为由未来触发门控的 `deferred` 工作、以及为终态的 `implemented` state,都请求过一个第六 state。都没发布。见下面的问题。

`wontfix` 拆成三种,差别很重要,因为只有其中一种会写入知识库:

| 为什么关 | 会发生什么 |
| --- | --- |
| 已实现 | 一条 comment 指向它已经存在的地方。什么都不写进 `.out-of-scope/`,因为它是已构建的特性,不是被拒的,而且把它归档到那里会污染去重检查。 |
| 被拒的 bug | 礼貌解释,然后关闭。 |
| 被拒的 enhancement | `.out-of-scope/` 下的一份文件,从关闭的 comment 里链接,然后关闭。 |

`.out-of-scope/` 是每个被拒绝的 **concept** 一份 Markdown 文件,而不是每个 issue 一份,写成一份短的设计文档,而不是数据库行:什么被拒绝、为什么、问过它的所有 issue。`triage` 在评估任何东西之前读整个目录,并按概念匹配而不是按关键字,所以 "night theme" 会匹配到 `dark-mode.md`。当它命中一个匹配,就把旧的决定摆出来,问你是否仍然这么想,而不是从头重新审理这个请求。

## Verify before you brief

在任何 [grilling](https://www.aihero.dev/ai-coding-dictionary/grilling) 之前,`triage` 检查那个说法是否真的成立。对一个 bug,它从 reporter 的步骤复现它。对一份 PR,它把分支 checkout 出来并跑相关测试。然后它报告三件事中的哪一件发生了:已确认,带代码路径;未能复现;或者细节不足以尝试,这本身就是最强的 `needs-info` 信号。

它在同一遍对代码库多跑两项检查:**redundancy**(这是否已经实现,按领域概念而不是 reporter 的措辞搜索?)和 **prior rejection**(`.out-of-scope/` 是否已经说不?)。两者都很便宜,两者命中时都会产出 `wontfix`。

这一切的存在是为了把一个工件做好:**agent brief**,即当 issue 移到 `ready-for-agent` 时作为结构化 comment 发布的那份。一旦发布,这份 brief 就是契约,而原始报告只是上下文。Brief 被写成 **耐久** 而非精确,因为一份 issue 可以在 `ready-for-agent` 里坐几周,期间代码从下面滑过去。所以它们命名类型、签名和行为契约,从不引用文件路径或行号。一份被确认的复现比猜测做出的 brief 要强得多。

## A PR is an issue with attached code

在 tracker 把外部 pull request 当作请求面的地方,它们走过同一台机器,同样的 categories、同样的 states、同样的转移。States 只是对 diff 读法不同:`ready-for-agent` 意味着一份 brief 已附,agent 应该接着走代码的下一步,`ready-for-human` 意味着已准备好让人 merge。Brief 在一份 PR 上描述的是对已有 diff 还剩什么要做,而不是如何从零搭这个东西。

Discovery 只 surface *外部* PR,因为合作者进行中的分支不是 triage 工作。那个 filter 仅用于 discovery,显式命名的 PR 不管作者是谁都会被 triage。一个粗糙的边:GitHub 模板的外部 PR 列出命令向 `gh pr list` 请求一个 `gh` 不暴露的 `authorAssociation` 字段,所以写下来的命令直接失败([#468](https://github.com/mattpocock/skills/issues/468))。

## Common questions

**我跑了 `/to-spec` 和 `/to-tickets`,现在这些 tickets 在那里未被 triage。我要跑 `/triage` 吗?**
不要。它们已经对 agent 就绪,因为 `to-tickets` 发布时会应用 `ready-for-agent` 标签,正是为了让 AFK runner 不需要再过一遍就能接住。撞到这个的用户已经跑过 spec flow,看到输出上有 `needs-triage`,并发现他们的 AFK runner 忽略了一切。`triage` 是为从外部到达的工作准备的入口;spec flow 是为你发源的工作准备的车道。它们在 `ready-for-agent` 处相遇,而不是更早。

**既然有 `to-spec` → `to-tickets` → `implement` flow,`triage` 还有意义吗?**
只有当你有涌入的工作时。`triage` 早于那条主干而存在,做的是不同的工作:它是别人提的 issue 的那条车道。如果 tracker 里所有东西都来自你自己的规划,你很少打开它。如果你维护任何公开的东西,或你的团队给你提 bug,它是前门。主要用途是接收外部贡献者的 issue 的开源仓库。

**Agent 试图应用 `ready-for-agent`,而 `gh` 说标签不存在。**
已知 open bug ([#616](https://github.com/mattpocock/skills/issues/616))。`setup-matt-pocock-skills` 把标签词汇写入 `docs/agents/triage-labels.md`,但不在你的 tracker 里创建标签。用 `gh label create` 或 tracker 的 UI 自己一次性创建那五个 state 标签和两个 category 标签,然后就停了。该 issue 上挂着一个尚未合并的社区修复分支。

**五个 state 不够:那 blocked、deferred、或 implemented 呢?**
这是该技能被报得最多的缺口,以三种形态出现。一个已完全指定但在等另一个 issue 关闭的 issue ([#139](https://github.com/mattpocock/skills/issues/139)),那里 reporter 的抱怨是 `ready-for-agent` 在那里 "technically true",但具有误导性,所以 agent 会接它然后撞墙。由触发门控的、打算做但目前还不能行动的 future work ([#297](https://github.com/mattpocock/skills/issues/297))。以及 "已实现,等待验证" 的终态,没有它,AFK runner 会把完成的 ticket 重新排队。Matt 已经同意 blocked 这种情形是真的,且对名字尚无定论(`blocked` 对 `paused`)。什么都没发布。人们使用的变通是在 category 旁边放一个 repo-local 的额外标签,canonical state 槽由某个诚实的东西占着,代价是该技能不知道它。一个社区的衍生走得更远,加了 `needs-slicing`、`tracking` 和 effort 标签。这能用,但那是他们的,不是技能的。

**这和 `/diagnosing-bugs` 有何不同?**
这里的验证步骤刻意做得很浅(足够回答 "这是真的吗,大致住哪里"),不是为了找根本原因。当一个 bug 在几分钟内无法从 reporter 的步骤复现,诚实的走法是 `needs-info`,或者你想现在追就用 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs)。两边技能的文本当前都没提对方;有用户找到了那条缝,它仍然 open。

**我能不能把整个 backlog 指向它,让它自己跑?**
你可以问,但看它在读什么。"show what needs attention" 那遍是一次便宜的 listing 用于 *挑选*,你挑一个,然后它在你挑的那一个上收集完整[上下文](https://www.aihero.dev/ai-coding-dictionary/context)。跨二十个 issue 跑它,agent 会悄悄把那份便宜的 listing 当作它的证据基线回退,返回 issue bodies 但不返回 comments。一位用户正好撞到这个:三个 issue 已经带了一条 comment 写 "already fixed, recommend closing",而三张全部拿到了新鲜的 agent briefs。如果你想要批量跑,显式声明 comments 必须按 issue 读。

**它能在 Linear 或 GitHub Issues 之外用吗?**
能,tracker 是配置,而不是硬编码假设,人们在它上面跑过 Linear(经 `linear` CLI)、GitLab,以及 `.scratch/` 下的纯 Markdown 文件。一种常见切分是 Linear 用于 issues 和规划,GitHub 用于代码和 PRs:说 "issue tracker" 的技能映射到 Linear,说 "PR" 的技能映射到 GitHub。在本地 markdown tracker 上,有一个 open 模板 bug,生成的文件可能带两遍验收标准,顶层一遍,在 agent brief 里又一遍([#200](https://github.com/mattpocock/skills/issues/200))。

## It's working if

- 它碰的每个条目最终恰好带一个 category role 和一个 state role,既不为零,也不为两个冲突的 state。
- 它给你一个带理由的推荐,然后停下,而不是重新打标签然后继续。
- bug 被复现,或 PR 被 checkout 并跑过,然后才有任何东西到达 `ready-for-agent`。
- 它写的 brief 命名类型和行为,不含文件路径和行号。
- 一个六个月前被拒绝的请求回来了,它说出来了,并引用旧的理由,而不是重新 triage。
- 它发布的每条 comment 都以 `> *This was generated by AI during triage.*` 开头。

## Where it fits

`triage` 是一个 **on-ramp**,而不是主链中的一步。主 flow 从你有的一个主意跑起(grill、spec、tickets、implement、review),`triage` 是为到达的工作而准备的并行车道。它在同一个地方合流:一张带着 brief 的 `ready-for-agent` 标签 issue,正是 [implement](https://aihero.dev/skills-implement) 会去接的,就像接 [to-tickets](https://aihero.dev/skills-to-tickets) 的一张 ticket。当一个请求在被简介之前需要被磨利,`triage` 同时跑 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling),一轮接一轮提问,所以决策在做出时就落到 `CONTEXT.md` 和 ADR。当你不确定你处在哪条车道时,[ask-matt](https://aihero.dev/skills-ask-matt) 来路由。
