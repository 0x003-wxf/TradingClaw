# market-service/infra/clients/providers.py 实现手顺

- 目标代码文件：`apps/market-service/src/tradingclaw_market_service/infra/clients/providers.py`
- 职责：封装外部行情供应商 client。
- 依赖：`行情与资讯详细设计.md`

## 类
- 类 `MarketProviderClient(provider_code: str, timeout_seconds: int)`：行情源抽象基类。
- 类 `ProviderQuoteResult(quotes: list[Quote], source: str, latency_ms: int)`：供应商返回对象。

## 函数
- `fetch_quotes(instrument_ids: list[str]) -> ProviderQuoteResult`：拉取实时行情。
- `fetch_history(instrument_id: str, period: str, start_at: datetime | None, end_at: datetime | None) -> list[Kline]`：拉取历史行情。
- `healthcheck() -> bool`：检查供应商连通性。

## 实现顺序
1. 先定义统一抽象，再接入具体供应商。
2. client 层只返回标准化对象。

## 集成检查
- 主备切换逻辑在 app 层，不在 client 层硬编码。
