---
name: implement-spec
description: "在代码中实现一份 spec。"
disable-model-invocation: true
---

你已获得一份 spec。该 spec 应该有关联的 tickets，描述如何实现该 spec。

目标是产出一个 PR，在单个分支上实现整份 spec。

这些 tickets 并不是步骤列表。它们是一个**任务图（task graph）**，其中 ticket 之间存在阻塞关系。这意味着始终存在一个可以立即领取的 tickets 的**前沿（frontier）**。

与子代理之间的通信应当精简。通信主要通过**上下文指针（context pointers）**进行：指向 spec、tickets、研究笔记和之前的 commit。不要重复已经可以通过指针获取的信息。

在可能的情况下，**实现者子代理（implementer subagents）**应当在后台运行，以实现**最大并发**。

## 步骤

1. 阅读 spec 和 tickets。读到足以理解任务图为止。

2. （可选）使用一个**探索子代理（exploration subagent）**来执行 tickets 所需的任何探索：相关代码库文件或外部文档。确保该探索子代理能够保存文件，它应当将其 markdown 笔记保存在 repo 之外的、未来的子代理都可以访问的目录中。这让**实现者子代理**可以专注于实现，而无需进行探索。

3. 创建一个分支，以及一个 draft PR。该 PR 应当标记为正在关闭（closing）spec issue 和相关的 tickets。

4. 使用**实现者子代理**来实现每个 ticket。每个实现者子代理应当在其自己的 worktree、自己的分支上工作。

5. 一旦某个**实现者子代理**完成，使用一个**合并子代理（merger subagent）**将其工作合并到 PR 分支。

6. 如果这改变了可领取 ticket 的**前沿（frontier）**，就启动更多的**实现者子代理**来处理新的 tickets。这样可以实现最大并发。

7. 一旦所有 ticket 都完成，对该 PR 分支运行 /code-review。在一个**实现者子代理**中修复 code review 提出的所有问题。

8. 将 PR 标记为可供审查。

9. 清理所有**实现者子代理**的 worktrees。
