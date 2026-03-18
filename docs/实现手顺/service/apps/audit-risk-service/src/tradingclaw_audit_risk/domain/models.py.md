# audit-risk-service/domain/models.py 实现手顺

- 目标代码文件：`apps/audit-risk-service/src/tradingclaw_audit_risk/domain/models.py`
- 职责：定义风险规则、风险事件、人工复核和审计日志模型。
- 依赖：`风控审计与通知详细设计.md`、`状态字段枚举表.md`

## 类
- 类 `RiskRule(rule_code: str, rule_version: int, status: str, priority: int, decision: str, matcher: dict)`：风险规则。
- 类 `RiskEvent(risk_event_id: str, rule_code: str, resource_type: str, resource_id: str | None, resource_ref: str, severity: str, status: str, decision: str)`：风险事件。
- 类 `ManualReview(review_id: str, risk_event_id: str, status: str, decision: str | None, reviewer_id: str | None)`：人工复核。
- 类 `AuditLog(audit_log_id: str, resource_ref: str, action_type: str, payload: dict, trace_id: str | None)`：审计日志。

## 函数
- `require_review(self) -> None`：风险事件进入 `PENDING_REVIEW`。
- `confirm(self, decision: str) -> None`：风险事件确认。
- `close(self) -> None`：风险事件关闭。
- `record_action(self, action_type: str, payload: dict) -> None`：审计日志追加。

## 实现顺序
1. 风险事件状态机和复核状态机分开实现。
2. `resource_ref`、`workflow_id`、`resume_token` 必须可追溯。

## 集成检查
- 治理域只提供裁决和复核，不直接修改订单或策略主状态。
