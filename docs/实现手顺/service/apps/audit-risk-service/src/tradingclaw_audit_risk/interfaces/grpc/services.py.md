# audit-risk-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/audit-risk-service/src/tradingclaw_audit_risk/interfaces/grpc/services.py`
- 职责：实现风险裁决 gRPC 服务。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `EvaluateOrderRisk(request, context) -> EvaluateRiskResponse`：订单风控裁决。
- `EvaluateStrategyRisk(request, context) -> EvaluateRiskResponse`：策略风控裁决。
- `CompleteReview(request, context) -> CompleteReviewResponse`：复核完成。

## 实现顺序
1. gRPC 层只做请求反序列化和响应序列化。
2. 决策字段统一使用 `ALLOW`、`REJECT`、`REVIEW`。

## 集成检查
- trade-gateway 和 strategy-runtime 依赖本接口。
