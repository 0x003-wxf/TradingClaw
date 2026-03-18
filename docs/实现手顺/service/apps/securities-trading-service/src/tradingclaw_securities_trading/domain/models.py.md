# securities-trading-service/domain/models.py 实现手顺

- 目标代码文件：`apps/securities-trading-service/src/tradingclaw_securities_trading/domain/models.py`
- 职责：定义券商认证流程、券商会话、资产快照、持仓快照和原始订单记录。
- 依赖：`证券交易详细设计.md`

## 类
- 类 `BrokerAuthFlow(auth_flow_id: str, account_id: str, provider_code: str, current_step_code: str, flow_status: str, artifacts_ref: str | None)`：券商认证流程。
- 类 `BrokerSession(broker_session_id: str, account_id: str, adapter_session_status: str, expires_at: datetime | None, runtime_context_ref: str | None)`：券商会话。
- 类 `SecuritiesOrderRecord(channel_order_id: str, account_id: str, symbol: str, raw_status: str, request_ref: str | None, response_ref: str | None)`：原始订单记录。

## 函数
- `start(self) -> None`：认证流程启动。
- `require_next_step(self, step_code: str, artifacts_ref: str | None = None) -> None`：切换下一步。
- `mark_available(self) -> None`：券商会话可用。
- `mark_failed(self, reason_code: str) -> None`：认证或会话失败。

## 实现顺序
1. 动态验证码、短信码、OTP 不写入长期凭据，只存在流程上下文。
2. 运行时 HTTP session 只保留引用，不直接持久化到 MySQL。

## 集成检查
- 适配域只维护 `adapter_session_status`。
