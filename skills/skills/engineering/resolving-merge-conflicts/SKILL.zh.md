---
name: resolving-merge-conflicts
description: "当你需要解决进行中的 git merge / rebase 冲突时使用。"
---

1. **查看当前状态** 的 merge / rebase。检查 git 历史和冲突文件。

2. **找到主要来源** 对每个冲突。深入理解每个变更背后的原因以及最初的意图。读取 commit 消息，查看 PR，检查原始 issue / ticket。

3. **解决每个 hunk。** 尽可能保留双方的意图。在不兼容的情况下，选择与 merge 明确目标匹配的那一个并记录权衡。**不要**发明新行为。始终解决；永远不要 `--abort`。

4. 发现项目的**自动化检查**并运行它们，通常是 typecheck，然后是 tests，然后是 format。修复 merge 破坏的任何东西。

5. **完成 merge / rebase。** stage 所有内容并 commit。如果正在 rebase，则继续 rebase 过程直到所有 commit 都 rebase 完成。
