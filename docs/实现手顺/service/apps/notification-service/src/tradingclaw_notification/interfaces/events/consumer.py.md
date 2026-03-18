# notification-service/interfaces/events/consumer.py 实现手顺

- 目标代码文件：`apps/notification-service/src/tradingclaw_notification/interfaces/events/consumer.py`
- 职责：消费通知请求事件和风险/策略/交易事件，触发通知域处理。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `handle_send_requested(envelope: EventEnvelope) -> None`：处理 `notification.send_requested`。
- `handle_risk_review_required(envelope: EventEnvelope) -> None`：将高风险复核事件映射为通知。
- `handle_order_status_changed(envelope: EventEnvelope) -> None`：将订单关键状态事件映射为通知。

## 实现顺序
1. consumer 先做事件去重，再调 app service。
2. 事件到通知命令的映射关系要配置化。

## 集成检查
- 通知投递失败不应阻塞原业务事件消费确认。
