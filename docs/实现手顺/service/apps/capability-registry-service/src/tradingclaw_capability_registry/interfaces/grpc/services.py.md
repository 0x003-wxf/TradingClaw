# capability-registry-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/capability-registry-service/src/tradingclaw_capability_registry/interfaces/grpc/services.py`
- 职责：实现能力治理 gRPC 服务。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `ListCapabilities(request, context) -> ListCapabilitiesResponse`：查询能力列表。
- `GetCapability(request, context) -> GetCapabilityResponse`：查询单能力。

## 实现顺序
1. gRPC 接口应足够轻量，适合网关和 AI 频繁读取。

## 集成检查
- 能力治理服务通常作为平台基础服务优先启动。
