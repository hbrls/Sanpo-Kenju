---
name: sk-execute-task
description: 执行并完成任务 `.context/current-task.md`，将任务标记为完成，将相关的过程文档上传更新。
metadata:
  version: 0.0.3
---

# Execute Task

**FAIL-FAST REQUIREMENT** Check if the tools exist, stop immediately and exit on any error.

```bash
$ backstage-gitea plan --help
```

**FAIL-FAST REQUIREMENT** 当前使用的基模在 MMLU 和 SWE-bench 上的评分必须优于 MiniMax-M2.5。如果满足，回复 **{当前使用的基模名称} + 所以我來砍，阿劍也不會生氣吧**。如果不满足，必须立刻停止并退出。

## Initialization

**MUST** 当前 Agent 只负责执行任务，所有的前置准备由其他工作节点完成。如果前置检查不满足，必须立刻停止并退出。

**MUST** 读取相关的 `Phase` 信息在 `.context/current-phase-metadata.yaml` 和 `.context/current-phase.md`。读取失败时必须立刻停止并退出。

**MUST** 读取相关的 `Task` 信息在 `.context/current-task-metadata.yaml` 和 `.context/current-task.md`。读取失败时必须立刻停止并退出。

## Workflow

- Step 1: 开始执行任务 `.context/current-task.md`。按顺序执行任务，从第一个 `- [ ]` 开始。

- Step 2: 一次只执行一个步骤条目。完成后将对应条目从 `- [ ]` 更新为 `- [x]`。

- Step 3: 任务产生的所有产出物，写入 `.context/current-task.md` 对应章节。**不允许在 `.context/` 下新建文件**。

- Step 4: 确认所有步骤条目已按预期完成并标注完成状态。

- Step 5: 任务完成后，使用 `backstage-gitea` 工具将 `.context/current-task.md` 上传更新。

```bash
# IF Task PASS
$ backstage-gitea agent PUT /:appName/:planId/:phaseId/:taskId --status PASS --context .context/current-task.md

# IF Task FAIL
$ backstage-gitea agent PUT /:appName/:planId/:phaseId/:taskId --status FAIL --context .context/current-task.md
```

- Step 6: 任务完成后，将 `.context/current-task.md` 的产出内容同步到 `.context/current-phase.md` 对应章节，并使用 `backstage-gitea` 工具上传更新。

```bash
$ backstage-gitea agent PUT /:appName/:planId/:phaseId --status TODO --context .context/current-phase.md
```

- Step 7: 如果执行过程中涉及 Design 变更，将变更内容更新到 `.context/current-plan.md` → `Design` 对应章节，并使用 `backstage-gitea` 工具上传更新。Design 变更包括但不限于：调研、盘点、思考类任务产生的跨 Phase 结论；对架构、方案、技术选型的调整。

```bash
$ backstage-gitea agent PUT /:appName/:planId --status TODO --context .context/current-plan.md
```

- Step 8: 只执行当前 Task，执行完就退出。

## Rules

**MUST** 应该在一个 `Agent Session` 中执行。如果 Context 即将用尽，必须立刻停止并退出。这种场景说明 Task 拆分有误。

**MUST** 执行过程中如识别到关键变更，必须立刻停止并退出。这种场景说明 Design 需要更新。
