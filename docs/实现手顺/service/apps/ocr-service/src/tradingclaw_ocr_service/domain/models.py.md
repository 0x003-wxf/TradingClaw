# ocr-service/domain/models.py 实现手顺

- 目标代码文件：`apps/ocr-service/src/tradingclaw_ocr_service/domain/models.py`
- 职责：定义 OCR 任务模型。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 类 `OCRJob(job_id: str, image_ref: str, scene: str, provider_code: str, status: str, confidence: Decimal | None, reason_code: str | None)`：OCR 任务。

## 函数
- `mark_running(self) -> None`：任务开始执行。
- `mark_finished(self, confidence: Decimal | None) -> None`：任务完成。
- `mark_failed(self, reason_code: str) -> None`：任务失败。

## 实现顺序
1. 验证码图像和识别文本都属于敏感信息，保留期要受控。

## 集成检查
- OCR 事件只表达识别任务完成结果，不表达业务认证主流程状态。
