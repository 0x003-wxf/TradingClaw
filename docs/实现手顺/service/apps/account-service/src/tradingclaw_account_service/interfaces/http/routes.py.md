# account-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/account-service/src/tradingclaw_account_service/interfaces/http/routes.py`
- 职责：暴露账户绑定、解绑、归属查询和默认会话设置接口。
- 依赖：`用户与账户详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `bind_account(body: BindAccountRequest, user_id: str, idempotency_key: str) -> Envelope`：绑定账户。
- `unbind_account(account_id: str, user_id: str) -> Envelope`：解绑。
- `get_accounts(user_id: str) -> Envelope`：列出用户账户摘要。
- `set_default_session(account_id: str, body: SetDefaultSessionRequest, user_id: str) -> Envelope`：设置默认会话。

## 实现顺序
1. 接口层不接收真实 secret，只接 `credential_ref`。
2. 写接口统一要求登录态；绑定类接口同时要求幂等键。

## 集成检查
- 前端和 CLI 都通过网关访问这些接口。
