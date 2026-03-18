# market-service/app/services.py 实现手顺

- 目标代码文件：`apps/market-service/src/tradingclaw_market_service/app/services.py`
- 职责：实现标的查询、实时行情、历史行情、指标计算和回补编排。
- 依赖：`行情与资讯详细设计.md`、`后端需求文档.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `search_instruments(keyword: str, market: str | None = None) -> Result[list[Instrument]]`：按关键字搜索标的。
- `get_quotes(instrument_ids: list[str] | None = None, symbols: list[str] | None = None, market: str | None = None, with_depth: bool = False) -> Result[QuoteResponse]`：查询实时行情。
- `get_history(instrument_id: str | None, symbol: str | None, market: str | None, period: str, start_at: datetime | None, end_at: datetime | None, limit: int | None) -> Result[HistoryResponse]`：查询历史行情。
- `calculate_indicator(instrument_id: str, indicator_type: str, window: int, period: str) -> Result[IndicatorSeries]`：计算指标。
- `backfill_history(instrument_id: str, period: str, start_at: datetime, end_at: datetime) -> Result[dict]`：发起历史回补。

## 实现顺序
1. 先完成 `get_quotes` 和 `get_history`，再补指标和回补流程。
2. 实时行情优先读缓存，外部源失败时回退最近成功缓存并标记 `data_status`。

## 集成检查
- 指标计算样本不足时返回 `calculable=false`。
