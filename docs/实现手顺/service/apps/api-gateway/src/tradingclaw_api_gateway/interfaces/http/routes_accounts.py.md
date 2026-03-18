# api-gateway/interfaces/http/routes_accounts.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/routes_accounts.py`
- 职责：代理账户绑定、解绑、列表和默认会话设置。
- 依赖：`网关与平台基础详细设计.md`、`用户与账户详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `bind_account(body: BindAccountRequest, ctx: RequestContext, token: str, idempotency_key: str) -> Envelope`：代理绑定账户。
- `unbind_account(account_id: str, ctx: RequestContext, token: str) -> Envelope`：代理解绑。
- `list_accounts(ctx: RequestContext, token: str) -> Envelope`：代理账户列表。
- `set_default_session(account_id: str, body: SetDefaultSessionRequest, ctx: RequestContext, token: str) -> Envelope`：代理默认会话设置。

## 实现顺序
1. 写接口统一要求幂等键和登录态。
2. 网关不判断交易会话是否存在，由 account/trade 服务协同判断。

## 集成检查
- 默认会话相关接口要返回清晰失败原因。
