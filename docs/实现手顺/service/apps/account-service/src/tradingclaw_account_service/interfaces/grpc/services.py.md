# account-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/account-service/src/tradingclaw_account_service/interfaces/grpc/services.py`
- 职责：实现账户域 gRPC 服务。
- 依赖：`用户与账户详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `AssertOwnership(request, context) -> AssertOwnershipResponse`：账户归属断言。
- `SetDefaultSession(request, context) -> SetDefaultSessionResponse`：设置默认交易会话。
- `ClearDefaultSession(request, context) -> ClearDefaultSessionResponse`：清理默认会话。
- `RefreshCapability(request, context) -> RefreshCapabilityResponse`：刷新能力投影。

## 实现顺序
1. gRPC 响应要区分归属失败、能力缺失和默认会话无效。

## 集成检查
- trade-gateway 和适配服务内部流程依赖本接口。
