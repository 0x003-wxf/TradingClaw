# contracts-events/risk.py 实现手顺

- 目标代码文件：`packages/contracts-events/src/tradingclaw_contracts_events/risk.py`
- 职责：定义风控、复核和恢复引用事件。
- 依赖：`风控审计与通知详细设计.md`、`事件字段字典.md`

## 类
- 类 `RiskReviewRequiredPayload(risk_event_id: str, resource_type: str, resource_id: str, workflow_id: str | None, resume_token: str | None)`：人工复核请求事件。
- 类 `RiskReviewCompletedPayload(risk_event_id: str, decision: str, resource_type: str, resource_id: str, workflow_id: str | None, resume_token: str | None)`：复核完成事件。

## 函数
- `build_review_required(...) -> RiskReviewRequiredPayload`：构造复核请求事件。
- `build_review_completed(...) -> RiskReviewCompletedPayload`：构造复核完成事件。

## 实现顺序
1. 保证 `risk_event_id`、`resource_id`、`resume_token` 始终存在于恢复链路。
2. 事件里只放恢复必要字段。

## 集成检查
- trade-gateway、strategy-runtime、notification-service 会消费这些事件。
