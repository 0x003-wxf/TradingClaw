# trade-gateway-service/app/order_service.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/app/order_service.py`
- 职责：实现统一下单、撤单、回报归一、读模型刷新和人工复核恢复。
- 依赖：`交易网关详细设计.md`、`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 输入输出对象
- 类 `PlaceOrderCommand(user_id: str, account_id: str, instrument_id: str | None, symbol: str | None, market: str | None, side: str, order_type: str, quantity: Decimal, price: Decimal | None, time_in_force: str, session_scope: str, reduce_only: bool | None, leverage: Decimal | None, idempotency_key: str, request_id: str, trace_id: str | None)`：统一下单命令。
- 类 `CancelOrderCommand(user_id: str, order_id: str, account_id: str, idempotency_key: str, request_id: str, trace_id: str | None)`：统一撤单命令。
- 类 `OrderAcceptedView(order_id: str, accepted: bool, status: str, reason_code: str | None, risk_event_id: str | None, message: str)`：同步命令受理结果。
- 类 `OrderDetailView(order_id: str, account_id: str, instrument: dict, status: str, filled_quantity: Decimal, avg_fill_price: Decimal | None, raw_status: str | None, data_status: str | None)`：订单详情视图。

## 函数
- `place_order(command: PlaceOrderCommand) -> Result[OrderAcceptedView]`：统一下单。
- `cancel_order(command: CancelOrderCommand) -> Result[OrderAcceptedView]`：统一撤单。
- `handle_execution_report(report: ExecutionReportEnvelope) -> Result[dict]`：消费标准化执行回报。
- `resume_reviewed_order(risk_event_id: str, order_id: str, decision: str, resume_token: str | None) -> Result[dict]`：处理复核恢复。
- `get_order(order_id: str, user_id: str) -> Result[OrderDetailView]`：查单。

## 细化函数
- `normalize_instrument(command: PlaceOrderCommand) -> str`：参数为下单命令；返回标准 `instrument_id`；职责是优先使用 `instrument_id`，必要时通过 `symbol + market` 兼容解析。
- `assert_place_order_prerequisites(command: PlaceOrderCommand) -> tuple[TradingAccountContext, TradingSession]`：参数为下单命令；返回账户上下文与统一会话；职责是完成用户会话、账户归属、默认会话和会话可写性校验。
- `load_trading_rule_profile(account_id: str, instrument_id: str) -> TradingRuleProfile`：参数为账户和标的；返回规则快照；职责是聚合市场规则、账户能力和通道限制。
- `validate_order_command(command: PlaceOrderCommand, rule_profile: TradingRuleProfile) -> None`：参数为下单命令和规则快照；无返回值；职责是校验精度、最小数量、TIF、session_scope、杠杆、只减仓等规则。
- `reserve_order_id(command: PlaceOrderCommand) -> str`：参数为下单命令；返回 `order_id`；职责是预生成统一订单主键，并作为风控与审计主锚点。
- `reserve_idempotency(command: PlaceOrderCommand, order_id: str) -> IdempotencyRecord`：参数为下单命令和订单 ID；返回幂等记录；职责是防止重复创建订单主记录。
- `build_risk_check_command(command: PlaceOrderCommand, order_id: str, rule_profile: TradingRuleProfile) -> RiskCheckCommand`：参数为下单命令、订单 ID、规则快照；返回风控检查命令；职责是构造标准风控输入并带上 `resource_ref`。
- `persist_new_order(command: PlaceOrderCommand, order_id: str, initial_status: str) -> Order`：参数为下单命令、订单 ID、初始状态；返回订单对象；职责是写入统一订单主记录。
- `dispatch_order_to_adapter(order: Order, rule_profile: TradingRuleProfile, account_context: TradingAccountContext) -> ChannelOrderResult`：参数为订单、规则快照、账户上下文；返回适配受理结果；职责是路由证券或数字资产适配器。
- `build_place_order_response(order: Order, accepted: bool, reason_code: str | None = None, risk_event_id: str | None = None) -> OrderAcceptedView`：参数为订单、受理结果、原因码、风险事件 ID；返回同步响应对象。
- `assert_cancel_prerequisites(command: CancelOrderCommand) -> tuple[Order, TradingSession]`：参数为撤单命令；返回订单和统一会话；职责是校验归属、当前状态和会话最小可用性。
- `dispatch_cancel_to_adapter(order: Order, trading_session: TradingSession) -> ChannelOrderResult`：参数为订单和会话；返回适配侧结果；职责是发起撤单。
- `dedupe_execution_report(report: ExecutionReportEnvelope) -> bool`：参数为执行回报事件；返回是否首次处理；职责是做回报去重。
- `validate_report_transition(order: Order, report: ExecutionReport) -> None`：参数为订单和标准回报；无返回值；职责是校验状态迁移是否合法。
- `refresh_read_models(order: Order) -> None`：参数为订单；无返回值；职责是异步刷新持仓、余额和交易规则缓存。
- `publish_order_events(order: Order, report: ExecutionReport | None = None) -> None`：参数为订单和可选回报；无返回值；职责是发布 `order.created`、`order.updated`、`execution.report_received` 等标准事件。
- `resume_pending_review_order(order: Order, decision: str, resume_token: str | None) -> None`：参数为订单、复核结论、恢复令牌；无返回值；职责是沿用原上下文恢复投递或终止订单。

## 仓储与外部协作
- `OrderRepository.save(order: Order) -> Order`：保存订单主记录。
- `ExecutionReportRepository.save(report: ExecutionReport) -> ExecutionReport`：保存标准执行回报。
- `IdempotencyService.reserve_command_key(scope: str, key: str, resource_id: str) -> IdempotencyRecord`：命令幂等预占。
- `RiskClient.evaluate_order_risk(command: RiskCheckCommand) -> RiskDecisionView`：请求风控裁决。
- `AdapterClientRouter.place_channel_order(order: Order, rule_profile: TradingRuleProfile) -> ChannelOrderResult`：发起适配侧下单。

## 实现顺序
1. 下单顺序固定为：会话校验 -> 账户归属 -> 规则校验 -> 预分配 `order_id` -> 幂等 -> 风控 -> 创建订单 -> 路由适配器。
2. 同步返回只表示受理或同步拒绝，不表示通道终态。
3. 执行回报处理必须做去重、乱序保护和状态机校验。
4. 人工复核恢复沿用原 `order_id` 和原 `idempotency_key`，绝不重建新订单。
5. 撤单流程同样必须经过归属校验、会话校验、幂等控制和标准事件发布。
6. `handle_execution_report` 处理成功后，订单事实更新与读模型刷新要拆分事务边界，避免一个慢读模型拖住主交易写链路。

## 集成检查
- 订单、执行回报、持仓余额刷新应拆为事务写入和异步事件发布两段。
- `resource_type=order` 时 `resource_id` 必须始终等于 `order_id`，风控、审计、复核恢复都不能换主键。
- 同步接口不返回“已成交”之类终态承诺，终态只能通过订单详情和后续事件观察。
