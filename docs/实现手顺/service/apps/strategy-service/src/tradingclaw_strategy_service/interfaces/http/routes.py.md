# strategy-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/strategy-service/src/tradingclaw_strategy_service/interfaces/http/routes.py`
- 职责：暴露策略生命周期管理 HTTP 接口。
- 依赖：`策略系统详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `create_strategy(...) -> Envelope`：创建策略。
- `list_strategies(...) -> Envelope`：策略列表。
- `get_strategy(...) -> Envelope`：策略详情。
- `update_strategy(...) -> Envelope`：更新策略。
- `stop_strategy(...) -> Envelope`：停止策略。

## 实现顺序
1. 请求体字段名与 Schema 模型保持一致。
2. 状态和运行时状态同时返回。

## 集成检查
- 所有写接口都要携带幂等键或操作说明。
