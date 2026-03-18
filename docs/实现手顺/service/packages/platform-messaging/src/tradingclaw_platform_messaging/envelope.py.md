# platform-messaging/envelope.py 实现手顺

- 目标代码文件：`packages/platform-messaging/src/tradingclaw_platform_messaging/envelope.py`
- 职责：定义统一事件信封、头字段和序列化规则。
- 依赖：`事件字段字典.md`

## 类
- 类 `EventEnvelope(event_type: str, event_version: int, event_id: str, occurred_at: datetime, producer: str, partition_key: str, trace_id: str | None, request_id: str | None, payload: dict, meta: dict | None)`：标准事件信封。

## 函数
- `build_envelope(event_type: str, partition_key: str, payload: dict, producer: str, request_id: str | None = None, trace_id: str | None = None, meta: dict | None = None) -> EventEnvelope`：构建事件。
- `serialize(envelope: EventEnvelope) -> bytes`：序列化事件。
- `deserialize(raw: bytes) -> EventEnvelope`：反序列化事件。

## 实现顺序
1. 冻结信封字段，所有领域事件都必须共用。
2. `partition_key` 按模块设计使用稳定键。

## 集成检查
- contracts-events 包中的事件 payload 模型应嵌入到这里的 `payload`。
