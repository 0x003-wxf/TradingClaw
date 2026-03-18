# audit-risk-service/app/audit_service.py 实现手顺

- 目标代码文件：`apps/audit-risk-service/src/tradingclaw_audit_risk/app/audit_service.py`
- 职责：实现审计旁路写入和审计查询。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `record_event(resource_ref: str, action_type: str, payload: dict, trace_id: str | None) -> Result[AuditLog]`：写审计日志。
- `search_logs(resource_ref: str | None, action_type: str | None, page: int, page_size: int) -> Result[PageResult[AuditLog]]`：查询审计日志。

## 实现顺序
1. 审计日志只追加，不静默删除。
2. 关键字段至少关联 `trace_id`、`request_id`、`resource_ref`。

## 集成检查
- 审计是旁路事实，不成为业务主状态来源。
