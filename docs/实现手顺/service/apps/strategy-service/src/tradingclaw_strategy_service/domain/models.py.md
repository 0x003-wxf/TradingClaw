# strategy-service/domain/models.py 实现手顺

- 目标代码文件：`apps/strategy-service/src/tradingclaw_strategy_service/domain/models.py`
- 职责：定义策略模板、策略实例、配置快照和执行记录元数据。
- 依赖：`策略系统详细设计.md`

## 类
- 类 `StrategyDefinition(strategy_definition_id: str, strategy_type: str, schema_version: str, status: str)`：策略模板定义。
- 类 `StrategyInstance(strategy_instance_id: str, user_id: str, account_id: str, strategy_type: str, status: str, runtime_status: str)`：策略实例。
- 类 `StrategyConfig(strategy_instance_id: str, version: int, config: dict, created_at: datetime)`：配置快照。

## 函数
- `start(self) -> None`：策略进入待启动或运行中。
- `stop(self, operator_reason: str | None = None) -> None`：停止策略。
- `archive(self) -> None`：归档策略。
- `create_next_version(self, config: dict) -> StrategyConfig`：创建下一版配置快照。

## 实现顺序
1. 实例状态和运行时状态分字段管理。
2. 配置版本不可变，修改配置要生成新版本。

## 集成检查
- 运行时执行上下文不放本服务。
