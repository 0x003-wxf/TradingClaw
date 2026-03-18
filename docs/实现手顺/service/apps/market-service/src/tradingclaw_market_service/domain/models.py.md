# market-service/domain/models.py 实现手顺

- 目标代码文件：`apps/market-service/src/tradingclaw_market_service/domain/models.py`
- 职责：定义标的、实时行情、历史 K 线和指标序列模型。
- 依赖：`行情与资讯详细设计.md`

## 类
- 类 `Instrument(instrument_id: str, symbol: str, market: str, asset_class: str, status: str)`：标的主数据。
- 类 `Quote(instrument_id: str, price: Decimal, timestamp: datetime, source: str, data_status: str)`：实时行情。
- 类 `Kline(instrument_id: str, period: str, bar_time: datetime, open: Decimal, high: Decimal, low: Decimal, close: Decimal, volume: Decimal)`：K 线。
- 类 `IndicatorSeries(instrument_id: str, indicator_type: str, window: int, points: list[dict], calculable: bool, reason: str | None)`：指标结果。

## 函数
- `assert_active(self) -> None`：校验标的状态可用。
- `mark_cache_fallback(self) -> None`：把行情数据状态标记为缓存回退。
- `build_uncalculable(instrument_id: str, indicator_type: str, window: int, reason: str) -> IndicatorSeries`：构造不可计算结果。

## 实现顺序
1. `instrument_id` 是主键，`symbol + market` 只作兼容定位。
2. Quote 和 IndicatorSeries 都必须带可用性语义。

## 集成检查
- 历史高频明细不要在领域模型里绑定 MySQL 实现细节。
