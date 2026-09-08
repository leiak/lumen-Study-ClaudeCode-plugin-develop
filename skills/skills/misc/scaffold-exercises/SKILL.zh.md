---
name: scaffold-exercises
description: 创建带有 sections、problems、solutions 和 explainers 的练习目录结构，并通过 lint 检查。当用户希望搭建练习脚手架、创建练习存根，或设置新的课程 section 时使用。
---

# 搭建练习脚手架

创建能够通过 `pnpm ai-hero-cli internal lint` 检查的练习目录结构，然后使用 `git commit` 提交。

## 目录命名

- **Sections**：`exercises/` 下的 `XX-section-name/`（例如 `01-retrieval-skill-building`）
- **Exercises**：section 下的 `XX.YY-exercise-name/`（例如 `01.03-retrieval-with-bm25`）
- 章节编号 = `XX`，练习编号 = `XX.YY`
- 名称使用 dash-case（小写字母、连字符）

## 练习变体

每个练习至少需要以下子文件夹之一：

- `problem/`：带有 TODO 的学生工作区
- `solution/`：参考实现
- `explainer/`：概念性材料，不含 TODO

在创建存根时，除非计划另有指定，否则默认使用 `explainer/`。

## 必需文件

每个子文件夹（`problem/`、`solution/`、`explainer/`）都需要一个 `readme.md`，要求：

- **不能为空**（必须包含真实内容，哪怕只是一行标题也行）
- 不能包含损坏的链接

在创建存根时，请创建一个最小的 readme，包含标题和描述：

```md
# Exercise Title

Description here
```

如果子文件夹中包含代码，那么它还需要一个 `main.ts`（超过 1 行）。但对于存根来说，只有 readme 的练习就可以了。

## 工作流

1. **解析计划**：提取 section 名称、练习名称和变体类型
2. **创建目录**：为每个路径使用 `mkdir -p`
3. **创建 readme 存根**：每个变体文件夹创建一个包含标题的 `readme.md`
4. **运行 lint**：使用 `pnpm ai-hero-cli internal lint` 进行验证
5. **修复任何错误**：迭代直到 lint 通过

## Lint 规则摘要

linter（`pnpm ai-hero-cli internal lint`）会检查：

- 每个练习都有子文件夹（`problem/`、`solution/`、`explainer/`）
- 至少存在 `problem/`、`explainer/` 或 `explainer.1/` 中的一个
- 主子文件夹中存在 `readme.md` 且不为空
- 没有 `.gitkeep` 文件
- 没有 `speaker-notes.md` 文件
- readme 中没有损坏的链接
- readme 中没有 `pnpm run exercise` 命令
- 每个子文件夹都需要 `main.ts`，除非该子文件夹只有 readme

## 移动/重命名练习

在重新编号或移动练习时：

1. 使用 `git mv`（而非 `mv`）来重命名目录，这样可以保留 git 历史
2. 更新数字前缀以保持顺序
3. 移动后重新运行 lint

示例：

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## 示例：从计划中创建存根

给定一个类似下面的计划：

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

可以这样创建：

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

然后创建 readme 存根：

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
