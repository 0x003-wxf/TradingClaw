# trade-gateway-service/domain/models.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/domain/models.py`
- 职责：定义统一交易会话、认证流程、订单、执行回报、持仓、余额和交易规则模型。
- 依赖：`交易网关详细设计.md`、`状态字段枚举表.md`

## 类
- 类 `TradingSession(trading_session_id: str, account_id: str, provider_code: str, trading_session_status: str, adapter_session_status: str | None)`：统一交易会话。
- 类 `TradingSessionAuthFlow(auth_flow_id: str, trading_session_id: str, step_code: str, challenge_required: bool, required_fields: list[dict], artifacts: dict | None, completed: bool)`：步骤式认证流程。
- 类 `Order(order_id: str, account_id: str, instrument_id: str, side: str, order_type: str, quantity: Decimal, price: Decimal | None, status: str, idempotency_key: str)`：统一订单。
- 类 `ExecutionReport(order_id: str, raw_status: str, status: str, filled_quantity: Decimal, avg_fill_price: Decimal | None, reason_code: str | None)`：标准化执行回报。
- 类 `TradingRuleProfile(account_id: str, instrument_id: str, time_in_force_options: list[str], session_scope_options: list[str], min_quantity: Decimal, quantity_step: Decimal, price_precision: int, reduce_only_supported: bool)`：统一交易规则快照。

## 函数
- `start_authentication(self) -> None`：会话进入 `AUTHENTICATING`。
- `mark_available(self) -> None`：会话进入 `AVAILABLE`。
- `mark_degraded(self, reason_code: str) -> None`：会话降级。
- `move_to_pending_review(self) -> None`：订单进入人工复核。
- `move_to_pending_submit(self) -> None`：订单进入待提交。
- `apply_report(self, report: ExecutionReport) -> None`：按统一状态机推进订单。
- `assert_writable(self) -> None`：校验会话可执行真实交易写操作。

## 实现顺序
1. 把会话状态机和订单状态机都固化在领域模型内。
2. `apply_report` 必须验证状态迁移合法性。

## 集成检查
- 不要在领域对象里直接调用 gRPC 或数据库。
