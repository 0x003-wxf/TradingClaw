# api-gateway/bootstrap/roles/websocket.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/bootstrap/roles/websocket.py`
- 职责：装配 WebSocket 入口和订阅路由。
- 依赖：`网关与平台基础详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `build_websocket_app(context: BootstrapContext) -> FastAPI`：构建 websocket 角色应用。
- `mount_websocket_routes(app: FastAPI, hub: WebSocketHub) -> None`：注册 websocket 路由。

## 实现顺序
1. 把 WebSocket 与普通 HTTP 分角色部署。
2. 连接建链前必须先准备认证 client 和 Redis 订阅映射。

## 集成检查
- 长连接和普通 HTTP 不要共用同一 Deployment。
