# Engineering（工程）

我每天用来做编码工作的 skills。

## 用户调用（User-invoked）

只有当你键入它们时才可达（Claude Code：`disable-model-invocation: true`；Codex：在 `agents/openai.yaml` 中设置 `policy.allow_implicit_invocation: false`）。

- **[ask-matt](./ask-matt/SKILL.md)**：询问哪种 skill 或流程最适合你的情境。是本仓库"用户可触达"skills 的路由总览。
- **[grill-with-docs](./grill-with-docs/SKILL.md)**：访谈会话，同时构建项目的领域模型 —— 就术语进行打磨，并就地更新 `CONTEXT.md` 与 ADR。
- **[triage](./triage/SKILL.md)**：让 issue 沿着一个分诊角色状态机前进。
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)**：扫描代码库中的"深化"机会，以可视化 HTML 报告呈现，然后针对你选中的那一个开展访谈。
- **[setup-matt-pocock-skills](./setup-matt-pocock-skills/SKILL.md)**：为本仓库的工程 skills 做配置（issue tracker、triage 标签、领域文档布局）。每个仓库运行一次。
- **[to-spec](./to-spec/SKILL.md)**：把当前对话整理成 spec，并发布到 issue tracker。
- **[to-tickets](./to-tickets/SKILL.md)**：把任何计划、spec 或对话拆成一组"示踪弹"工单，每张工单声明自己的阻塞边 —— 以本地文件中的文字形式，或在真实 tracker 上以原生阻塞链接形式。
- **[implement](./implement/SKILL.md)**：根据 spec 或一组工单构建产物，在预先约定的接缝处驱动 `/tdd`，并在提交前以 `/code-review` 收尾。
- **[wayfinder](./wayfinder/SKILL.md)**：规划一块巨大的、超出单个 agent 会话承载能力的工作，以 issue tracker 上的一张"决策工单地图"展开，逐个解决，直到通往目的地的路径清晰可见。

## 模型调用（Model-invoked）

模型或人都可触达（描述里保留丰富的触发措辞，让模型能自动抓取）。

- **[prototype](./prototype/SKILL.md)**：构建一个一次性的原型来回答某个设计问题 —— 状态/逻辑类问题用一份可共享的 HTML 文件，UI 类问题用同一路由下可切换的几个变体。

- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)**：针对棘手 bug 与性能回归的纪律性诊断循环：构建一个会因本 bug 而变红的反馈环 → 最小化 → 提出假设 → 插桩 → 修复 → 加回归测试。
- **[research](./research/SKILL.md)**：针对高可信度的"主源"调查一个问题，并产出一份带引用的 Markdown 文件写入仓库，由后台 agent 执行。
- **[tdd](./tdd/SKILL.md)**：基于红-绿-重构循环的测试驱动开发。一次构建一个垂直切片，要么搭功能，要么修 bug。
- **[domain-modeling](./domain-modeling/SKILL.md)**：主动构建并打磨项目的领域模型：挑战术语、用场景做压力测试，并就地更新 `CONTEXT.md` 与 ADR。
- **[codebase-design](./codebase-design/SKILL.md)**：用于设计"深模块"的共同纪律与词汇：小接口、干净的接缝，可通过该接口测试。
- **[code-review](./code-review/SKILL.md)**：自某个固定基线以来 diff 的两轴评审：**Standards**（是否符合仓库编码规范，外加一套 Fowler 坏味基线）与 **Spec**（是否忠实地实现了来源 issue/spec），由并行子 agent 执行。
- **[resolving-merge-conflicts](./resolving-merge-conflicts/SKILL.md)**：逐 hunk 处理进行中的 git merge 或 rebase 冲突，依据两侧主源回溯到意图来化解，然后收尾操作（绝不 `--abort`）。
- **[wizard](./wizard/SKILL.md)**：生成一份交互式 bash 向导，带人走过那些只有人能完成的步骤：provisioning 基础设施、配置凭据或 CI secret、走过陌生的第三方 dashboard、运行一次性迁移或切换。
