# notification-service/domain/models.py 实现手顺

- 目标代码文件：`apps/notification-service/src/tradingclaw_notification/domain/models.py`
- 职责：定义通知主记录和投递尝试记录。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 类 `Notification(notification_id: str, channel: str, target: str, template_code: str, status: str, payload: dict)`：通知主记录。
- 类 `DeliveryAttempt(notification_id: str, attempt_no: int, status: str, provider_response: dict | None, reason_code: str | None)`：投递记录。

## 函数
- `mark_created(self) -> None`：通知资源已创建。
- `mark_delivered(self) -> None`：通知投递成功。
- `mark_failed(self, reason_code: str) -> None`：投递失败。

## 实现顺序
1. 通知状态和投递尝试分开建模。
2. 失败重试通过追加 `DeliveryAttempt`，不要覆盖历史。

## 集成检查
- 通知域不反向修改业务状态。
