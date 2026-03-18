# account-service/app/services.py 实现手顺

- 目标代码文件：`apps/account-service/src/tradingclaw_account_service/app/services.py`
- 职责：实现账户绑定、解绑、归属断言、默认交易会话引用写入和能力投影刷新。
- 依赖：`用户与账户详细设计.md`、`交易网关详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `bind_account(user_id: str, credential_ref: str, provider_code: str, account_type: str, idempotency_key: str) -> Result[AccountBindingView]`：绑定账户。
- `unbind_account(user_id: str, account_id: str) -> Result[dict]`：解绑账户。
- `assert_account_ownership(user_id: str, account_id: str, include_capability: bool = False) -> Result[OwnershipAssertion]`：归属断言。
- `set_default_session(user_id: str, account_id: str, trading_session_id: str) -> Result[dict]`：写默认交易会话。
- `clear_default_session(user_id: str, account_id: str) -> Result[dict]`：清默认会话。
- `refresh_account_capability(account_id: str, capabilities: dict, status: str) -> Result[dict]`：刷新能力投影。

## 实现顺序
1. 先完成绑定和归属断言，再补默认会话引用写入。
2. 写默认会话前通过 trade-gateway client 校验目标会话 `AVAILABLE`。

## 集成检查
- 本服务对外回答“是否归属当前用户”，不回答“是否已经可交易”。
