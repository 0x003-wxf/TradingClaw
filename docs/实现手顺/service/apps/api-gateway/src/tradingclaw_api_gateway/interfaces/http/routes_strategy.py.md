# api-gateway/interfaces/http/routes_strategy.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/routes_strategy.py`
- 职责：代理策略创建、查询、修改和停止入口。
- 依赖：`网关与平台基础详细设计.md`、`策略系统详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `create_strategy(...) -> Envelope`：代理策略创建。
- `list_strategies(...) -> Envelope`：代理策略列表。
- `get_strategy(...) -> Envelope`：代理策略详情。
- `update_strategy(...) -> Envelope`：代理策略更新。
- `stop_strategy(...) -> Envelope`：代理策略停止。

## 实现顺序
1. 策略写接口透传 `X-Idempotency-Key`。
2. 未开放策略能力时返回能力降级说明。

## 集成检查
- 策略运行时状态由下游服务返回，网关不重算。
