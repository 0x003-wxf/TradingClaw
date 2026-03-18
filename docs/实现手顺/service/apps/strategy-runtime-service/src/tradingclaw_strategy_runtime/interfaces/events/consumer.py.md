# strategy-runtime-service/interfaces/events/consumer.py 实现手顺

- 目标代码文件：`apps/strategy-runtime-service/src/tradingclaw_strategy_runtime/interfaces/events/consumer.py`
- 职责：消费行情、订单和复核事件，驱动策略运行时。
- 依赖：`策略系统详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `handle_quote_updated(envelope: EventEnvelope) -> None`：行情事件触发策略。
- `handle_order_changed(envelope: EventEnvelope) -> None`：订单状态事件刷新执行窗口。
- `handle_review_completed(envelope: EventEnvelope) -> None`：人工复核完成后恢复策略执行窗口。

## 实现顺序
1. 消费前做事件去重。
2. 只消费统一行情、统一订单事实和统一风控结论。

## 集成检查
- 恢复时通过 workflow signal 或 app service 完成。
