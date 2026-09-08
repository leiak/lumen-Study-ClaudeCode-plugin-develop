# Claude Code 当前已启用插件清单

> 文档生成时间：2026-09-08
> 数据来源：`%USERPROFILE%\.claude\settings.json`（`enabledPlugins`）与 `installed_plugins.json`
> 当前 CLI 模型：`MiniMax-M3`（settings 中 `ANTHROPIC_MODEL` / fast-mode 开启）

本目录（`D:\work-ai\0401-github-skill\`）在系统提示中被识别为本地 Claude Code 插件工程（`.claude-plugin/`、`.agents/`、`.changeset/`），其本身不作为“已启用插件”出现在用户级 `settings.json` 中——下表列出的是通过 `enabledPlugins: true` 启用的 6 个用户级插件。

---

## 总览

| # | 插件 ID | 来源 marketplace | 版本 | 作者 | 类别 |
|---|---|---|---|---|---|
| 1 | `superpowers` | `claude-plugins-official` | 5.1.0 | Jesse Vincent | 工作流 / TDD / 调试 |
| 2 | `andrej-karpathy-skills` | `karpathy-skills` | 1.0.0 | forrestchang | 编码准则 |
| 3 | `claude-code-setup` | `claude-plugins-official` | 1.0.0 | Anthropic | 配置建议 |
| 4 | `ai-platform-devops` | `personal`（本地目录 marketplace） | 1.0.0 | wma19 | DevOps / 平台规约 |
| 5 | `42crunch-api-security-testing` | `claude-plugins-official` | 1.5.7 | 42Crunch | API 安全 |
| 6 | `agent-skills` | `addy-agent-skills` | d187883b7d76 | Addy Osmani | 全生命周期工程方法论 |

合计：**6 插件 / 47 skills / 8 斜杠命令 / 4 子代理 / 2 SessionStart hook**。

---

## 1. `superpowers@claude-plugins-official` — v5.1.0

- **来源**：GitHub `anthropics/claude-plugins-official`
- **描述**：Core skills library for Claude Code: TDD, debugging, collaboration patterns, and proven techniques
- **许可证**：MIT
- **关键字**：skills、tdd、debugging、collaboration、best-practices、workflows
- **安装路径**：`C:\Users\wma19\.claude\plugins\cache\claude-plugins-official\superpowers\5.1.0`
- **安装时间**：2026-05-20

### Hooks
- `SessionStart`（matcher: `startup|clear|compact`）→ 调用 `${CLAUDE_PLUGIN_ROOT}/hooks/run-hook.cmd session-start`

### Skills（14 个）

| Skill | 触发场景 |
|---|---|
| `brainstorming` | 任何创造性工作之前——创建功能、构建组件、添加功能或修改行为前都必须先调用它，用于澄清意图、需求与设计 |
| `dispatching-parallel-agents` | 面对 2+ 无共享状态/无顺序依赖的独立任务时 |
| `executing-plans` | 在独立会话中执行已写好的实施计划，含评审检查点 |
| `finishing-a-development-branch` | 实现完成、所有测试通过后，决定如何集成工作（merge/PR/cleanup） |
| `receiving-code-review` | 收到代码评审反馈后、在实现评审建议前——尤其是反馈看起来含糊或技术上可疑时 |
| `requesting-code-review` | 完成任务、实现主要功能或合并前，验证工作是否符合要求 |
| `subagent-driven-development` | 在当前会话中执行含独立任务的实施计划时 |
| `systematic-debugging` | 遇到任何 bug、测试失败或意外行为时，在提出修复方案前 |
| `test-driven-development` | 实现任何功能或修复 bug 时，在写实现代码前 |
| `using-git-worktrees` | 启动需要与当前工作区隔离的功能工作或执行实施计划前 |
| `using-superpowers` | 任何对话开始时使用——建立如何查找与使用 skills 的方法 |
| `verification-before-completion` | 即将声明工作完成、已修复或通过时，提交或建 PR 前必须先跑验证命令 |
| `writing-plans` | 拥有规范或需求、准备进入多步骤任务时，触碰代码之前 |
| `writing-skills` | 创建新 skill、编辑现有 skill、部署前验证 skill |

> 该插件不提供斜杠命令，所有能力都通过 Skill 工具按需调用。

---

## 2. `andrej-karpathy-skills@karpathy-skills` — v1.0.0

- **来源**：GitHub `forrestchang/andrej-karpathy-skills`
- **描述**：Behavioral guidelines to reduce common LLM coding mistakes, derived from Andrej Karpathy's observations on LLM coding pitfalls
- **许可证**：MIT
- **安装路径**：`C:\Users\wma19\.claude\plugins\cache\karpathy-skills\andrej-karpathy-skills\1.0.0`
- **安装时间**：2026-06-05

### Skills（1 个）

| Skill | 触发场景 |
|---|---|
| `karpathy-guidelines` | 编写、评审或重构代码时，减少 LLM 常见编码错误（避免过度复杂、做出最小改动、暴露假设、定义可验证的成功标准） |

---

## 3. `claude-code-setup@claude-plugins-official` — v1.0.0

- **来源**：GitHub `anthropics/claude-plugins-official`
- **描述**：Analyze codebases and recommend tailored Claude Code automations such as hooks, skills, MCP servers, and subagents.
- **作者**：Anthropic
- **安装路径**：`C:\Users\wma19\.claude\plugins\cache\claude-plugins-official\claude-code-setup\1.0.0`
- **安装时间**：2026-06-05

### Skills（1 个）

| Skill | 触发场景 |
|---|---|
| `claude-automation-recommender` | 分析代码库并推荐 Claude Code 自动化（hooks/subagents/skills/plugins/MCP servers）。当用户询问自动化建议、优化设置、改进工作流、首次为项目设置 Claude Code 或询问如何开始时使用 |

---

## 4. `ai-platform-devops@personal` — v1.0.0

- **来源**：本地目录 marketplace（`C:\Users\wma19\.claude\plugins\marketplaces\personal`）
- **描述**：Dev environment health check and project conventions for the AI agent platform (FastAPI 0.115 + Next.js 15 + LangChain 0.3). Includes /setup-dev and project-conventions skills.
- **作者**：wma19
- **安装路径**：`C:\Users\wma19\.claude\plugins\cache\personal\ai-platform-devops\1.0.0`
- **安装时间**：2026-06-06

### 斜杠命令（由 `setup-dev` skill 暴露）

| 命令 | 用途 |
|---|---|
| `/setup-dev` | 验证 dev 环境健康——检查端口 11334（前端）、11335（后端）、11434（Ollama），通过项目 MCP ping MySQL，并测试一次 embedding 调用 |

### Skills（2 个 — 项目级 Claude-only 知识）

| Skill | 触发场景 |
|---|---|
| `project-conventions` | 在 ai-platform 仓库工作时（FastAPI + Next.js 15 + LangChain）——自动浮出响应信封契约、前端读取模式、MySQL MCP 选择、workflow 节点规范位置。**不直接调用**，自动加载 |
| `setup-dev` | 与上面 `/setup-dev` 斜杠命令对应，验证开发环境健康度 |

---

## 5. `42crunch-api-security-testing@claude-plugins-official` — v1.5.7

- **来源**：GitHub `anthropics/claude-plugins-official`（插件来源 `42Crunch-AI/claude-plugins`）
- **描述**：Catch API security issues during development: audit, scan, remediate, validate with AI guardrails in Claude Code.
- **作者**：42Crunch（Apache 2.0）
- **关键字**：openapi、api-security、audit、scan、remediation、vulnerability、compliance、owasp、bola、bfla、ai、devsecops
- **安装路径**：`C:\Users\wma19\.claude\plugins\cache\claude-plugins-official\42crunch-api-security-testing\1.5.7`
- **安装时间**：2026-06-10

### Skills（5 个）

| Skill | 触发场景 |
|---|---|
| `42crunch-api-security-testing` | 同时运行 Audit + Scan 的完整流水线；触发词：`run audit and scan` / `full 42crunch pipeline` / `full security check` / `audit then scan` / `42crunch` / `SQG` |
| `42crunch-audit` | 单独运行 Audit + 修复 SQG-blocking 问题；触发词：`run audit` / `audit only` / `fix audit issues` / `SQG audit` / `42crunch audit` |
| `42crunch-scan` | 单独运行 live conformance + authorization scan；触发词：`run scan` / `scan only` / `conformance test` / `BOLA test` / `BFLA test` / `42crunch scan` |
| `42crunch-setup` | 配置 42Crunch 环境（安装/更新 `42c-ast`、配置 API key、排查 binary/凭证问题）；触发词：`setup 42crunch` / `install 42c-ast` / `42crunch not working` 等 |
| `code-to-oas` | 从代码库分析并生成 OpenAPI 3.0 规范；触发词：`generate OAS from code` / `document my API` / `reverse-engineer spec` |

---

## 6. `agent-skills@addy-agent-skills` — d187883b7d76

- **来源**：GitHub `addyosmani/agent-skills`
- **描述**：Production-grade engineering skills for AI coding agents — covering the full software development lifecycle from spec to ship.
- **作者**：Addy Osmani
- **许可证**：MIT
- **安装路径**：`C:\Users\wma19\.claude\plugins\cache\addy-agent-skills\agent-skills\d187883b7d76`
- **安装时间**：2026-06-11

### Hooks
- `SessionStart` → 执行 `bash ${CLAUDE_PLUGIN_ROOT}/hooks/session-start.sh`

### 斜杠命令（8 个）

| 命令 | 描述 |
|---|---|
| `/build` | 增量实现任务——构建、测试、验证、提交。加 `auto` 一键跑完整个 plan |
| `/spec` | 启动 spec-driven 开发流程——先写结构化规范再写代码 |
| `/planning` | 把工作拆解为带验收标准与依赖顺序的小任务 |
| `/test` | TDD 工作流——先写失败测试、实现、验证。bug 修复走 Prove-It 模式 |
| `/review` | 五维度代码评审——correctness、readability、architecture、security、performance |
| `/code-simplify` | 在不改变行为的前提下简化最近变更的代码——降低复杂度 |
| `/ship` | 预发布 checklist——并行 fan-out 三个专家角色（reviewer / security-auditor / test-engineer），合并成 go/no-go 决策 |
| `/webperf` | 通过 `web-performance-auditor` 子代理运行 Web 性能审计（Deep / Quick 两种模式） |

### Subagents（4 个）

| 子代理 | 职责 |
|---|---|
| `code-reviewer` | 高级代码评审员，从 5 个维度评估变更——正确性、可读性、架构、安全性、性能 |
| `security-auditor` | 安全工程师，专注于漏洞检测、威胁建模与安全编码实践 |
| `test-engineer` | QA 工程师，专长测试策略、测试编写与覆盖率分析 |
| `web-performance-auditor` | Web 性能工程师，专注于 Core Web Vitals、加载、渲染与网络优化 |

### Skills（24 个）

| Skill | 触发场景 |
|---|---|
| `using-agent-skills` | 元 skill——会话开始或需要发现哪个 skill 适用时使用，负责发现与调度所有其他 skill |
| `spec-driven-development` | 启动新项目、功能或重大变更且尚无规范时使用 |
| `planning-and-task-breakdown` | 已有 spec/需求但需要把工作拆成可执行任务时 |
| `test-driven-development` | 实现任何逻辑、修复任何 bug、修改任何行为时使用 |
| `incremental-implementation` | 实现跨多文件的特性或变更时；避免一次性写大量代码 |
| `doubt-driven-development` | 正确性优先于速度，或在不可逆/高风险操作前，进行对抗性审查 |
| `interview-me` | 需求不明时（"build me X" 而无"为谁/为什么现在"），通过逐题访谈提取真实意图 |
| `idea-refine` | 把模糊想法收敛为可执行概念；压力测试假设、扩展选项 |
| `code-review-and-quality` | 任何变更合入主干前，进行多维度评审 |
| `code-simplification` | 重构以提升清晰度而不改变行为 |
| `debugging-and-error-recovery` | 测试失败、构建中断、行为不符预期时，系统性根因排查 |
| `security-and-hardening` | 处理用户输入、认证、数据存储或外部集成时 |
| `performance-optimization` | 存在性能需求、怀疑性能回归或需要优化 CWV/加载时间时 |
| `frontend-ui-engineering` | 构建或修改用户可见界面时 |
| `browser-testing-with-devtools` | 构建或调试浏览器中运行的功能时（需要 chrome-devtools MCP） |
| `api-and-interface-design` | 设计 API、模块边界或任何公共接口时 |
| `documentation-and-adrs` | 架构决策、变更公共 API、发布特性时记录决策与上下文 |
| `git-workflow-and-versioning` | 任何代码变更时——commit、branch、conflict 解决、组织并行流 |
| `ci-cd-and-automation` | 设置或修改构建/部署流水线时 |
| `shipping-and-launch` | 准备生产部署时——清单、监控、分阶段发布、回滚策略 |
| `observability-and-instrumentation` | 上线后行为需要可观测与可诊断时——logging/metrics/tracing/alerting |
| `deprecation-and-migration` | 移除旧系统/API/功能，或迁移用户时 |
| `context-engineering` | 启动新会话、agent 输出质量下降、切换任务、为项目配置规则与上下文时 |
| `source-driven-development` | 用任何框架/库构建时，确保每个实现决策都基于官方文档、来源可溯 |

---

## 全局设置速览（来自 `~/.claude/settings.json`）

```jsonc
{
  "permissions": { "defaultMode": "bypassPermissions" },
  "enabledPlugins": {
    "superpowers@claude-plugins-official": true,
    "andrej-karpathy-skills@karpathy-skills": true,
    "claude-code-setup@claude-plugins-official": true,
    "ai-platform-devops@personal": true,
    "42crunch-api-security-testing@claude-plugins-official": true,
    "agent-skills@addy-agent-skills": true
  },
  "extraKnownMarketplaces": {
    "karpathy-skills":   { "source": { "source": "github", "repo": "forrestchang/andrej-karpathy-skills" } },
    "personal":          { "source": { "source": "directory", "path": "C:\\Users\\wma19\\.claude\\plugins\\marketplaces\\personal" } },
    "addy-agent-skills": { "source": { "source": "github", "repo": "addyosmani/agent-skills" } }
  },
  "fastMode": true,
  "skipDangerousModePermissionPrompt": true,
  "theme": "light",
  "verbose": true
}
```

> 备注：默认 marketplace `claude-plugins-official`（Anthropic 官方）来自内置注册表，未在 `extraKnownMarketplaces` 中重复声明。

---

## 速查：本次会话可直接使用的入口

- **斜杠命令**：`/build` `/spec` `/planning` `/test` `/review` `/code-simplify` `/ship` `/webperf` `/setup-dev`
- **子代理**：`code-reviewer` `security-auditor` `test-engineer` `web-performance-auditor`
- **自动注入的会话级知识**：`superpowers/using-superpowers`（每次响应前必须先 Skill 工具选 skill）、`agent-skills/using-agent-skills`、`ai-platform-devops/project-conventions`（仅在 ai-platform 仓库）

---

*本文档由 Claude Code 实时读取本机插件缓存生成，可在 settings 变更后重新生成。*
