# contracts-events/envelope.py 实现手顺

- 目标代码文件：`packages/contracts-events/src/tradingclaw_contracts_events/envelope.py`
- 职责：定义事件 payload 共用基类和强类型 Schema。
- 依赖：`事件字段字典.md`、`API字段字典.md`

## 类
- 类 `BaseEventPayload(request_id: str | None, trace_id: str | None, operator_id: str | None)`：所有 payload 公共字段。

## 函数
- `payload_schema(model_type: type[BaseModel]) -> dict`：导出 JSON Schema。
- `validate_payload(model_type: type[BaseModel], payload: dict) -> BaseModel`：对消费到的 payload 做强校验。

## 实现顺序
1. 先定义公共基类，再让交易、风险、策略等事件模型继承。
2. 导出的 Schema 供 Schema Registry 和文档生成复用。

## 集成检查
- 事件生产和消费两端都要强制使用这里的模型。
