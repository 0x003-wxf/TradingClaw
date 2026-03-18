# audit-risk-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/audit-risk-service/src/tradingclaw_audit_risk/interfaces/http/routes.py`
- 职责：暴露风险规则、复核任务和审计查询接口。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `list_rules(...) -> Envelope`：查询规则列表。
- `list_reviews(...) -> Envelope`：查询待复核任务。
- `complete_review(...) -> Envelope`：完成复核。
- `list_audit_logs(...) -> Envelope`：查询审计日志。

## 实现顺序
1. 管理接口建议单独鉴权和部署。

## 集成检查
- 复核接口必须留操作者和操作说明。
