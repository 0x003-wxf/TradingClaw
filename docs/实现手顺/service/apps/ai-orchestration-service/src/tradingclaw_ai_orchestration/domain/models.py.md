# ai-orchestration-service/domain/models.py 实现手顺

- 目标代码文件：`apps/ai-orchestration-service/src/tradingclaw_ai_orchestration/domain/models.py`
- 职责：定义模型配置、会话和工具调用记录。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 类 `ModelConfig(model_id: str, provider_code: str, route_name: str, enabled: bool, timeout_seconds: int)`：模型配置。
- 类 `Conversation(conversation_id: str, user_id: str, model_id: str, status: str, context_scope: list[str])`：AI 会话。
- 类 `ToolCall(tool_call_id: str, conversation_id: str, tool_name: str, resource_ref: str | None, status: str)`：工具调用记录。

## 函数
- `start(self) -> None`：会话激活。
- `record_tool_call(self, tool_name: str, resource_ref: str | None) -> ToolCall`：登记工具调用。
- `complete_tool_call(self, status: str) -> None`：结束工具调用。

## 实现顺序
1. AI 会话和工具调用都必须可审计。
2. AI 结果不持有交易主状态。

## 集成检查
- 会话长文本建议落对象存储，只在模型中保留摘要索引。
