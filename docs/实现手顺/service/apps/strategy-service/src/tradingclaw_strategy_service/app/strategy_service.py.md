# strategy-service/app/strategy_service.py 实现手顺

- 目标代码文件：`apps/strategy-service/src/tradingclaw_strategy_service/app/strategy_service.py`
- 职责：实现策略创建、查询、修改、停启和归档。
- 依赖：`策略系统详细设计.md`、`后端需求文档.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `create_strategy(user_id: str, account_id: str, strategy_type: str, config: dict, start_immediately: bool, idempotency_key: str) -> Result[StrategyInstanceView]`：创建策略。
- `list_strategies(user_id: str, page: int, page_size: int) -> Result[PageResult[StrategyInstanceView]]`：分页查询策略。
- `get_strategy(user_id: str, strategy_instance_id: str) -> Result[StrategyDetailView]`：策略详情。
- `update_strategy(user_id: str, strategy_instance_id: str, config: dict) -> Result[StrategyInstanceView]`：更新配置。
- `stop_strategy(user_id: str, strategy_instance_id: str, operator_reason: str | None) -> Result[dict]`：停止策略。

## 实现顺序
1. 先做创建、列表、详情，再做修改和停启。
2. 创建前校验用户会话、账户归属、策略额度和配置 Schema。

## 集成检查
- 若 `start_immediately=true`，只返回受理结果并触发运行时工作流。
