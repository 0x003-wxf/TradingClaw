# api-gateway/interfaces/http/websocket_hub.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/websocket_hub.py`
- 职责：维护连接、订阅关系、鉴权和消息投递。
- 依赖：`网关与平台基础详细设计.md`

## 类
- 类 `ConnectionContext(connection_id: str, user_id: str, channels: set[str], resources: set[str])`：单连接上下文。
- 类 `WebSocketHub(redis_client: Redis, identity_client: IdentityGrpcClient)`：连接管理器。

## 函数
- `connect(socket: WebSocket, access_token: str) -> ConnectionContext`：握手并鉴权。
- `subscribe(ctx: ConnectionContext, channel: str, resource_id: str | None = None) -> None`：注册订阅。
- `unsubscribe(ctx: ConnectionContext, channel: str, resource_id: str | None = None) -> None`：取消订阅。
- `publish(channel: str, data: dict, resource_id: str | None = None, user_id: str | None = None) -> int`：推送消息并返回成功连接数。
- `disconnect(ctx: ConnectionContext) -> None`：清理连接与 Redis 映射。

## 实现顺序
1. 连接建立时做一次会话校验，订阅时再做一次资源级权限校验。
2. 连接态放 Redis，进程内只保留热点映射。
3. 推送失败要记录原因并清理僵尸连接。

## 集成检查
- 推送通道最先支持 `market.quote`、`trade.order`、`strategy.status`、`notification.system`。
