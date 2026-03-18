# api-gateway/interfaces/http/dependencies.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/dependencies.py`
- 职责：提供路由依赖项，如用户身份、请求上下文、下游 gRPC client。
- 依赖：`网关与平台基础详细设计.md`、`API字段字典.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `get_request_context(request: Request) -> RequestContext`：解析或生成 `request_id`、`trace_id`、`client_type`、`client_version`。
- `require_access_token(request: Request) -> str`：提取 Bearer Token。
- `require_idempotency_key(request: Request) -> str`：对命令接口校验 `X-Idempotency-Key`。
- `get_identity_client() -> IdentityGrpcClient`：获取身份服务 client。
- `get_trade_client() -> TradeGatewayGrpcClient`：获取交易网关 client。
- `get_capability_client() -> CapabilityRegistryClient`：获取能力治理服务 client。

## 实现顺序
1. 把所有头字段解析集中到依赖函数中。
2. 对缺失 Token 或幂等键返回统一错误码。

## 集成检查
- 所有路由不得手写请求头解析逻辑。
