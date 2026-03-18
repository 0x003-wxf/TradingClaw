# ocr-service/app/ocr_service.py 实现手顺

- 目标代码文件：`apps/ocr-service/src/tradingclaw_ocr_service/app/ocr_service.py`
- 职责：实现 OCR 任务创建、识别执行和结果查询。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `create_job(user_id: str, image_ref: str, scene: str, idempotency_key: str) -> Result[OCRJob]`：创建 OCR 任务。
- `run_job(job_id: str) -> Result[OCRJobResult]`：执行 OCR 识别。
- `get_job(job_id: str, user_id: str) -> Result[OCRJobResult]`：查询任务结果。

## 实现顺序
1. MVP 阶段统一经 LiteLLM 路由到外部视觉模型。
2. 任务完成后发布 `ocr.job_finished`。

## 集成检查
- 失败结果要返回标准错误码和可读错误信息。
