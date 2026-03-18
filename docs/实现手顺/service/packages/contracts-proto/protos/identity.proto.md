# contracts-proto/identity.proto 实现手顺

- 目标代码文件：`packages/contracts-proto/protos/identity.proto`
- 职责：定义身份域 gRPC 契约。
- 依赖：`用户与账户详细设计.md`、`API字段字典.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `service IdentityQueryService`：提供 `ValidateSession(ValidateSessionRequest) returns (ValidateSessionResponse)`，参数包含 `access_token`、`required_scope`，返回 `valid`、`user_id`、`session_id`、`auth_session_status`。
- `service IdentityCommandService`：提供 `Logout(LogoutRequest) returns (LogoutResponse)`，参数包含 `session_id` 或 `access_token`，返回 `revoked`。
- `message UserSummary`：承载 `user_id`、`level`、`strategy_quota_total`、`strategy_quota_used`、`points_available`。

## 实现顺序
1. 字段名保持 snake_case，对外与 HTTP 字段同名。
2. 状态字段复用统一枚举。

## 集成检查
- identity-service gRPC 接口和 api-gateway 内部 client 依赖此文件。
