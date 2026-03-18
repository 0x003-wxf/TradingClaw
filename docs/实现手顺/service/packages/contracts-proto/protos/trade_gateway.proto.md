# contracts-proto/trade_gateway.proto 实现手顺

- 目标代码文件：`packages/contracts-proto/protos/trade_gateway.proto`
- 职责：定义统一交易会话、下单、撤单、查询和恢复相关 gRPC 契约。
- 依赖：`交易网关详细设计.md`、`API字段字典.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `service TradingSessionService`：定义 `CreateSession`、`SubmitAuthStep`、`RefreshSession`、`CloseSession`。
- `service TradingOrderService`：定义 `PlaceOrder`、`CancelOrder`、`GetOrder`、`ListPositions`、`ListBalances`。
- `message ExecutionReport`：包含 `order_id`、`channel_order_id`、`raw_status`、`status`、`filled_quantity`、`avg_fill_price`、`reason_code`。

## 实现顺序
1. 会话流和订单流分服务定义。
2. 所有命令接口都要带 `request_id`、`trace_id`、`idempotency_key`。

## 集成检查
- trade-gateway-service、strategy-runtime-service、适配服务都要依赖此 proto。
