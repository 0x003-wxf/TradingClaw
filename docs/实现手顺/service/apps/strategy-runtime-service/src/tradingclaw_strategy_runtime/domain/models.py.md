# strategy-runtime-service/domain/models.py 实现手顺

- 目标代码文件：`apps/strategy-runtime-service/src/tradingclaw_strategy_runtime/domain/models.py`
- 职责：定义策略运行时快照、执行窗口、信号和待执行交易计划。
- 依赖：`策略系统详细设计.md`

## 类
- 类 `StrategySnapshot(strategy_instance_id: str, version: int, runtime_status: str, payload: dict)`：运行时快照。
- 类 `StrategyExecution(execution_id: str, strategy_instance_id: str, status: str, market_snapshot: dict, account_snapshot: dict, position_snapshot: dict)`：单次执行窗口。
- 类 `StrategySignal(signal_id: str, strategy_instance_id: str, signal_type: str, payload: dict)`：计算信号。
- 类 `TradingPlan(strategy_instance_id: str, plan_id: str, orders: list[dict], review_required: bool | None = None)`：待执行交易计划。

## 函数
- `pause_for_review(self, reason_code: str) -> None`：执行窗口暂停等待复核。
- `resume(self) -> None`：恢复执行窗口。
- `complete(self) -> None`：执行窗口完成。

## 实现顺序
1. Redis 中的运行时状态只做优化，MySQL 快照是恢复主锚点。
2. 执行窗口状态与策略实例生命周期分开。

## 集成检查
- 恢复时依赖 `StrategySnapshot`、执行记录和统一订单事实。
