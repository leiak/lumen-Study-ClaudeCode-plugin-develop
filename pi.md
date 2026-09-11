# git@github.com:earendil-works/pi.git

**earendil-works/pi** 采用 npm workspaces 构建了一个高度模块化的 TypeScript Monorepo (单体代码库)。从 `package.json` 可以看出，该项目在工程化、供应链安全和底层性能优化上做了极其严格的设计。

以下是该项目核心组件与技术栈的详细拆解：

### 1. 核心架构与工作区 (Monorepo Workspaces)

项目将 Agent 的不同能力拆分为独立的包，通过 `packages/*` 进行管理，构建脚本中暴露了以下核心模块：

* **`chord`**：底层运行时，负责服务组合、RPC通信和状态管理。
* **`tui`**：终端用户界面（Terminal UI）渲染库，甚至包含针对不同操作系统 (`darwin`, `linux`, `win32`) 的原生构建脚本 (`build:native`)。
* **`ai`**：大语言模型 API 统一接入层，包含模型数据的生成与校验 (`generate:models`, `hydrate:model-data`)。
* **`agent` & `coding-agent**`：Agent 的核心逻辑与面向最终用户的 CLI 应用。
* **`protocol` & `client`/`server**`：通信协议层与客户端/服务端实现。
* **`session-backends/sqlite-node`**：使用 SQLite 作为 Agent 会话状态和数据的持久化后端。
* **Extensions (扩展组件)**：包含针对特定厂商或环境的集成，如 `custom-provider-anthropic`、`sandbox` 和 `gondolin`（本地微型虚拟机路由）。

### 2. 核心构建与执行链

* **Node.js 运行环境**：要求严格，`"node": ">=22.19.0"`，且声明为 `"type": "module"`，全面拥抱原生 ESM 模块。
* **TypeScript 构建**：
* 使用 `typescript` (5.9.3) 进行静态类型检查。
* 引入了 `@typescript/native-preview`，表明项目可能在尝试或依赖 Node.js 原生的 TypeScript 运行能力，以减少编译开销。
* 使用 `tsx` 快速执行 TypeScript 脚本。


* **ESBuild**：使用 `esbuild` (0.28.2) 作为打包工具，提供极速的模块打包。
* **测试框架**：未使用 Jest 或 Vitest，而是直接使用 Node.js 内置的原生测试运行器 (`node --test`) 执行测试。

### 3. 代码质量与供应链安全

该项目对依赖控制和代码规范的要求达到了企业级安全标准：

* **Biome (`@biomejs/biome`)**：全面替代了 Prettier 和 ESLint，作为统一的代码格式化、Linting 和类型检查工具，执行速度极快。
* **Husky**：通过 `prepare: husky` 挂载 Git Hooks，在提交前自动拦截不合规的代码或意外的锁文件更改。
* **极致的依赖校验 (自定义 Scripts)**：
* `check:pinned-deps` / `check:runtime-deps`：校验生产依赖是否被严格锁定（防投毒）。
* `check:shrinkwrap` / `check:install-lock`：生成并校验 shrinkwrap 文件，确保 npm 用户的传递依赖也被绝对锁定。
* `check:ts-imports` / `check:entry-graphs`：检查内部模块导入的正确性和依赖图谱。



### 4. 关键底层与第三方集成

* **`@anthropic-ai/sandbox-runtime`**：集成了 Anthropic 官方的沙盒运行时环境，用于让 Agent 在安全隔离的沙盒中执行代码和终端命令（对应 `extensions/sandbox` 工作区）。
* **`protobufjs` (在 overrides 中锁定版本)**：结合 `protocol` 工作区和 RPC 架构，表明项目底层使用了 Protocol Buffers (Protobuf) 来定义系统各组件（或服务端/客户端）之间的高效序列化通信协议。
* **`shx`**：一个跨平台的 Shell 命令工具，确保 `clean` 等构建脚本在 Windows/Linux/macOS 上表现一致。