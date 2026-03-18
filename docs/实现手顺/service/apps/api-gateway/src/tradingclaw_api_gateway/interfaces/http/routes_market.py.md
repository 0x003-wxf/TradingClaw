# api-gateway/interfaces/http/routes_market.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/routes_market.py`
- 职责：代理行情与资讯查询入口。
- 依赖：`网关与平台基础详细设计.md`、`行情与资讯详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `get_quotes(...) -> Envelope`：代理实时行情查询。
- `get_history(...) -> Envelope`：代理历史行情查询。
- `get_indicators(...) -> Envelope`：代理指标计算查询。
- `list_news(...) -> Envelope`：代理资讯列表。
- `list_news_scores(...) -> Envelope`：代理资讯评分列表。

## 实现顺序
1. 标准查询优先使用 `instrument_id`。
2. 分页信息通过 `meta.pagination` 返回。

## 集成检查
- 网关不做行情缓存主逻辑，只做能力过滤和转发。
