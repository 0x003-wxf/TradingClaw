# strategy-runtime-service/interfaces/workers/temporal_worker.py 实现手顺

- 目标代码文件：`apps/strategy-runtime-service/src/tradingclaw_strategy_runtime/interfaces/workers/temporal_worker.py`
- 职责：注册策略运行时 workflow 和 activities。
- 依赖：`运行启动与环境编排详细设计.md`、`策略系统详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `build_temporal_worker(context: BootstrapContext) -> Worker`：构建 Temporal worker。
- `register_workflows(worker: Worker) -> None`：注册工作流。
- `register_activities(worker: Worker) -> None`：注册 activity。

## 实现顺序
1. 把 worker 构建与业务 activity 分离。
2. Readiness 成功条件包含 workflow 注册成功。

## 集成检查
- worker 角色不能顺带暴露 HTTP 接口。
