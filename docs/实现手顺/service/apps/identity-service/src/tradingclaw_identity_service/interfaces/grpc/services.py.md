# identity-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/identity-service/src/tradingclaw_identity_service/interfaces/grpc/services.py`
- 职责：实现身份域 gRPC 服务。
- 依赖：`用户与账户详细设计.md`、`identity.proto`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `ValidateSession(request, context) -> ValidateSessionResponse`：校验访问令牌。
- `Logout(request, context) -> LogoutResponse`：注销会话。
- `GetUserProfile(request, context) -> GetUserProfileResponse`：返回用户资料摘要。

## 实现顺序
1. RPC 层只做协议转换和错误映射。
2. 响应字段与 proto 契约保持一致。

## 集成检查
- api-gateway 和其他服务内部调用依赖本接口。
