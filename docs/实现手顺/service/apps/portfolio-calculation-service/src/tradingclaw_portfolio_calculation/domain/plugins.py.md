# portfolio-calculation-service/domain/plugins.py 实现手顺

- 目标代码文件：`apps/portfolio-calculation-service/src/tradingclaw_portfolio_calculation/domain/plugins.py`
- 职责：定义纯计算策略插件接口和首批策略插件。
- 依赖：`策略系统详细设计.md`

## 类
- 类 `StrategyPlugin`：抽象接口，定义 `validate`、`prepare`、`evaluate`、`generate_plan`。
- 类 `GridPlugin`：实现网格策略计算。
- 类 `MartingalePlugin`：实现马丁策略计算。
- 类 `RebalancePlugin`：实现动态平衡策略计算。
- 类 `DcaPlugin`：实现定投策略计算。

## 函数
- `validate(config: dict, context: dict) -> dict`：校验配置并返回标准化配置。
- `prepare(snapshot: dict) -> dict`：从快照生成计算上下文。
- `evaluate(market: dict, account: dict, positions: dict, config: dict) -> dict`：产出信号。
- `generate_plan(signal: dict) -> dict`：产出交易计划。

## 实现顺序
1. 所有插件必须保持纯计算，不访问数据库、消息总线或交易通道。
2. 每个插件都要同时输出 JSON Schema 和 Pydantic Model。

## 集成检查
- 插件输出必须稳定可重放。
