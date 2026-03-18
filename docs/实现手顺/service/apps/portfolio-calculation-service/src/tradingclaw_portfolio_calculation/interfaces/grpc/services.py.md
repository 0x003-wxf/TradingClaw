# portfolio-calculation-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/portfolio-calculation-service/src/tradingclaw_portfolio_calculation/interfaces/grpc/services.py`
- 职责：实现纯计算服务 gRPC 接口。
- 依赖：`策略系统详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `ValidateConfig(request, context) -> ValidateConfigResponse`：校验配置。
- `Evaluate(request, context) -> EvaluateResponse`：计算策略信号。
- `GeneratePlan(request, context) -> GeneratePlanResponse`：生成交易计划。

## 实现顺序
1. gRPC 返回值要保留结构化原因，尤其是不可计算场景。

## 集成检查
- 该服务应保持无副作用、易扩缩容。
