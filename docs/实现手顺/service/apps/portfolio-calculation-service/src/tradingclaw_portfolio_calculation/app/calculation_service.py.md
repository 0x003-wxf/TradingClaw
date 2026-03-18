# portfolio-calculation-service/app/calculation_service.py 实现手顺

- 目标代码文件：`apps/portfolio-calculation-service/src/tradingclaw_portfolio_calculation/app/calculation_service.py`
- 职责：实现插件路由和统一计算服务。
- 依赖：`策略系统详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `validate_config(strategy_type: str, config: dict, context: dict) -> Result[dict]`：校验策略配置。
- `evaluate(strategy_type: str, market: dict, account: dict, positions: dict, config: dict) -> Result[dict]`：运行计算。
- `generate_plan(strategy_type: str, signal: dict) -> Result[dict]`：生成交易计划。
- `get_plugin(strategy_type: str) -> StrategyPlugin`：按策略类型取插件。

## 实现顺序
1. 先实现插件注册与路由，再补四类插件。
2. 服务层只负责调度插件，不实现具体策略公式。

## 集成检查
- strategy-runtime 通过 gRPC 调本服务。
