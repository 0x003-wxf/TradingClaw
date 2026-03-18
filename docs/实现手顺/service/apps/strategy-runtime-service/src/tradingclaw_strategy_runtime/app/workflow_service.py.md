# strategy-runtime-service/app/workflow_service.py 实现手顺

- 目标代码文件：`apps/strategy-runtime-service/src/tradingclaw_strategy_runtime/app/workflow_service.py`
- 职责：封装 Temporal workflow 和 activity 调用。
- 依赖：`策略系统详细设计.md`、`运行启动与环境编排详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `start_strategy_workflow(strategy_instance_id: str, trigger_source: str) -> str`：启动策略工作流。
- `signal_review_completed(workflow_id: str, risk_event_id: str, decision: str, resume_token: str | None) -> None`：复核完成后发 signal。
- `signal_order_report(workflow_id: str, order_id: str, status: str) -> None`：把订单状态变化通知工作流。

## 实现顺序
1. 工作流层负责长流程和恢复，不持有最终事实主权。

## 集成检查
- Temporal worker 角色最终注册本文件导出的 workflow/activity。
