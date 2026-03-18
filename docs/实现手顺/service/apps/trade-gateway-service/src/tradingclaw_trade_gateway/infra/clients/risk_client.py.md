# trade-gateway-service/infra/clients/risk_client.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/infra/clients/risk_client.py`
- 职责：封装风险裁决服务 gRPC client。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `evaluate_order_risk(command: RiskCheckCommand) -> RiskDecisionView`：发起订单风控。
- `evaluate_strategy_risk(command: RiskCheckCommand) -> RiskDecisionView`：发起策略风控。

## 实现顺序
1. risk client 只负责协议转换和错误映射。

## 集成检查
- 资源引用字段必须完整透传。
