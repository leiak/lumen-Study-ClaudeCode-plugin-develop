## What it does

`resolving-merge-conflicts` 在一次进行中的 git merge 或 rebase 中工作,hunk by hunk,然后跑项目自己的检查,并以一个 commit 完成操作。

它拒绝把冲突当作文本问题。在碰一个 hunk 之前,它把每一边都追到它的 **[primary source](https://www.aihero.dev/ai-coding-dictionary/primary-source)**(commit message、PR、原始 issue),所以它是在两种意图之间选择,而不是在两段文本之间,并且在它们相容的地方保留两边。当它们真正不相容时,它选与 merge 的既定目标匹配的那一边,并命名 trade-off。它不会为了掩盖冲突而发明新行为,并且 `--abort` 不是它拥有的选项:merge 总会带到一份完成的 commit。

## When to reach for it

输入 `/resolving-merge-conflicts`,或在任务匹配时 [agent](https://www.aihero.dev/ai-coding-dictionary/agent) 自动调用它。

在 git 已经停在它自己无法解决的冲突上时使用它。它被限定在你面前的冲突,不是它两边的任何东西:

| 你的处境 | 技能 |
| --- | --- |
| Merge 或 rebase 中,树里有冲突标记 | 本技能 |
| Merge 完成,但某些东西表现不对,你看不到原因 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |
| 在规划如何切工作以减少分支碰撞 | 都不是:见下面关于并行工作的问题 |

## Primary sources over `ours` and `theirs`

这个技能要消灭的失败模式是按旗标解决:`--ours`、`--theirs`、或手工删除看起来不那么重要的那个块,于是标记消失了,build 编译过。这种解决在语法上可能完美,却仍然悄悄丢掉别人有意识做的修改。

你没法保留一个没读过的意图。所以工作从历史开始(commits、PRs、[tickets](https://www.aihero.dev/ai-coding-dictionary/ticket)),然后才到 diff。循环里另一步为同一个原因存在:该技能找到仓库自己的[自动化检查](https://www.aihero.dev/ai-coding-dictionary/automated-check)并在 commit 前跑它们,因为 merge 是 git 里最容易产出满足两个分支、却两个测试都不过的代码的地方。

## Common questions

**Claude Code 自己已经解决冲突解决得挺好了。为什么这需要一个技能?**
附加价值在于 "find the primary sources" 和 "run feedback loops" 这两步,否则每次都得手工 prompt。一个未经 prompt 的 agent 通常会从 diff 本身产出一个貌似合理的解决就停在那里。这个技能的价值在于那两步,它不会让 agent 跳过:阅读每一边为何存在,以及之后跑那些检查。这相对一个好的[模型](https://www.aihero.dev/ai-coding-dictionary/model)是一个微弱的 margin,而它就该是这样:至少一位读者预言过,随着模型改进,这整技能会变成 no-op。

**我该不该让并行的 agent 远离同一批文件,以从源头避免冲突?**
多数情况不必。在并行任务之间分区文件成本高于回报,因为 agent 足够擅长 merge conflict,所以权衡没那么严苛。值得保留的一条纪律是,先做大重构。一次大改名落在十条分支已经分叉之后,才是始终昂贵的那个情形。

一份关于并行 worktrees 的用户报告里有一个 caveat:当兄弟[会话](https://www.aihero.dev/ai-coding-dictionary/session)各自在自己的 tree 里搭一个 ticket,merge 回主干最好由写过那次改动的那一个会话来做,因为它已经知道意图。把所有人的冲突在末尾批给一个 agent,恰好把本技能步骤 2 必须重建又必须去走的[上下文](https://www.aihero.dev/ai-coding-dictionary/context)扔掉了。

**为什么永不 `--abort`?**
Abort 丢掉解决工作,然后下次你尝试时让你回到同一个冲突,毫无变化。这个技能为那种 merge 必然会发生的场景而写。如果你已经决定它不该发生,那是在调用之前做的决定,不是循环里的分支。

## It's working if

- Agent 在解决时引用 commit messages、PRs 或 issues,而不只是 diff hunks。
- 每个 hunk 最终要么保有两边的行为,要么伴有一条明确注释,说明丢了什么以及为什么。
- 结果中没有什么是两边都不曾拥有的东西。
- Typecheck、tests 和 format 都被定位并在 commit *之前* 跑绿,而不是在你注意到某样东西坏了之后。
- 你停在一棵干净的树上,操作已完成,包括多 commit rebase 中剩下的每一个 commit。

## Where it fits

一个随时可用的独立工具,不依赖任何其他技能:它在 git 停下时启动,在树干净且已 commit 时结束。它唯一的真正邻居是 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs),它在一个 merge 干净解决、但 merge 后的代码行为不对时接手:那是诊断问题,不是冲突问题。它完全坐落在主"从主意到上线"流程之外,所以 [ask-matt](https://aihero.dev/skills-ask-matt) 是关于它前后跑什么的地图。
