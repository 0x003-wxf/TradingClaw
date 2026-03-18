# strategy-runtime-service/interfaces/workers/scheduler.py 实现手顺

- 目标代码文件：`apps/strategy-runtime-service/src/tradingclaw_strategy_runtime/interfaces/workers/scheduler.py`
- 职责：实现策略定时触发调度器。
- 依赖：`策略系统详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `run_schedule_tick(now: datetime) -> None`：扫描到期策略并触发执行。
- `load_due_strategies(now: datetime) -> list[str]`：查询到期策略实例。

## 实现顺序
1. 调度器只负责触发，不在此计算交易计划。

## 集成检查
- scheduler 角色应与事件消费者分开部署。
