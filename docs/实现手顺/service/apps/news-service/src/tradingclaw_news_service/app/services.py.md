# news-service/app/services.py 实现手顺

- 目标代码文件：`apps/news-service/src/tradingclaw_news_service/app/services.py`
- 职责：实现多源资讯抓取、标准化、去重、评分和查询。
- 依赖：`行情与资讯详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `ingest_news(source_id: str, since: datetime | None = None) -> Result[int]`：抓取并入库资讯。
- `score_news(news_id: str, model_version: str) -> Result[NewsScore]`：执行影响力评分。
- `list_news(keyword: str | None, source_id: str | None, page: int, page_size: int) -> Result[PageResult[NewsItem]]`：分页查询资讯。
- `list_scores(news_id: str | None, min_score: Decimal | None, page: int, page_size: int) -> Result[PageResult[NewsScore]]`：分页查询评分。

## 实现顺序
1. 先打通抓取、标准化和去重，再补评分。
2. 抓取与评分完成后分别发布 `news.ingested`、`news.scored`。

## 集成检查
- 资讯正文可存对象存储或搜索引擎，MySQL 保留索引和摘要。
