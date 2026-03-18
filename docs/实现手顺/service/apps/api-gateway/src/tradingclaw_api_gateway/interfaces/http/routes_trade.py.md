# api-gateway/interfaces/http/routes_trade.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/routes_trade.py`
- 职责：暴露交易会话、下单、撤单、订单查询、持仓和余额 HTTP 路由。
- 依赖：`网关与平台基础详细设计.md`、`交易网关详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `create_session(body: CreateSessionRequest, ctx: RequestContext, token: str) -> Envelope`：转发创建交易会话请求。
- `submit_auth_step(trading_session_id: str, body: SubmitAuthStepRequest, ctx: RequestContext, token: str) -> Envelope`：转发认证步骤。
- `place_order(body: PlaceOrderRequest, ctx: RequestContext, token: str, idempotency_key: str) -> Envelope`：转发统一下单命令。
- `cancel_order(order_id: str, body: CancelOrderRequest, ctx: RequestContext, token: str, idempotency_key: str) -> Envelope`：转发撤单。
- `get_order(order_id: str, ctx: RequestContext, token: str) -> Envelope`：查询订单详情。

## 实现顺序
1. 命令路由优先校验幂等键和登录态，再调用下游 gRPC。
2. HTTP 路由不做订单状态机判断。

## 集成检查
- `symbol + market` 只保留兼容入口，标准请求优先 `instrument_id`。
