# audit-risk-service/app/review_service.py 实现手顺

- 目标代码文件：`apps/audit-risk-service/src/tradingclaw_audit_risk/app/review_service.py`
- 职责：实现人工复核受理、完成和恢复引用发布。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `create_review(risk_event_id: str, reviewer_pool: str | None = None) -> Result[ManualReview]`：创建复核任务。
- `complete_review(review_id: str, decision: str, reviewer_id: str, comment: str | None) -> Result[ManualReview]`：完成复核。
- `publish_review_completed(review: ManualReview, risk_event: RiskEvent) -> None`：发布 `risk.review_completed`。

## 实现顺序
1. 复核完成后先落库，再发事件。
2. 恢复事件必须带 `risk_event_id`、`resource_id`、`workflow_id`、`resume_token`。

## 集成检查
- 恢复原流程由 trade-gateway 或 strategy-runtime 负责。
