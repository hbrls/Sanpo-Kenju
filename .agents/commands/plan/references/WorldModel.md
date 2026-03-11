---
name: WorldModel
metadata:
  version: 0.0.2
---

<Task type="Model">
- [ ] **TASK-xxx**: {Task Description}
  - **Dependencies**:
    - TASK-xxx
    - TASK-xxx
  - **Do**: 
    - {明确“要执行的动作”以及“预期产出物/交付物”（推荐用 `动作 -> 产出` 的形式）}
    - {要执行的动作}
    - {要推进的事项}
  - **Check**: 
    - {明确“如何验收/验证”，并给出可观测信号/指标与通过/失败阈值}
    - {验收/验证标准}
    - {观测信号/指标}
    - Conditions:
      - IF PASS   将 Act 设置为: PASS and Continue to 下一条 TASK-xxx
      - ELSE FAIL 将 Act 设置为: Pause and HITL，{阻塞点/需要决策的问题}
  - **Act**: {根据 Check Conditions 设置}
  - **禁止添加字段，使用 Do 和 Check 描述和执行任务**
</Task>

<Phase type="Model">
### Phase a: {Phase Description}

- [ ] **TASK-a00**
- [ ] **TASK-a01**
- [ ] **TASK-a02**
- ...
- [ ] **TASK-a99**
</Phase>

<DCA type="Process">
- **Do**: 执行阶段，根据设计执行相应的操作。
- **Check**: 检查阶段，验证执行结果是否符合预期。
- **Act**: 行动阶段，根据检查结果采取相应的行动。
</DCA>
