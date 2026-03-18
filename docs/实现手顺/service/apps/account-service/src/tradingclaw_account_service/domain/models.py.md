# account-service/domain/models.py 实现手顺

- 目标代码文件：`apps/account-service/src/tradingclaw_account_service/domain/models.py`
- 职责：定义交易账户、绑定关系、凭据引用、默认交易会话引用和能力投影。
- 依赖：`用户与账户详细设计.md`、`状态字段枚举表.md`

## 类
- 类 `TradingAccount(account_id: str, account_type: str, provider_code: str, account_no: str, status: str)`：统一交易账户。
- 类 `AccountBinding(binding_id: str, user_id: str, account_id: str, binding_status: str)`：账户绑定关系。
- 类 `CredentialReference(credential_ref: str, provider_code: str, adapter_auth_mode: str, adapter_session_mode: str, challenge_mode: str, secret_schema: str, secret_path: str)`：凭据引用元数据。
- 类 `AccountCapabilityProjection(account_id: str, account_capability_status: str, capabilities: dict)`：能力投影。
- 类 `DefaultTradingSession(account_id: str, user_id: str, default_trading_session_id: str | None)`：默认会话引用。

## 函数
- `bind(self) -> None`：把绑定状态推进到 `BOUND`。
- `unbind(self) -> None`：解绑。
- `set_default_session(self, trading_session_id: str) -> None`：写入默认会话引用。
- `clear_default_session(self) -> None`：清理默认会话引用。
- `assert_owned_by(self, user_id: str) -> None`：校验账户归属。

## 实现顺序
1. 模型中明确区分绑定可用、能力可用、交易会话可用三层语义。
2. 不要在账户域镜像 `trading_session_status`。

## 集成检查
- 默认会话只是引用，真实会话状态必须从 trade-gateway 获取。
