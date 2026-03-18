# notification-service/app/notification_service.py 实现手顺

- 目标代码文件：`apps/notification-service/src/tradingclaw_notification/app/notification_service.py`
- 职责：实现通知创建、投递、重试和去重。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `send_requested(command: NotificationSendCommand) -> Result[Notification]`：处理 `notification.send_requested`。
- `deliver(notification_id: str) -> Result[DeliveryAttempt]`：执行一次投递。
- `retry_failed(notification_id: str) -> Result[DeliveryAttempt]`：失败重试。

## 实现顺序
1. 先创建通知主记录并发布 `notification.created`，再执行投递。
2. 投递成功后发布 `notification.delivered`。

## 集成检查
- 去重键建议基于 `scene + target + business_resource_ref`。
