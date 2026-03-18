# trade-gateway-service/interfaces/events/consumer.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/interfaces/events/consumer.py`
- 职责：消费适配侧执行回报和风险复核完成事件。
- 依赖：`交易网关详细设计.md`、`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `handle_execution_report(envelope: EventEnvelope) -> None`：消费 `execution.report_received`。
- `handle_review_completed(envelope: EventEnvelope) -> None`：消费 `risk.review_completed`。

## 实现顺序
1. 消费前做事件去重。
2. 消费者只恢复原上下文，不重新拼装订单请求。

## 集成检查
- 事件处理失败要支持重试和死信。
