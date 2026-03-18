# identity-service/infra/db/repositories.py 实现手顺

- 目标代码文件：`apps/identity-service/src/tradingclaw_identity_service/infra/db/repositories.py`
- 职责：实现用户、会话、额度和积分相关仓储。
- 依赖：`用户与账户详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `save_user(user: User) -> User`：持久化用户。
- `get_user_by_principal(principal: str) -> User | None`：按登录主体查询用户。
- `save_session(session: Session) -> Session`：写入会话。
- `get_session_by_token(access_token: str) -> Session | None`：按 token 查询会话。
- `revoke_session(session_id: str) -> None`：更新会话状态。
- `load_profile_view(user_id: str) -> UserProfileView`：聚合只读资料。

## 实现顺序
1. 写操作和聚合查询分开实现。
2. 会话查询优先按 token 建唯一索引。

## 集成检查
- 仓储接口返回领域模型或专用查询模型。
