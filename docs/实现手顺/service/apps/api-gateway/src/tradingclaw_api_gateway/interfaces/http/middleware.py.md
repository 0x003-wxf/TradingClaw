# api-gateway/interfaces/http/middleware.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/middleware.py`
- 职责：实现请求追踪、鉴权、限流、能力过滤和统一响应信封中间件。
- 依赖：`网关与平台基础详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 辅助对象
- 类 `RequestContext(request_id: str, trace_id: str, client_type: str | None, client_version: str | None, user_id: str | None, token: str | None)`：请求上下文对象。
- 类 `RateLimitDecision(allowed: bool, scope: str, remaining: int, reset_at: datetime | None, reason_code: str | None)`：限流判定结果。
- 类 `CapabilityDecision(allowed: bool, capability_id: str | None, capability_status: str | None, degrade_strategy: str | None, message: str | None)`：能力开放判定结果。

## 函数
- `request_context_middleware(request: Request, call_next: Callable) -> Response`：注入 `request_id`、`trace_id`。
- `auth_middleware(request: Request, call_next: Callable) -> Response`：校验会话并透传身份。
- `rate_limit_middleware(request: Request, call_next: Callable) -> Response`：执行用户级、IP 级限流。
- `capability_filter_middleware(request: Request, call_next: Callable) -> Response`：校验能力开放状态。
- `response_normalizer_middleware(request: Request, call_next: Callable) -> Response`：包装统一响应信封。

## 细化函数
- `build_request_context(request: Request) -> RequestContext`：参数为 FastAPI 请求对象；返回请求上下文；职责是统一提取 `Authorization`、`X-Request-Id`、`X-Client-Type`、`X-Client-Version`。
- `resolve_or_create_request_id(request: Request) -> str`：参数为请求对象；返回请求 ID；职责是优先复用来路请求头，没有则生成新值。
- `resolve_trace_id(request: Request) -> str`：参数为请求对象；返回 trace ID；职责是优先提取链路上下文，没有则本地生成。
- `should_require_auth(request: Request) -> bool`：参数为请求对象；返回布尔值；职责是判断当前路由是否需要鉴权。
- `validate_access_token(token: str, required_scope: list[str] | None = None) -> dict`：参数为访问令牌和可选 scope；返回身份断言结果；职责是调用身份服务并把结果写入 request state。
- `build_rate_limit_key(request: Request, ctx: RequestContext) -> str`：参数为请求和上下文；返回限流 key；职责是按用户、IP、路径和客户端类型构造限流维度。
- `check_rate_limit(key: str, limit_name: str) -> RateLimitDecision`：参数为限流 key 和规则名；返回限流结果；职责是向 Redis 读取/写入计数并输出统一判定结构。
- `resolve_capability_id(request: Request) -> str | None`：参数为请求对象；返回能力 ID；职责是根据路由映射找到对应能力项。
- `check_capability(ctx: RequestContext, capability_id: str | None) -> CapabilityDecision`：参数为上下文和能力 ID；返回能力判定；职责是调用能力治理服务确认是否开放或降级。
- `normalize_success_response(request: Request, response: Response, payload: dict) -> Response`：参数为请求、原始响应和业务载荷；返回统一信封响应；职责是包装 `request_id`、`code`、`message`、`meta.trace_id`。
- `normalize_error_response(request: Request, exc: Exception) -> Response`：参数为请求和异常；返回统一错误响应；职责是把平台异常、下游异常和未知异常映射到稳定错误码。
- `prune_empty_fields(payload: dict | list | None) -> dict | list | None`：参数为响应体；返回清洗后的响应体；职责是移除不必要的空字段，但保留语义性空数组和布尔字段。

## 协作接口
- `IdentityGrpcClient.validate_session(access_token: str, required_scope: list[str] | None) -> SessionAssertion`：身份校验。
- `CapabilityRegistryClient.get_capability(capability_id: str, client_type: str, client_version: str | None) -> CapabilityView`：能力开放判断。
- `Redis.incr(key: str) -> int` / `Redis.expire(key: str, ttl: int) -> bool`：限流计数。

## 实现顺序
1. 先实现请求上下文和响应规范，再补鉴权与限流。
2. 下游错误在这里稳定映射，不直接暴露内部栈。
3. 把 `RequestContext` 放到 `request.state`，后续依赖函数和路由统一复用。
4. `capability_filter_middleware` 必须在鉴权之后执行，便于按用户、客户端版本和租户策略做精细判断。
5. `response_normalizer_middleware` 必须兜底处理普通 JSON、异常和 Streaming 场景，避免只支持 dict 响应。

## 集成检查
- 中间件实现要避免重复读取请求体导致的副作用。
- 统一响应必须始终包含 `request_id` 和 `code`，分页接口补 `meta.pagination`，能力降级补 `meta.capability_status`。
- 对登录类免鉴权接口，`auth_middleware` 要允许透传空 token 继续执行。
