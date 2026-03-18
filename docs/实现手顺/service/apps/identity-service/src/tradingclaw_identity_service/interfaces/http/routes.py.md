# identity-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/identity-service/src/tradingclaw_identity_service/interfaces/http/routes.py`
- 职责：暴露注册、登录、登出、会话校验和用户资料 HTTP 接口。
- 依赖：`用户与账户详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `register(body: RegisterRequest) -> Envelope`：用户注册。
- `login(body: LoginRequest) -> Envelope`：用户登录。
- `logout(token: str) -> Envelope`：登出。
- `validate(token: str) -> Envelope`：会话校验。
- `get_profile(user_id: str) -> Envelope`：查询用户资料。

## 实现顺序
1. 参数模型使用 Pydantic；响应统一走平台信封。
2. 登录接口返回 `access_token`、`expires_at`、`user_summary`。

## 集成检查
- `get_profile` 要依赖身份断言。
