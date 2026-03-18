# capability-registry-service/app/capability_service.py 实现手顺

- 目标代码文件：`apps/capability-registry-service/src/tradingclaw_capability_registry/app/capability_service.py`
- 职责：实现能力矩阵查询和开放治理。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `list_capabilities(client_type: str, client_version: str) -> Result[list[CapabilityView]]`：查询客户端可见能力。
- `get_capability(capability_id: str, client_type: str, client_version: str) -> Result[CapabilityView]`：查询单能力。
- `upsert_capability_version(capability_id: str, open_version: str, enabled: bool, capability_status: str, degrade_strategy: str | None, client_matrix: dict) -> Result[CapabilityVersion]`：更新能力版本。

## 实现顺序
1. 能力注册中心必须成为前端、CLI、AI 和网关的统一版本口径来源。

## 集成检查
- 能力查询结果要可被网关直接用于降级提示。
