---
name: setup-ts-deep-modules
description: 将 dependency-cruiser 接入 TypeScript repo，使每个 package 都是一个 deep module：实现隐藏在子文件夹中，只能通过其入口文件访问。由用户调用。
disable-model-invocation: true
---

# 设置 TS Deep Modules

将本 repo 中的每个 package 都设为**deep module**：小接口后面隐藏着大量行为。一个 package 的公共面是其**入口点（entry points）**（package 根目录下的文件），其子文件夹中的一切都是隐藏的。本 skill 安装 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 以及使入口点成为唯一通道的规则，然后验证这些规则确实生效。

关于术语（deep module、interface、seam、depth），请调用 Skill 工具并传入 "codebase-design"，并在全程使用它的语言。

## 所强制形成的形状

```
src/packages/
  <name>/
    index.ts        ← 入口点（公共）。从外部导入这个。
    client.ts       ← 另一个入口点。Package 可以暴露 SEVERAL 个。
    lib/            ← 实现：对外部隐藏，可自由相互导入。
    tests/          ← 同地放置的测试与 fixtures（一个子文件夹，因此是私有的）。
```

公共面是 package 的**根文件**，而不是某个指定的 `index.ts`。按惯例，实现放在 `lib/`，测试放在 `tests/`，这让每个 package 都拥有相同的两个子文件夹形状。规则本身是通用的：*任意*子文件夹中的*任何*内容都是私有的，因此你永远不需要为了新增文件夹而扩展配置。

四条规则，全部为 `error`：

1. **入口点边界**：package 外部的代码（应用代码或其他 package）只能导入该 package 的入口点（其根文件），绝不能导入其子文件夹中的任何内容。
2. **包内自由**：package 自身的文件可自由地相互导入。
3. **测试通过入口点**：`<pkg>/tests/` 下的文件可以导入任何 package 的入口点和它们自己的 `tests/` fixtures，但绝不能导入任何 package 子文件夹内部的代码（连它们自己 package 的也不行）。跨 package 的集成测试可以；深度导入则不行。
4. **无循环**：不存在依赖循环。

**入口点，而非 barrel（汇总导出）**。由于公共面是*每一个*根文件，一个 package 可以暴露几个小入口点（`index.ts`、`client.ts`、`server.ts`），而不是把所有内容都汇总到单一的巨型 `index.ts` 中。不鼓励那种重新导出整棵子树的 barrel 文件；让入口点保持小巧，将实现隐藏在子文件夹中。

分层（哪些 package 可以依赖哪些）是另一个*不同*的问题，本 repo 在配置中将其作为注释掉的存根留给后续补充。

## 步骤

### 1. 检测环境

- **包管理器**：`pnpm-lock.yaml` → pnpm，`yarn.lock` → yarn，`bun.lockb` → bun，否则 npm。在下面的所有命令中都使用它（`pnpm`/`yarn`/`npm run`/`bunx`）。
- **Packages 根目录**：如果存在 `src/`，则使用 `src/packages`，否则使用 `packages`。如果 repo 已经有明显不同的约定，请与用户确认所选路径。
- **现有配置**：检查是否存在 `.dependency-cruiser.*` 文件。如果已存在，**不要**覆盖它：将四条规则与选项合并进去，并告知用户你添加了什么。

**完成条件**：包管理器、packages 根目录、是否存在现有配置，这三项都已确定。

### 2. 安装 dependency-cruiser

使用检测到的包管理器将 `dependency-cruiser` 安装为 devDependency。

**完成条件**：`dependency-cruiser` 出现在 `devDependencies` 中。

### 3. 编写配置

将 [`dependency-cruiser.config.cjs`](./dependency-cruiser.config.cjs) 复制到 repo 根目录，命名为 `.dependency-cruiser.cjs`。将 `PACKAGES_ROOT` 设为步骤 1 中检测到的根目录。规则是基于路径深度且与扩展名无关的，因此无需做其他调整。

**完成条件**：`.dependency-cruiser.cjs` 存在，`PACKAGES_ROOT` 设置正确，且四条禁止规则都已就位。

### 4. 将其接入检查流程

- 添加一个 `lint:boundaries` 脚本：`depcruise <packages-root>`（或 `depcruise src`）。
- 将其纳入 repo 的总检查命令中，即那个已经会运行 typecheck 的命令（例如 `check` / `ci` / `validate` 脚本）。**不要**改动 `tsconfig` 或添加 path aliases。
- 如果不存在总检查脚本，请添加 `lint:boundaries` 并告知用户在 CI 中纳入它。

**完成条件**：`lint:boundaries` 存在，并且与 typecheck 一起作为同一命令的一部分运行。

### 5. 搭建示例 package

在 `<packages-root>/example/` 中提交一份 copy-me 模板：

- `index.ts` 是一个入口点。导出一个函数，该函数委托给一个内部文件（这样该 package 看上去是明显的*deep*，而非直通的）。
- `lib/impl.ts`：**子文件夹**中的一个内部文件，由 `index.ts` 导入，外部无法访问。
- `tests/example.test.ts` 仅导入 **`../index`**（一个入口点），并针对公共函数进行断言。

告知用户这是一个用于复制或删除的入门模板。

**完成条件**：示例 package 存在，通过根目录入口点暴露其行为，并将 `impl` 隐藏在子文件夹中。

### 6. 证明规则确实生效

这是整个 skill 的完成标准：一个无法在违反时失败的配置毫无价值。

1. 运行 `lint:boundaries`。在干净的示例上它必须**通过**。
2. 临时向 `tests/example.test.ts` 中添加一个深度导入（例如 `import { thing } from "../lib/impl"`）。再次运行 `lint:boundaries`；它必须**失败**，并提示 `tests-through-entrypoints`。
3. 撤销该深度导入。再运行一次，它必须**通过**。

**完成条件**：你已经观察到一次通过、一次在深度导入上失败、然后再次通过。如果步骤 2 没有失败，说明规则没有正确接入，请在完成前修复。

### 7. 将约定写入文档

在 packages 文件夹中编写一份 `README.md`（即 `<packages-root>/README.md`，紧邻其约束的 packages），内容包括：`src/packages/<name>/` 的布局（入口点在根目录，`lib/` 放实现，`tests/` 放测试）、"只能通过一个 package 的入口点（其根文件）进行导入"，以及如何运行 `lint:boundaries`。**明确不鼓励 barrel 文件**：暴露几个小入口点，而非通过单一的 index 重新导出整棵子树。保持在 copy-me 片段之外，加上每条规则各一段简述。

然后从 repo 的 agent 指令文件（如果存在 `CLAUDE.md` 则使用它，否则使用 `AGENTS.md`，若两者都不存在则创建 `AGENTS.md`）添加一个**上下文指针**指向它。一行就够，例如 `Packages are deep modules: see [src/packages/README.md](./src/packages/README.md) before adding or importing one.` 这正是让 agent 能发现这条边界规则而不是被绊倒的方式。

**完成条件**：`<packages-root>/README.md` 存在且不鼓励 barrel，且 repo 的 `CLAUDE.md`/`AGENTS.md` 链接到它。

## 备注

- 配置中的 `$1` 反向引用（dependency-cruiser 的 group 匹配）正是让一个 package 能触及自身内部、而外部却无法触及的原因。不要把它们扁平化为针对每个 package 的单独规则。
- 公共与私有由**深度**决定：一个 package 的根文件是入口点；任何子文件夹中的内容都是私有的。约定俗成的子文件夹是 `lib/`（实现）和 `tests/`，但规则本身并没有硬编码它们：任何子文件夹都是私有的，因此新增文件夹永远不需要改动配置。新增入口点只是新增一个根文件（无需 barrel）。
- Packages 是**扁平**的：在根目录下仅有一层直接的子目录。一个 package 的内部可以按你所需任意嵌套；一个 package 不能包含另一个 package。
- 使用 `.cjs`（而非 `.js`），这样即便在 `"type": "module"` 的 repo 中，配置的 `module.exports` 也能工作。
