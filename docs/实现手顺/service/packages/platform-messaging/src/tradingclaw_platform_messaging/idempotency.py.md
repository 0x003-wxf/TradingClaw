# platform-messaging/idempotency.py 实现手顺

- 目标代码文件：`packages/platform-messaging/src/tradingclaw_platform_messaging/idempotency.py`
- 职责：实现幂等键检查、消费者去重和回放保护。
- 依赖：`后端详细设计.md` 中 at-least-once 约束

## 类
- 类 `IdempotencyRecord(scope: str, key: str, resource_id: str, status: str, created_at: datetime)`：幂等记录。

## 函数
- `check_command_key(scope: str, key: str) -> IdempotencyRecord | None`：查询命令幂等键。
- `reserve_command_key(scope: str, key: str, resource_id: str) -> IdempotencyRecord`：预占幂等键。
- `mark_command_finished(scope: str, key: str, status: str) -> None`：标记命令完成。
- `dedupe_event(event_id: str, consumer_group: str) -> bool`：事件去重。

## 实现顺序
1. 命令幂等与事件去重分别建模，不要混用。
2. 同一幂等键命中时必须返回原资源 ID。

## 集成检查
- 交易下单、账户绑定、策略创建和通知发送请求都要使用本文件。
