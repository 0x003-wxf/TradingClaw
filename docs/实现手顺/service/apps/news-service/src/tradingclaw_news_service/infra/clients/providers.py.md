# news-service/infra/clients/providers.py 实现手顺

- 目标代码文件：`apps/news-service/src/tradingclaw_news_service/infra/clients/providers.py`
- 职责：封装外部资讯源 client。
- 依赖：`行情与资讯详细设计.md`

## 类
- 类 `NewsProviderClient(source_id: str, timeout_seconds: int)`：资讯源抽象 client。

## 函数
- `fetch_items(since: datetime | None = None, page_token: str | None = None) -> tuple[list[dict], str | None]`：抓取资讯列表。
- `fetch_detail(source_news_id: str) -> dict`：抓取资讯详情。
- `healthcheck() -> bool`：检测供应商可用性。

## 实现顺序
1. 抓取能力至少支持列表、详情、时间范围或分页轮询。

## 集成检查
- 外部字段到标准字段的映射放在 app 层或 mapper 层。
