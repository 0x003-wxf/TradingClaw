# contracts-events/trading.py 实现手顺

- 目标代码文件：`packages/contracts-events/src/tradingclaw_contracts_events/trading.py`
- 职责：定义交易域标准事件 payload。
- 依赖：`交易网关详细设计.md`、`事件字段字典.md`

## 类
- 类 `TradingSessionAvailablePayload(trading_session_id: str, account_id: str, status: str, provider_code: str)`：会话可用事件。
- 类 `OrderCreatedPayload(order_id: str, account_id: str, instrument_id: str, status: str, idempotency_key: str)`：订单创建事件。
- 类 `ExecutionReportReceivedPayload(order_id: str, channel_order_id: str | None, raw_status: str, normalized_status: str, occurred_at: datetime)`：执行回报事件。

## 函数
- `build_trading_session_available(...) -> TradingSessionAvailablePayload`：构造会话可用 payload。
- `build_order_created(...) -> OrderCreatedPayload`：构造订单创建 payload。
- `build_execution_report(...) -> ExecutionReportReceivedPayload`：构造执行回报 payload。

## 实现顺序
1. 按事件字段字典冻结字段名。
2. 对外事件只暴露统一状态。

## 集成检查
- trade-gateway 和适配服务都依赖本文件。
