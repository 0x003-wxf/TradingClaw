# capability-registry-service/domain/models.py 实现手顺

- 目标代码文件：`apps/capability-registry-service/src/tradingclaw_capability_registry/domain/models.py`
- 职责：定义能力定义和能力版本矩阵。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 类 `CapabilityDefinition(capability_id: str, name: str, owner: str, description: str)`：能力定义。
- 类 `CapabilityVersion(capability_id: str, open_version: str, enabled: bool, capability_status: str, degrade_strategy: str | None, client_matrix: dict)`：能力版本与可见性矩阵。

## 函数
- `enable(self) -> None`：启用能力版本。
- `disable(self) -> None`：禁用能力版本。
- `assert_client_visible(self, client_type: str, client_version: str) -> None`：校验客户端可见性。

## 实现顺序
1. 平台开放状态统一使用 `capability_status`。

## 集成检查
- 能力注册中心不维护业务主状态。
