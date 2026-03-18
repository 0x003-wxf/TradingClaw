# news-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/news-service/src/tradingclaw_news_service/interfaces/http/routes.py`
- 职责：暴露资讯和评分查询接口。
- 依赖：`行情与资讯详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `list_news(...) -> Envelope`：资讯列表。
- `list_scores(...) -> Envelope`：评分列表。

## 实现顺序
1. 分页信息统一写入 `meta.pagination`。

## 集成检查
- 网关可直接代理这些接口。
