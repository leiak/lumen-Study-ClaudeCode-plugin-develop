---
"mattpocock-skills": patch
---

把跨 skill 的调用统一为显式的"调用 Skill 工具"指令，不再使用裸的 `/skill` 风格散文，覆盖 `code-review`、`diagnosing-bugs`、`grill-with-docs`、`grill-me`、`improve-codebase-architecture`、`tdd`、`to-spec`、`to-tickets`、`triage`、`wayfinder`。

- 一份 skill 在散文中点另一份 skill 的名字（如"运行 `/grilling` skill"）并不会可靠地让它被加载。这就是 `grill-with-docs` 被报告最多的那个问题背后的"被记录的粗糙边"。直接点出工具（`Call the Skill tool with "grilling"`）意在提高命中率。去掉开头的 `/` 也让这条指令对 harness 中立：不再假设 Claude Code 的触发语法。
- 一段需要多份 skill 的步骤，现在按多次调用来表述（"Call the Skill tool twice, for `grilling` and `domain-modeling`"），而不是"一次调用传两个名字"。
- 把这条约定记入 `.agents/invocation.md`，供后续 skill 遵循。
