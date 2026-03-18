# strategy-runtime-service/app/runtime_service.py 实现手顺

- 目标代码文件：`apps/strategy-runtime-service/src/tradingclaw_strategy_runtime/app/runtime_service.py`
- 职责：实现策略触发、快照恢复、调用计算服务、风控接入和交易执行编排。
- 依赖：`策略系统详细设计.md`、`交易网关详细设计.md`、`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 输入输出对象
- 类 `RuntimeTrigger(strategy_instance_id: str, trigger_type: str, occurred_at: datetime, instrument_id: str | None, payload: dict)`：统一运行时触发对象。
- 类 `ExecutionContext(execution: StrategyExecution, strategy_config: dict, market_snapshot: dict, account_snapshot: dict, position_snapshot: dict)`：策略执行期上下文。
- 类 `PlanExecutionResult(execution_id: str, submitted_order_ids: list[str], review_required: bool, risk_event_id: str | None, runtime_status: str)`：交易计划执行结果。

## 函数
- `handle_market_trigger(strategy_instance_id: str, instrument_id: str, trigger_payload: dict) -> Result[StrategyExecution]`：处理行情触发。
- `handle_schedule_trigger(strategy_instance_id: str, scheduled_at: datetime) -> Result[StrategyExecution]`：处理定时触发。
- `evaluate_strategy(execution: StrategyExecution) -> Result[TradingPlan]`：调用组合计算服务生成交易计划。
- `submit_plan_to_risk(plan: TradingPlan, execution: StrategyExecution) -> Result[RiskDecisionView]`：风控裁决。
- `execute_plan(plan: TradingPlan, execution: StrategyExecution) -> Result[dict]`：调用 trade-gateway 提交订单。
- `recover_runtime(strategy_instance_id: str) -> Result[StrategySnapshot]`：从快照恢复运行时。

## 细化函数
- `load_strategy_runtime_state(strategy_instance_id: str) -> tuple[StrategyInstanceView, dict, StrategySnapshot | None]`：参数为策略实例 ID；返回策略实例、当前配置和最近快照；职责是恢复运行前读取主数据。
- `build_execution_context(execution: StrategyExecution) -> ExecutionContext`：参数为执行窗口；返回执行上下文；职责是聚合市场、账户、持仓和配置快照。
- `load_market_snapshot(strategy_instance_id: str, instrument_id: str | None) -> dict`：参数为策略实例和可选标的；返回市场快照；职责是通过市场服务读取行情、K 线或指标。
- `load_account_snapshot(account_id: str) -> dict`：参数为账户 ID；返回账户和余额快照；职责是通过 trade-gateway 读取统一交易视图。
- `load_position_snapshot(account_id: str, instrument_ids: list[str]) -> dict`：参数为账户 ID 和标的集合；返回持仓快照；职责是为策略计算提供统一持仓视图。
- `call_calculation_validate(strategy_type: str, config: dict, context: dict) -> dict`：参数为策略类型、配置和上下文；返回标准化配置；职责是调用组合计算服务做运行前配置校验。
- `call_calculation_evaluate(context: ExecutionContext) -> dict`：参数为执行上下文；返回策略信号；职责是调用纯计算插件产出 `signal`。
- `call_calculation_generate_plan(signal: dict, strategy_type: str) -> TradingPlan`：参数为信号和策略类型；返回交易计划；职责是生成待执行订单集合。
- `validate_trading_plan(plan: TradingPlan, context: ExecutionContext) -> None`：参数为交易计划和上下文；无返回值；职责是做系统级前置校验，如空计划、重复订单、账户不可交易等。
- `pause_execution_for_review(execution: StrategyExecution, decision: RiskDecisionView) -> None`：参数为执行窗口和风控裁决；无返回值；职责是把执行窗口置为暂停并写恢复引用。
- `submit_orders(plan: TradingPlan, execution: StrategyExecution) -> PlanExecutionResult`：参数为计划和执行窗口；返回执行结果；职责是逐单或批量调用 trade-gateway 下单，并记录提交结果。
- `apply_order_updates(execution: StrategyExecution, order_events: list[dict]) -> None`：参数为执行窗口和订单事件；无返回值；职责是根据回报刷新执行窗口状态。
- `write_snapshot(execution: StrategyExecution, payload: dict) -> StrategySnapshot`：参数为执行窗口和快照载荷；返回快照对象；职责是把当前运行状态持久化为可恢复锚点。
- `resume_paused_execution(strategy_instance_id: str, risk_event_id: str, decision: str, resume_token: str | None) -> Result[dict]`：参数为策略实例、风险事件、决策和恢复令牌；返回恢复结果；职责是沿用原执行窗口恢复或终止当前周期。

## 仓储与外部协作
- `StrategyRepository.get_instance(strategy_instance_id: str) -> StrategyInstanceView`：读取策略实例。
- `StrategySnapshotRepository.save(snapshot: StrategySnapshot) -> StrategySnapshot`：保存运行时快照。
- `MarketClient.get_quotes(...) -> dict` / `MarketClient.get_indicators(...) -> dict`：读取市场快照。
- `PortfolioCalculationClient.evaluate(...) -> dict`：调用纯计算服务。
- `RiskClient.evaluate_strategy_risk(command: RiskCheckCommand) -> RiskDecisionView`：策略风控裁决。
- `TradeGatewayClient.place_order(command: PlaceOrderCommand) -> OrderAcceptedView`：执行真实交易。

## 实现顺序
1. 固定闭环必须是“触发 -> 计算 -> 计划 -> 风控 -> 交易 -> 回报 -> 快照”。
2. 运行时不得直接访问证券或数字资产适配服务。
3. 所有触发入口最终都要归一为 `RuntimeTrigger` 或 `StrategyExecution`，避免行情触发和定时触发写两套编排逻辑。
4. `evaluate_strategy` 前必须先拿到统一账户与持仓语义，不能直接消费适配域原始余额或原始订单状态。
5. 每次执行窗口推进到关键节点都要写快照，至少包括：已开始计算、已生成计划、等待复核、已下单、已完成。
6. `recover_runtime` 要能在不依赖 Redis 的情况下，仅凭 MySQL 快照、订单事实和市场重放恢复执行上下文。

## 集成检查
- 人工复核命中时暂停当前执行窗口，而不是销毁整个策略实例。
- 恢复后的执行链路必须沿用原 `execution_id`、`strategy_instance_id`、`risk_event_id` 和待恢复上下文，不能重新生成一套新执行窗口冒充继续执行。
- 若交易计划为空，应明确写执行记录和快照，而不是静默跳过本次触发。
