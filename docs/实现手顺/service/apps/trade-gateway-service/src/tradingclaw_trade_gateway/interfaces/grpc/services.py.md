# trade-gateway-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/interfaces/grpc/services.py`
- 职责：实现统一交易域 gRPC 服务。
- 依赖：`交易网关详细设计.md`、`trade_gateway.proto`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `CreateSession(request, context) -> CreateSessionResponse`：创建交易会话。
- `SubmitAuthStep(request, context) -> SubmitAuthStepResponse`：提交认证步骤。
- `RefreshSession(request, context) -> RefreshSessionResponse`：刷新会话。
- `PlaceOrder(request, context) -> PlaceOrderResponse`：统一下单。
- `CancelOrder(request, context) -> CancelOrderResponse`：撤单。
- `GetOrder(request, context) -> GetOrderResponse`：查单。

## 实现顺序
1. 每个 RPC 只做协议转换和错误映射。
2. gRPC metadata 中传递 `request_id`、`trace_id`、`access_token`。

## 集成检查
- api-gateway、strategy-runtime 和运维工具将直接调用这些 RPC。
