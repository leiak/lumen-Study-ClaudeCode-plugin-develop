---
"mattpocock-skills": patch
---

新增 `implement-spec` skill（in-progress 桶，用户调用）。它接住一份 spec 与它的工单，并把整个工程驱动到一张 PR：工单被读成一张带阻塞边的任务图，因此实现用的子 agent 在后台的 worktree 上针对"已就绪的 frontier"并行运行；一个合并子 agent 把每一份折回 PR 分支；最后在 PR 标为 ready 之前用 `/code-review` 收尾。
