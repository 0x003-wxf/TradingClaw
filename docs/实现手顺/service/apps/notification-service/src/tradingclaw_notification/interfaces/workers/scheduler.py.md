# notification-service/interfaces/workers/scheduler.py 实现手顺

- 目标代码文件：`apps/notification-service/src/tradingclaw_notification/interfaces/workers/scheduler.py`
- 职责：实现失败通知重试和定时补投递任务。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `run_retry_scheduler() -> None`：周期扫描失败通知并重试。
- `run_dead_letter_recovery() -> None`：处理死信恢复。

## 实现顺序
1. 调度器只发起重试，不直接修改已成功通知记录。

## 集成检查
- scheduler 角色应独立部署。
