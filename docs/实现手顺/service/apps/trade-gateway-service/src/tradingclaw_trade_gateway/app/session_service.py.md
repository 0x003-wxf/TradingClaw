# trade-gateway-service/app/session_service.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/app/session_service.py`
- 职责：实现创建交易会话、提交认证步骤、刷新会话和关闭会话。
- 依赖：`交易网关详细设计.md`、`证券交易详细设计.md`、`数字资产交易详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 输入输出对象
- 类 `CreateSessionCommand(user_id: str, account_id: str, set_as_default: bool, request_id: str, trace_id: str | None, client_type: str | None)`：创建会话命令对象。
- 类 `SubmitAuthStepCommand(user_id: str, trading_session_id: str, auth_flow_id: str, step_code: str, fields: dict, request_id: str)`：提交认证步骤命令对象。
- 类 `TradingSessionResponse(trading_session_id: str, status: str, challenge_required: bool, auth_flow_id: str | None, required_fields: list[dict] | None, artifacts: dict | None, default_trading_session_written: bool | None)`：统一交易会话返回体。

## 函数
- `create_session(user_id: str, account_id: str, set_as_default: bool, request_id: str) -> Result[TradingSessionResponse]`：创建或复用统一交易会话。
- `submit_auth_step(user_id: str, trading_session_id: str, auth_flow_id: str, step_code: str, fields: dict) -> Result[TradingSessionResponse]`：提交认证步骤。
- `refresh_session(user_id: str, trading_session_id: str) -> Result[TradingSessionResponse]`：刷新会话。
- `close_session(user_id: str, trading_session_id: str) -> Result[dict]`：关闭会话。

## 细化函数
- `load_account_context(user_id: str, account_id: str) -> TradingAccountContext`：参数为用户 ID、账户 ID；返回账户上下文；职责是调用账户服务获取归属、能力投影、凭据元数据和默认会话引用。
- `load_or_create_trading_session(account_context: TradingAccountContext) -> TradingSession`：参数为账户上下文；返回统一会话；职责是复用已有活跃会话或创建新会话主记录。
- `build_adapter_session_command(account_context: TradingAccountContext, trading_session: TradingSession) -> dict`：参数为账户上下文和统一会话；返回适配器请求对象；职责是把 `provider_code`、`adapter_auth_mode`、`adapter_session_mode`、`credential_ref` 转成统一适配调用参数。
- `handle_adapter_session_result(trading_session: TradingSession, adapter_result: dict, set_as_default: bool) -> TradingSessionResponse`：参数为统一会话、适配器结果、是否设默认；返回最终响应对象；职责是根据一步成功或多步认证结果推进状态并组装返回体。
- `create_auth_flow(trading_session: TradingSession, adapter_result: dict) -> TradingSessionAuthFlow`：参数为统一会话和适配结果；返回认证流程对象；职责是保存 `step_code`、`required_fields`、`artifacts`、`challenge_required`。
- `validate_auth_step_payload(auth_flow: TradingSessionAuthFlow, step_code: str, fields: dict) -> None`：参数为认证流程、步骤编码、用户输入；无返回值；职责是校验当前步骤和输入字段是否匹配。
- `apply_auth_step_result(trading_session: TradingSession, auth_flow: TradingSessionAuthFlow, adapter_result: dict) -> TradingSessionResponse`：参数为统一会话、认证流程、适配返回；返回响应对象；职责是推进下一步或完成会话建链。
- `write_default_session_if_needed(user_id: str, account_id: str, trading_session: TradingSession, set_as_default: bool) -> bool`：参数为用户、账户、统一会话和设默认标记；返回是否成功写入默认会话引用；职责是仅在 `AVAILABLE` 时调用账户服务落默认引用。
- `archive_completed_auth_flow(auth_flow_id: str, final_status: str) -> None`：参数为认证流程 ID 和终态；无返回值；职责是在成功、失败、超时后归档流程上下文。
- `build_session_response(trading_session: TradingSession, auth_flow: TradingSessionAuthFlow | None = None, default_written: bool | None = None) -> TradingSessionResponse`：参数为统一会话、可选认证流程和默认写入标记；返回响应对象；职责是统一输出字段格式。

## 仓储与外部协作
- `TradingSessionRepository.save(session: TradingSession) -> TradingSession`：保存统一交易会话。
- `TradingSessionAuthFlowRepository.save(flow: TradingSessionAuthFlow) -> TradingSessionAuthFlow`：保存步骤式认证流程。
- `AccountClient.assert_account_ownership(user_id: str, account_id: str, include_capability: bool = True) -> OwnershipAssertion`：账户归属断言。
- `AdapterClientRouter.create_adapter_session(account_context: dict) -> AdapterSessionResult`：发起适配侧建链。
- `AdapterClientRouter.submit_adapter_auth_step(auth_flow_id: str, step_code: str, fields: dict) -> AdapterSessionResult`：推进适配侧认证步骤。

## 实现顺序
1. 先做账户归属断言，再读取凭据元数据决定路由哪个适配器。
2. 一步建链成功时直接推进为 `AVAILABLE`；多步流程则创建 `auth_flow` 返回 `required_fields`。
3. `submit_auth_step` 只能推进当前 `auth_flow` 的下一步，不允许跳步骤或并发提交旧步骤。
4. `refresh_session` 与 `create_session` 复用同一套适配路由和结果映射，但要保留原 `trading_session_id`。
5. `close_session` 先调用适配域释放运行时上下文，再把统一交易会话推进为 `CLOSED`，最后视情况清理默认会话引用。

## 集成检查
- 设置默认会话时必须先确认统一会话已 `AVAILABLE`。
- 返回给前端/CLI 的 `required_fields` 和 `artifacts` 必须足够直接渲染当前认证步骤。
- `credential_ref` 只读元数据，不接收验证码、短信码、OTP 等动态输入长期持久化。
