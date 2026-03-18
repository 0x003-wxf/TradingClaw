# market-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/market-service/src/tradingclaw_market_service/interfaces/http/routes.py`
- 职责：暴露市场数据查询 HTTP 接口。
- 依赖：`行情与资讯详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `get_quotes(...) -> Envelope`：返回 `quotes` 和 `data_status`。
- `get_history(...) -> Envelope`：返回历史 K 线。
- `get_indicators(...) -> Envelope`：返回指标序列和 `calculable`。
- `search_instruments(...) -> Envelope`：关键字搜索。

## 实现顺序
1. 对外保持 `instrument_id` 优先，兼容 `symbol + market`。
2. 分页和 limit 参数统一限制上限。

## 集成检查
- 接口返回体字段必须复用 `API字段字典.md`。
