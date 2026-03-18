# account-service/infra/db/repositories.py 实现手顺

- 目标代码文件：`apps/account-service/src/tradingclaw_account_service/infra/db/repositories.py`
- 职责：实现账户、绑定、凭据引用、能力投影和默认会话引用仓储。
- 依赖：`用户与账户详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `save_trading_account(account: TradingAccount) -> TradingAccount`：持久化账户。
- `save_binding(binding: AccountBinding) -> AccountBinding`：持久化绑定关系。
- `get_binding(user_id: str, account_id: str) -> AccountBinding | None`：查询绑定。
- `get_credential_ref(credential_ref: str) -> CredentialReference | None`：查询凭据引用。
- `save_capability_projection(projection: AccountCapabilityProjection) -> None`：写能力投影。
- `save_default_session(user_id: str, account_id: str, trading_session_id: str | None) -> None`：写默认会话引用。

## 实现顺序
1. 账户主数据和绑定关系分表实现。
2. 默认会话引用更新必须有审计字段和变更时间。

## 集成检查
- 对 `user_id + account_id` 建唯一索引。
