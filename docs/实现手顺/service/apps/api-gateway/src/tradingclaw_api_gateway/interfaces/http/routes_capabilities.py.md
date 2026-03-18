# api-gateway/interfaces/http/routes_capabilities.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/routes_capabilities.py`
- 职责：代理平台能力矩阵查询。
- 依赖：`网关与平台基础详细设计.md`、`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `list_capabilities(ctx: RequestContext, token: str | None = None) -> Envelope`：查询能力列表。
- `get_capability(capability_id: str, ctx: RequestContext) -> Envelope`：查询单能力详情。

## 实现顺序
1. 能力查询结果要带 `capability_status` 和降级策略。

## 集成检查
- 能力注册中心是唯一来源，网关不得本地硬编码开放矩阵。
