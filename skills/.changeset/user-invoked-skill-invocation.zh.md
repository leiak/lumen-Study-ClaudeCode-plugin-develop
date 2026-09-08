---
"mattpocock-skills": patch
---

不让 skill 试图通过 Skill 工具去触达用户调用的 skill：修复那些违反 `.agents/invocation.md` 中"没有其他 skill 能调用它"这条不变式的跨 skill 引用，涉及 `to-spec`、`wayfinder`、`to-tickets`、`triage`、`code-review`、`diagnosing-bugs`。

- `to-spec`、`wayfinder`、`to-tickets`、`triage`、`code-review` 各带一条前置条件（"……run `/setup-matt-pocock-skills` if not"），PR #878 把它们改写成了字面的 `Call the Skill tool with "setup-matt-pocock-skills"` 指令。`setup-matt-pocock-skills` 是用户调用的，因此这五份 skill（无论是用户调用还是模型调用）都不能调用它。把它们全部改写成"让 agent 告诉人自己运行"的指令。
- `diagnosing-bugs` 的第 6 阶段复盘向 `improve-codebase-architecture`（同样是用户调用）做了同样的交接，且整个流程是自动、常常无人值守的，没有人在回路中捕捉那次失败的调用。与其弱化，不如直接去掉这个交接，因为它在实践中很少真正触发。第 6 阶段现在只保留"清理（Cleanup）"；机械化的清单不动。
- 给 `.agents/invocation.md` 的"它们之间的依赖"一节加了一段例外说明：`Call the Skill tool with "name"` 这条约定只在被命名的 skill 是模型调用时成立。这正是 PR #878 引入该节时，没有与上方八行之处的"用户调用/模型调用"不变式对齐所留下的缺口；这个缺口就是为什么这个 bug 扩散到了六个调用点而不是一个。

修复 #453。
