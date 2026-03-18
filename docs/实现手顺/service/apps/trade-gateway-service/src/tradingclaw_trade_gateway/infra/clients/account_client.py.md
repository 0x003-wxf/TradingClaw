# trade-gateway-service/infra/clients/account_client.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/infra/clients/account_client.py`
- 职责：封装账户服务 gRPC client。
- 依赖：`用户与账户详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `assert_account_ownership(user_id: str, account_id: str, include_capability: bool = True) -> OwnershipAssertion`：查询归属和能力摘要。
- `set_default_session(user_id: str, account_id: str, trading_session_id: str) -> None`：写默认会话引用。
- `clear_default_session(user_id: str, account_id: str) -> None`：清默认会话引用。

## 实现顺序
1. client 层只关心协议转换、超时、重试和错误映射。

## 集成检查
- trade-gateway 不要直接访问 account-service 数据库。
