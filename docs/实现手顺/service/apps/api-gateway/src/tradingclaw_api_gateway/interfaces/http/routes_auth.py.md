# api-gateway/interfaces/http/routes_auth.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/routes_auth.py`
- 职责：代理注册、登录、登出和会话校验入口。
- 依赖：`网关与平台基础详细设计.md`、`用户与账户详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `register(body: RegisterRequest, ctx: RequestContext) -> Envelope`：转发注册请求。
- `login(body: LoginRequest, ctx: RequestContext) -> Envelope`：转发登录请求。
- `logout(ctx: RequestContext, token: str) -> Envelope`：转发登出请求。
- `validate(ctx: RequestContext, token: str) -> Envelope`：转发会话校验请求。

## 实现顺序
1. 只做参数校验和下游转发，不在网关生成会话。
2. 响应体字段保持与身份服务一致。

## 集成检查
- 登录接口允许无 `Authorization`，其余接口按鉴权策略处理。
