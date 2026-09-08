# In Progress（进行中）

Beta。这些 skills 故意公开：试试，告诉我哪里崩了。它们被排除在插件与顶层 README 之外，直到毕业到稳定桶；它们没有文档页；它们可能变化或消失，恕不另行通知。

插件不会给你这些。直接单条安装：

```bash
npx skills@latest add mattpocock/skills --skill=<name>
```

- **[loop-me](./loop-me/SKILL.md)**：通过多场会话，把你自己 grill 成可实施的工作流 spec，使用当前目录作为有状态的工作区。用户调用。
- **[writing-beats](./writing-beats/SKILL.md)**：把一篇文章塑造成一段节拍之旅，"自己挑下一步"风格。挑一个起始节拍，只写那一节拍，然后转向下一个，直到文章到达自然终点。
- **[writing-fragments](./writing-fragments/SKILL.md)**：一场访谈会话，把你当作片段（异质的写作 nuggets）的矿脉，把它们追加到一份文档里，作为未来文章的原材料。
- **[writing-shape](./writing-shape/SKILL.md)**：把一份"原材料"的 markdown 文件，一段一段地塑造成一篇文章，并在每一步论辩格式选择。
- **[claude-handoff](./claude-handoff/SKILL.md)**：把当前对话交接给一个新的后台 agent，让它立刻接手，并通过 `claude --bg` 注入一份交接摘要作为种子。用户调用。
- **[setup-ts-deep-modules](./setup-ts-deep-modules/SKILL.md)**：把 dependency-cruiser 接入一份 TypeScript 仓库，使每个 package 都成为一个深模块：实现藏在子文件夹里，只能通过入口文件触达，测试也只通过这些入口验证它。用户调用。
- **[implement-spec](./implement-spec/SKILL.md)**：在一个分支上完成整份 spec。把工单当作任务图而非列表来推进，让实现子 agent 沿着"已就绪的 frontier"并行运行以追求最大并发，最终以一张 PR 落地。用户调用。
- **[retro](./retro/SKILL.md)**：在一次会话之后，建议对编码 agent 的环境（steering 文件、编码规范、自动化检查、工具）做哪些改进。STUB：仅有设计笔记，尚未能跑。用户调用。
