# api-gateway/bootstrap/roles/http_api.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/bootstrap/roles/http_api.py`
- 职责：装配网关 HTTP 角色。
- 依赖：`网关与平台基础详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `build_http_app(context: BootstrapContext) -> FastAPI`：装配 FastAPI 应用。
- `configure_middlewares(app: FastAPI, settings: GatewaySettings) -> None`：注册 request_id、trace_id、auth、rate-limit、response-normalizer。
- `mount_routes(app: FastAPI) -> None`：挂载 auth、account、market、trade、strategy、ai、capabilities 路由。

## 实现顺序
1. 先装配基础依赖 client，再注册中间件，再挂路由。
2. 中间件顺序必须保证 request_id/trace_id 最先，响应规范最后。
3. 把健康检查和版本信息接口一并挂载。

## 集成检查
- 对外 HTTP 入口只做网关逻辑，不直连数据库主表。
