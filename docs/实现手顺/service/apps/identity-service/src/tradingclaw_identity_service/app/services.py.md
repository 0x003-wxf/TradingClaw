# identity-service/app/services.py 实现手顺

- 目标代码文件：`apps/identity-service/src/tradingclaw_identity_service/app/services.py`
- 职责：实现注册、登录、会话校验、登出和用户资料聚合应用服务。
- 依赖：`用户与账户详细设计.md`、`后端需求文档.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `register_user(principal: str, credential: str, identity_type: str, request_id: str) -> Result[User]`：注册用户。
- `login(principal: str, credential: str, client_type: str, request_id: str) -> Result[LoginResponse]`：登录并签发会话。
- `validate_session(access_token: str, required_scope: list[str] | None = None) -> Result[SessionAssertion]`：校验访问令牌。
- `logout(session_id: str | None = None, access_token: str | None = None) -> Result[dict]`：登出并吊销会话。
- `get_user_profile(user_id: str) -> Result[UserProfileView]`：聚合用户等级、额度、积分和账户摘要。

## 实现顺序
1. 先实现登录与会话校验，再补用户资料聚合。
2. 登录成功后发布 `session.created` 事件。

## 集成检查
- 会话校验返回断言结果，不返回交易上下文。
