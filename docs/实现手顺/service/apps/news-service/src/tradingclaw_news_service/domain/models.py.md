# news-service/domain/models.py 实现手顺

- 目标代码文件：`apps/news-service/src/tradingclaw_news_service/domain/models.py`
- 职责：定义资讯实体、去重键和评分结果模型。
- 依赖：`行情与资讯详细设计.md`

## 类
- 类 `NewsItem(news_id: str, source_id: str, title: str, summary: str, published_at: datetime, dedup_key: str)`：标准资讯实体。
- 类 `NewsDedupKey(news_id: str, dedup_key: str, fingerprint: str)`：去重索引。
- 类 `NewsScore(news_id: str, model_version: str, score: Decimal, reason: str | None, scored_at: datetime)`：评分结果。

## 函数
- `build_dedup_key(title: str, published_at: datetime, source_id: str) -> str`：生成去重键。
- `mark_scored(self, score: Decimal, reason: str) -> None`：标记评分结果。

## 实现顺序
1. 同题多源资讯需按标题指纹、发布时间窗口和来源等级合并。

## 集成检查
- 评分模型版本和来源必须可审计。
