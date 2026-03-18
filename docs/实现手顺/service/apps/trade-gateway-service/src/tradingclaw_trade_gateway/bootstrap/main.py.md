# trade-gateway-service/bootstrap/main.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/bootstrap/main.py`
- 职责：注册服务角色并连接平台 bootstrap。
- 依赖：`运行启动与环境编排详细设计.md`、对应模块详细设计

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `register_roles(registry: RoleRegistry) -> None`：注册本服务支持的角色。
- `main(argv: list[str] | None = None) -> int`：服务启动入口。

## 实现顺序
1. 按文档为 `trade-gateway-service` 注册角色：`http-api`、`grpc-api`、`event-consumer`、`temporal-worker`、`migration-job`。
2. 角色 builder 指向对应 `bootstrap/roles` 或 `interfaces/workers` 的装配函数。
3. `main` 只调用平台 runner，不直接启动业务服务器。

## 集成检查
- 新增角色时必须同步更新 `运行启动与环境编排详细设计.md` 和本文件手顺。
