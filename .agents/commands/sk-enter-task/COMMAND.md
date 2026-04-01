---
name: sk-enter-task
description: 找到 Current Task，更新相关 Metadata 和 Context，完成后立刻停止并退出。
metadata:
    version: 0.0.3
---

# Enter Task

**FAIL-FAST REQUIREMENT** Check if the tools exist, stop immediately and exit on any error.

```bash
$ backstage-gitea agent --help
```

**FAIL-FAST REQUIREMENT** 当前使用的基模在 MMLU 和 SWE-bench 上的评分必须优于 MiniMax-M2.5。如果满足，回复 **{当前使用的基模名称} + 所以我來砍，阿劍也不會生氣吧**。如果不满足，必须立刻停止并退出。

## Initialization

**MUST** 读取相关的 `Plan` 信息在 `.context/current-plan-metadata.yaml` 和 `.context/current-plan.md`。读取失败时必须立刻停止并退出。

**MUST** 读取相关的 `Phase` 信息在 `.context/current-phase-metadata.yaml` 和 `.context/current-phase.md`。读取失败时必须立刻停止并退出。

## Workflow

**职责范围**：获取并更新 Current Task 的元数据与上下文信息，完成后立即退出。不修改代码文件，具体任务执行由后续 Agent 负责。

- Step 1: 使用 `backstage-gitea` 工具找到并下载 Current Task。

更新 Current Task Metadata `.context/current-task-metadata.yaml`。

```bash
$ backstage-gitea agent HEAD /:appId/:planId/:phaseId/current-task > .context/current-task-metadata.yaml
# 将下载的内容写入 .context/current-task-metadata.yaml
```

更新 Current Task Context `.context/current-task.md`。

```bash
$ backstage-gitea agent GET /:appId/:planId/:phaseId/current-task > .context/current-task.md
# 将下载的内容写入 .context/current-task.md
```
