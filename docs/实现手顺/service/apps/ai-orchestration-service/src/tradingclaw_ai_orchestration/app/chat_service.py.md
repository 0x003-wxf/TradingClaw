# ai-orchestration-service/app/chat_service.py 实现手顺

- 目标代码文件：`apps/ai-orchestration-service/src/tradingclaw_ai_orchestration/app/chat_service.py`
- 职责：实现问答编排、上下文聚合、脱敏和工具调用。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 输入输出对象
- 类 `ChatRequest(user_id: str, conversation_id: str | None, question: str, context_scope: list[str] | None, tool_preference: list[str] | None, client_type: str | None, client_version: str | None, request_id: str)`：AI 问答请求对象。
- 类 `ChatResponse(conversation_id: str, answer: str, tool_calls: list[dict], model_name: str, usage: dict | None)`：AI 问答响应对象。
- 类 `ToolExecutionPlan(tool_name: str, arguments: dict, resource_ref: str | None, allowed: bool, reason_code: str | None)`：工具调用计划对象。

## 函数
- `chat(user_id: str, conversation_id: str | None, question: str, context_scope: list[str] | None, tool_preference: list[str] | None) -> Result[ChatResponse]`：执行 AI 问答。
- `load_capabilities(client_type: str, client_version: str) -> Result[dict]`：查询当前可用能力。
- `build_context(user_id: str, context_scope: list[str]) -> Result[dict]`：聚合行情、资讯、策略、交易上下文。
- `run_tools(conversation_id: str, tool_calls: list[dict]) -> Result[list[ToolCall]]`：执行工具链。
- `sanitize_context(payload: dict) -> dict`：对上下文做脱敏和最小必要裁剪。

## 细化函数
- `load_or_create_conversation(user_id: str, conversation_id: str | None) -> Conversation`：参数为用户 ID 和可选会话 ID；返回会话对象；职责是复用既有会话或创建新会话。
- `resolve_model_route(user_id: str, context_scope: list[str], tool_preference: list[str] | None) -> ModelConfig`：参数为用户、上下文范围和工具偏好；返回模型配置；职责是根据 LiteLLM 路由和能力矩阵挑选模型。
- `load_market_context(user_id: str) -> dict`：参数为用户 ID；返回行情上下文；职责是聚合热点行情、持仓关联标的和基础市场摘要。
- `load_news_context(user_id: str) -> dict`：参数为用户 ID；返回资讯上下文；职责是聚合相关新闻、评分和最新资讯摘要。
- `load_strategy_context(user_id: str) -> dict`：参数为用户 ID；返回策略上下文；职责是聚合策略状态、异常执行窗口和最近快照摘要。
- `load_trade_context(user_id: str) -> dict`：参数为用户 ID；返回交易上下文；职责是聚合账户、订单和交易会话摘要，但不带敏感凭据字段。
- `sanitize_trade_context(payload: dict) -> dict`：参数为交易上下文；返回脱敏结果；职责是清理账户号、密钥引用、原始报文和高风险写操作细节。
- `build_prompt_messages(question: str, context: dict, conversation: Conversation) -> list[dict]`：参数为问题、上下文和会话；返回模型 messages；职责是组装系统提示词、历史消息和可控上下文片段。
- `plan_tool_calls(model_output: dict, capabilities: dict) -> list[ToolExecutionPlan]`：参数为模型输出和能力矩阵；返回工具调用计划；职责是对模型建议的工具调用做白名单校验。
- `execute_single_tool(conversation_id: str, plan: ToolExecutionPlan) -> ToolCall`：参数为会话 ID 和单个工具计划；返回工具调用记录；职责是执行具体工具、记录输入输出引用和状态。
- `persist_tool_call(tool_call: ToolCall) -> ToolCall`：参数为工具调用对象；返回持久化后的对象；职责是写 `ai_tool_calls` 主记录。
- `build_chat_response(conversation: Conversation, answer: str, tool_calls: list[ToolCall], model_name: str, usage: dict | None) -> ChatResponse`：参数为会话、回答、工具调用、模型名和用量；返回最终响应对象。

## 仓储与外部协作
- `ConversationRepository.save(conversation: Conversation) -> Conversation`：保存 AI 会话。
- `ToolCallRepository.save(tool_call: ToolCall) -> ToolCall`：保存工具调用记录。
- `CapabilityRegistryClient.list_capabilities(client_type: str, client_version: str | None) -> dict`：读取能力矩阵。
- `LiteLLMClient.chat(model: str, messages: list[dict], tools: list[dict] | None = None) -> dict`：调用大模型服务。
- `MarketClient`、`NewsClient`、`StrategyClient`、`TradeGatewayClient`：按上下文范围聚合外部业务上下文。

## 实现顺序
1. 先实现能力查询与上下文聚合，再接 LiteLLM。
2. 入模前必须执行脱敏和权限裁剪。
3. 工具调用必须先经过能力矩阵、工具白名单和资源权限校验，不能直接执行模型原样返回的调用请求。
4. AI 会话和工具调用要写审计记录，尤其是涉及交易、账户和 OCR 场景时。
5. 模型超时、工具失败或上下文服务异常时要返回可读降级结果，而不是直接中断整个聊天链路。

## 集成检查
- AI 结果可增强，但不能越权执行交易写操作。
- 入模前的上下文必须经过最小必要字段裁剪，禁止把 `credential_ref`、原始报文、密钥引用或完整交易流水直接送入模型。
- 工具调用事件要能通过 `resource_ref` 追踪到具体业务对象，便于审计和排障。
