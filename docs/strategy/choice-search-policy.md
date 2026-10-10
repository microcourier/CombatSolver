# 选牌实体与洗牌保路收缩

[返回策略入口](README.md)

## 当前政策

0.50.2 fork 按用户要求缩减选牌实体分支和预测牌序保路。升级、消耗、转变等持久选择及有序牌堆取牌，仅保留每个语义选择的一个实体；不保留尾部实体或不同剩余顺序。不同费用、升级、关键字和动态变量仍分别考虑。整动作、嵌套、药水和回合末的实体预留为零。显式用户或录制的选牌 token 继续定位被选实体，真实牌堆顺序、RNG 和续用模拟保持准确。

搜索快照不计算预测洗牌后的顺序键／价值；删除依赖投影的代表保留、Pocketwatch 牌序 cohort、额外束宽和有序变异的洗牌偏好。实际卡牌结算使用的 StableShuffleProjection 仍保留。

这是搜索空间收缩，允许原最优路线被舍弃。能力估值和正常战术代表仍在；不保证全部牌序操作消失，不作人类 SL 等价、质量无退化或整搜提速结论。

## 本轮证据（2026-10-10）

- Release 构建通过，0 警告／0 错误；结构边界检查通过。
- `CHOICE-COMBINATION-CONTRACT` Passed，runId `6642b34715664b48999c3847850cb392`。分隔重复牌取牌仅一份语义代表、持久修改不补实体、1,200 组独立组合枚举对照通过；原生一回合执行通过，实例清理。
- `CHOICE-TOKEN-CONTRACT` Passed，runId `bed0f9a580574309a5d143fdc8008cbb`。1,024 组 token 对照、显式末实体准确回放、不同语义 B/C 不被重复 A 挤掉、整动作／嵌套额外实体额度为零通过，实例清理。
- `SEARCH-POLICY-SHRINK-SENTINEL` Passed，runId `0daca3dae47244e0aff004b5a1ff50ea`。Ironclad／Fuzzy Wurm Crawler Weak／种子 COMBATSOLVER，敌方 HP 25，束宽 24、节点预算 3,000、软预算 1,500 ms、DOP1、增量核对。原生 T2 获胜，80 HP，计划外重算 0，实例清理。此结果证明该场预测与执行可用，不是同根基线质量或性能对照。

没有完整战斗集质量／耗时对照。上述组合与 token 微基准只验证枚举／定位，不是整搜性能证据。

## 最小复跑

Windows 使用已确认游戏与 RitsuLib 目录；两项都在自有隔离实例中运行：

```powershell
pwsh -NoProfile -File tools/testing/run-unattended-test.ps1 -ScenarioId CHOICE-COMBINATION-CONTRACT -Sts2GameRoot <游戏目录> -RitsuWorkshopRoot <RitsuLib目录> -TimeoutSeconds 120 -ExitOnComplete -CleanupInstanceOnExit
pwsh -NoProfile -File tools/testing/run-unattended-test.ps1 -ScenarioId CHOICE-TOKEN-CONTRACT -Sts2GameRoot <游戏目录> -RitsuWorkshopRoot <RitsuLib目录> -TimeoutSeconds 120 -ExitOnComplete -CleanupInstanceOnExit
```

Linux 等价入口：

```bash
./tools/testing/run-unattended-test.sh --scenario-id CHOICE-COMBINATION-CONTRACT --sts2-game-root '<游戏目录>' --ritsu-workshop-root '<RitsuLib目录>' --timeout-seconds 120 --exit-on-complete --cleanup-instance-on-exit
./tools/testing/run-unattended-test.sh --scenario-id CHOICE-TOKEN-CONTRACT --sts2-game-root '<游戏目录>' --ritsu-workshop-root '<RitsuLib目录>' --timeout-seconds 120 --exit-on-complete --cleanup-instance-on-exit
```

跨回合哨兵：

```powershell
pwsh -NoProfile -File tools/testing/run-unattended-test.ps1 -ScenarioId SEARCH-POLICY-SHRINK-SENTINEL -Sts2GameRoot <游戏目录> -RitsuWorkshopRoot <RitsuLib目录> -EnemyCurrentHp 25 -FixedSearchBudget -SearchBudgetOverrideMilliseconds 1500 -SearchMaxExpandedNodesForTest 3000 -SearchBeamWidthForTest 24 -SearchMaxDegreeOfParallelismForTest 1 -VerifyIncrementalSearch -ExpectedFinishedTurnAtMost 6 -ExpectedUnexpectedReplansAtMost 0 -HeadlessFastModeForTest Instant -DeploymentFastModeForTest Instant -DeploymentInterActionDelaySecondsForTest 0 -TimeoutSeconds 120 -ExitOnComplete -CleanupInstanceOnExit
```
