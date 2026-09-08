# Matt Pocock Skills

一套由 Claude Code 加载的 agent skills（斜杠命令与行为）。Skills 按桶组织，并由 `/setup-matt-pocock-skills` 产出的"每个仓库一份"的配置来消费。

## 语言

**Issue tracker（Issue 跟踪器）**：
托管仓库 issue 的工具：GitHub Issues、Linear、本地的 `.scratch/` markdown 约定、或类似工具。`to-tickets`、`to-spec`、`triage` 等 skill 会读写它。
_避免_：backlog manager、backlog backend、issue host

**Issue**：
**Issue tracker** 内一份被追踪的工作单元：一个 bug、任务、spec、或由 `to-tickets` 产出的切片。
_避免_：ticket（仅在引用外部系统的"ticket"叫法时使用，或用于 **Decision ticket**，见下文）

**Decision ticket（决策工单）**：
一个 `wayfinder` 单位：`wayfinder:map` 的一张子 **Issue**，承载一个**问题**，其答案是"做一项决策"，而不是"执行一片构建"。**decision** 这个限定词是它与实现工单的区别所在；`wayfinder` 引入了这个术语，随后使用 "ticket"。

**Triage role（分诊角色）**：
分诊过程中应用到 **Issue** 上的一个规范化状态机标签（如 `needs-triage`、`ready-for-afk`）。每个角色通过 `docs/agents/triage-labels.md` 映射到 **Issue tracker** 中的真实标签字符串。

## 关系

- 一个 **Issue tracker** 持有许多 **Issues**
- 一张 **Issue** 同时只承载一个 **Triage role**
- 一张 **Decision ticket** 就是一张 **Issue**（`wayfinder:map` 的子 issue）

## 标记出的歧义

- "backlog" 此前同时用来表示托管 issue 的**工具**和其中的**工作总量**。已解决：工具是 **Issue tracker**；"backlog" 不再作为领域术语使用。
- "backlog backend" / "backlog manager"。已解决：合并入 **Issue tracker**。
