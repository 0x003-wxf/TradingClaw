# capability-registry-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/capability-registry-service/src/tradingclaw_capability_registry/interfaces/http/routes.py`
- 职责：暴露能力查询和管理接口。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `list_capabilities(...) -> Envelope`：列出能力矩阵。
- `get_capability(...) -> Envelope`：查询单能力。
- `upsert_capability(...) -> Envelope`：更新能力版本。

## 实现顺序
1. 管理接口建议与只读查询接口分权限控制。

## 集成检查
- 开放状态和降级策略都必须显式返回。
