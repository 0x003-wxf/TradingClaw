# strategy-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/strategy-service/src/tradingclaw_strategy_service/interfaces/grpc/services.py`
- 职责：实现策略管理 gRPC 服务。
- 依赖：`策略系统详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `GetStrategy(request, context) -> GetStrategyResponse`：查询策略详情。
- `ListStrategies(request, context) -> ListStrategiesResponse`：分页列出策略。
- `CreateStrategy(request, context) -> CreateStrategyResponse`：创建策略。
- `UpdateStrategy(request, context) -> UpdateStrategyResponse`：更新策略。

## 实现顺序
1. 对外暴露统一策略字段，不泄漏内部快照细节。

## 集成检查
- strategy-runtime 可通过 gRPC 读取实例和配置快照。
