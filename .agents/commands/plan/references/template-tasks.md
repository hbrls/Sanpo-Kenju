---
name: template-tasks
metadata:
  version: 0.0.2
---

# Tasks

## 执行模式

<!-- // 将下方规则同步到 Plan 中，使规则与 Tasks 相邻，便于执行时随时查阅 -->

以下为示例规则与示例模板；具体执行规则口径以 `.agents/commands/execute/COMMAND.md` 为准。

按 Phase / Task 两层结构拆分。

- **MUST** 每个 Phase 的第一条 Task 必须用于复核“上一个 Phase 的完成情况”（确认所有 Task 已标注完成、验收点已满足、以及上一个 Phase 的收尾动作已完成），且编号必须为 `TASK-a00`（其中 `a` 为 Phase 编号，例如 Phase 1 为 `1xx`）；若不满足则暂停并回到上一个 Phase 补齐。
- **MUST** 每个 Phase 的最后一条 Task 必须用于判断"当前 Phase 是否需要返修 Design 或 Specs"（例如发现关键变更、设计口径不一致、验收标准需调整），且编号必须为 `TASK-a99`（其中 `a` 为 Phase 编号，例如 Phase 1 为 `1xx`）；若需要返修则暂停并先更新 Design 或 Specs 与用户确认，再重新拆分/调整后续 Tasks。
- **MUST** Phase 0 为特殊 Phase：用于确认每个 Phase 的执行准备是否已就绪（尤其是拆分是否合理、验收是否可验证、依赖是否明确）。其中 `HITL-000` 用于确认整体目标，`HITL-00x`（例如 `HITL-001`）用于确认对应的 Phase a（例如 Phase 1）的拆分是否合理。

- **MUST** 必须严格按顺序执行任务，并从第一个 `- [ ]` 开始。
- **MUST** 必须一次只执行一个 Task，完成后暂停并等待下一步指示。
- **MUST** 必须在完成 Task 后将对应条目从 `- [ ]` 更新为 `- [x]`。
- **MUST** 发生错误时必须立即停止执行，并等待用户指示。
- **MUST** 执行过程中如识别到关键变更，必须立即暂停，先更新 Design 或 Specs 并与用户确认，再重新按 Phase / Task 拆分并调整顺序。

- **MUST NOT** 跳过任务。
- **MUST NOT** 不按顺序执行。
- **MUST NOT** 执行任务列表之外的工作。
- **MUST NOT** 出错后继续执行。

##  Context Window / Phase 执行约束：

- **默认**：一个 Phase 在一个新的 Context Window 中执行。
- **边界**：一个 Context Window 内只推进一个 Phase。
- **例外**：如果执行过程中识别到某个 Phase 过大（例如超过上下文容量或需要大量并行信息），暂停并提示 HITL 指示拆分到多个 Phase。

当一个 Phase 完成后暂停，执行下面的检查：
- 确认所有 Task 已按预期完成并在文档中标注完成状态。
- 如需测试：将测试拆为独立 Task 并在 Tasks 中跟进；完成后在此确认已全部通过。
- 如需更新文档：将文档更新拆为独立 Task 并在 Tasks 中跟进；完成后在此确认已更新。

## 概览

| Phase           | Tasks   | Completed | Progress |
|-----------------|---------|-----------|----------|
| Phase 0         | {n}     | 0         | 0%       |
| Phase 1         | {n}     | 0         | 0%       |
| Phase 2         | {n}     | 0         | 0%       |
| **Total**       | **{n}** | **0**     | **0%**   |

## Exception Dependencies & Blockers
 
<!-- // 本章节只记录“特殊依赖/阻塞项（exceptions）”，用于表达偏离 Tasks 自然从上到下顺序的关系；严禁把正常流程/常规依赖（已由顺序隐含表达）重复写进来。 -->

仅在满足以下任一条件时，才写入本章节（否则留空即可）：

- {需要提前准备的前置条件：权限/账号/环境/证书/配额/外部依赖等}
- {需要跨团队并行协调的对齐点：接口联调窗口、发布节奏、数据口径确认等}
- {顺序反转或回流：后置事项反向阻塞前置推进，或需要回到上游返修 Design/Requirements}
- {高不确定性/高风险路径：一旦失败会导致大范围返工或阻塞的关键点}
- {需要显式停下确认的决策点：需要 Human/Owner 做选择才能继续}

```mermaid
graph LR
    %% Only list exceptions that deviate from the top-to-bottom order
    TASK-XXX[TASK-XXX] --> TASK-YYY[TASK-YYY]
```

**Blockers**

- **Blocker**: {blocker description}
- **Blocking Items**: {TASK-XXX / STORY-XXX}
- **Raised**: {date}
- **Owner**: {name}
- **Status**: Open/Resolved
- **Resolution**: {resolution}

## Changelog

{以最新的设计/决策为准，不维护面向历史版本的变更日志；大多数更新应直接反映在 Design 中。本节仅记录少量与执行强相关的记忆点，例如关键场景、易误解边界、踩坑与注意事项，用于后续执行与对齐。}

{date}: {memory}

## Tasks Breakdown

### Phase 0: Human-in-the-Loop Alignment

本 Phase 为 Human 参与的对齐阶段，用于调整计划目标、约束、范围与验收标准。

- **允许多轮 Loop**：Agent 与 Human 可在本 Phase 反复迭代（补充上下文、澄清需求、收敛范围、调整优先级）。

- [ ] **HITL-000**: 确认整体目标
  - **Dependencies**:
    - None
  - **Do**:
    - {要对齐/澄清的事项} -> {输出：已确认的目标/范围/约束}
  - **Check**:
    - {确认口径}
    - {观测信号/证据：Human 明确确认（引用原话/链接）}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 HITL-001
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{待确认问题清单（含推荐选项/默认建议）}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **HITL-001**: 确认 Phase 1 的拆分是否合理
  - **Dependencies**:
    - HITL-000
  - **Do**:
    - 复核 Phase 1 的拆分是否可执行/可验收/依赖明确 -> {输出：拆分确认（或缺口清单）}
  - **Check**:
    - {确认口径}
    - {观测信号/证据：Human 明确确认（引用原话/链接）}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 HITL-002
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{待确认问题清单（含推荐选项/默认建议）}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **HITL-002**: 确认 Phase 2 的拆分是否合理
  - **Dependencies**:
    - HITL-001
  - **Do**:
    - 复核 Phase 2 的拆分是否可执行/可验收/依赖明确 -> {输出：拆分确认（或缺口清单）}
  - **Check**:
    - {确认口径}
    - {观测信号/证据：Human 明确确认（引用原话/链接）}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 HITL-099
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{待确认问题清单（含推荐选项/默认建议）}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **HITL-099**: {task description}
  - **Dependencies**:
    - HITL-002
  - **Do**:
    - {要对齐/澄清的事项} -> {输出：已确认的目标/范围/约束}
  - **Check**:
    - {确认口径}
    - {观测信号/证据：Human 明确确认（引用原话/链接）}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Pause and HITL，{Phase 0 收尾确认}
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{待确认问题清单（含推荐选项/默认建议）}
  - **Act**: {根据 Check Conditions 设置}

### Phase 1: {Phase Description}

- [ ] **TASK-100**: 复核 Phase 0 是否已完成并满足开始 Phase 1 的前置条件
  - **Dependencies**:
    - HITL-099
  - **Do**:
    - 复核前置条件 -> {输出：前置条件确认（或缺口清单）}
  - **Check**:
    - {前置条件检查清单}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-101
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{缺口清单}与{待确认点}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-101**: Do Nothing
  - **Dependencies**:
    - TASK-100
  - **Do**:
    - Do Nothing
  - **Check**:
    - Check Nothing
  - **Act**: PASS and Continue to TASK-101-1

- [ ] **TASK-101-1**: 演示在 TASK-101 和 TASK-101-2 之间插入任务 1
  - **Dependencies**:
    - TASK-101
  - **Do**:
    - {要执行的动作} -> {预期产出物/交付物}
  - **Check**:
    - {验收/验证标准}
    - {观测信号/指标}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-101-1-1
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{失败原因/阻塞点}与{需要决策的问题}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-101-1-1**: 演示在 TASK-101-1 和 TASK-101-1-2 之间插入任务 1-1
  - **Dependencies**:
    - TASK-101-1
  - **Do**:
    - {要执行的动作} -> {预期产出物/交付物}
  - **Check**:
    - {验收/验证标准}
    - {观测信号/指标}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-101-1-2
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{失败原因/阻塞点}与{需要决策的问题}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-101-1-2**: 演示在 TASK-101-1-1 和 TASK-101-2 之间插入任务 1-2
  - **Dependencies**:
    - TASK-101-1-1
  - **Do**:
    - {要执行的动作} -> {预期产出物/交付物}
  - **Check**:
    - {验收/验证标准}
    - {观测信号/指标}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-101-2
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{失败原因/阻塞点}与{需要决策的问题}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-101-2**: 演示在 TASK-101-1 和 TASK-102 之间插入任务 2
  - **Dependencies**:
    - TASK-101-1
  - **Do**:
    - {要执行的动作} -> {预期产出物/交付物}
  - **Check**:
    - {验收/验证标准}
    - {观测信号/指标}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-102
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{失败原因/阻塞点}与{需要决策的问题}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-102**: {Task Description}
  - **Dependencies**:
    - TASK-101-2
  - **Do**:
    - {要执行的动作} -> {预期产出物/交付物}
  - **Check**:
    - {验收/验证标准}
    - {观测信号/指标}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-103
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{失败原因/阻塞点}与{需要决策的问题}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-103**: {Task Description}
  - **Dependencies**:
    - TASK-102
  - **Do**:
    - {要执行的动作} -> {预期产出物/交付物}
  - **Check**:
    - {验收/验证标准}
    - {观测信号/指标}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-199
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{失败原因/阻塞点}与{需要决策的问题}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-199**: 判断 Phase 1 是否需要返修 Design/Specs（人工验收）
  - **Dependencies**:
    - TASK-101 或其他中间任务
  - **Do**:
    - 复盘本 Phase 产出与 Design/Specs 一致性 -> {输出：需要返修的点（如有）与修改建议}
  - **Check**:
    - 是否存在关键变更/口径不一致/验收标准不清导致后续不可执行
    - {观测信号/证据：列出不一致点/关键变更点/标准缺口}
    - IF PASS  : 将 Act 设置为 PASS and Pause and HITL，{Phase 收尾确认}并提问{是否进入下一个 Phase}
    - ELSE FAIL: 将 Act 设置为 Pause and HITL，{需要返修点清单}，先更新 Specs 并与用户确认，再重拆后续 Tasks
  - **Act**: {根据 Check 的结果设置}

### Phase 2: {phase description}

- [ ] **TASK-200**: 复核 Phase 1 是否已完成并满足进入 Phase 2 的前置条件
  - **Dependencies**:
    - TASK-199
  - **Do**:
    - 复核前置条件 -> {输出：前置条件确认（或缺口清单）}
  - **Check**:
    - {前置条件检查清单}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-201
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{缺口清单}与{待确认点}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-201**: Do Nothing
  - **Dependencies**:
    - TASK-200
  - **Do**:
    - Do Nothing
  - **Check**:
    - Check Nothing
  - **Act**: PASS and Continue to TASK-202

- [ ] **TASK-202**: {Task Description}
  - **Dependencies**:
    - TASK-201
  - **Do**:
    - {要执行的动作} -> {预期产出物/交付物}
  - **Check**:
    - {验收/验证标准}
    - {观测信号/指标}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-299
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{失败原因/阻塞点}与{需要决策的问题}
  - **Act**: {根据 Check Conditions 设置}

- [ ] **TASK-299**: 判断 Phase 2 是否需要返修 Design/Specs（人工验收）
  - **Dependencies**:
    - TASK-202
  - **Do**:
    - 复盘本 Phase 产出与 Design/Specs 一致性 -> {输出：需要返修的点（如有）与修改建议}
  - **Check**:
    - 是否存在关键变更/口径不一致/验收标准不清导致后续不可执行
    - {观测信号/证据：列出不一致点/关键变更点/标准缺口}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Pause and HITL，{Phase 收尾确认}并提问{是否进入下一个 Phase（含可选项）}
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{需要返修点清单}，先更新 Specs 并与用户确认，再重拆后续 Tasks
  - **Act**: {根据 Check Conditions 设置}
