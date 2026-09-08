# 领域文档

工程 skill 在探索代码库时应如何消费该仓库的领域文档。

## 探索之前，读取这些

- 仓库根目录下的 **`CONTEXT.md`**，或者
- 仓库根目录下的 **`CONTEXT-MAP.md`**（如果存在）：它指向每个 context 的一个 `CONTEXT.md`。阅读与话题相关的每一个。
- **`docs/adr/`**：阅读涉及你即将工作的区域的 ADR。在多 context 仓库中，还要检查 `src/<context>/docs/adr/` 中 context 范围内的决策。

如果这些文件中的任何一个不存在，**静默继续**。不要标记它们的缺失；不要建议预先创建它们。`/domain-modeling` skill（通过 `/grill-with-docs` 和 `/improve-codebase-architecture` 到达）在术语或决策实际被解决时按需懒创建它们。

## 文件结构

单 context 仓库（大多数仓库）：

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多 context 仓库（根目录下存在 `CONTEXT-MAP.md`）：

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← 系统级决策
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← context 特有决策
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## 使用词汇表的词汇

当你的输出命名一个领域概念（在 issue 标题、重构提案、假设、测试名称中）时，使用 `CONTEXT.md` 中定义的术语。不要漂移到词汇表明确避免的同义词。

如果你需要的概念尚未出现在词汇表中，那是一个信号：要么你在发明项目不使用的语言（重新考虑），要么存在真正的空白（为 `/domain-modeling` 记下来）。

## 标记 ADR 冲突

如果你的输出与现有 ADR 相矛盾，明确地揭示它而不是默默覆盖：

> _Contradicts ADR-0007 (event-sourced orders), but worth reopening because…_
