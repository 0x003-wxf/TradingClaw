# market-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/market-service/src/tradingclaw_market_service/interfaces/grpc/services.py`
- 职责：实现行情域 gRPC 服务。
- 依赖：`行情与资讯详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `GetQuotes(request, context) -> GetQuotesResponse`：查询实时行情。
- `GetHistory(request, context) -> GetHistoryResponse`：查询历史行情。
- `CalculateIndicator(request, context) -> CalculateIndicatorResponse`：计算指标。
- `SearchInstruments(request, context) -> SearchInstrumentsResponse`：搜索标的。

## 实现顺序
1. gRPC 与 HTTP 使用相同字段口径。

## 集成检查
- trade-gateway、strategy-runtime、ai-orchestration 会依赖本接口。
