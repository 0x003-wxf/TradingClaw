# ocr-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/ocr-service/src/tradingclaw_ocr_service/interfaces/http/routes.py`
- 职责：暴露 OCR 任务创建和查询接口。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `create_job(...) -> Envelope`：创建 OCR 任务。
- `get_job(...) -> Envelope`：查询 OCR 结果。

## 实现顺序
1. 创建任务接口要求 `Authorization` 和 `X-Idempotency-Key`。

## 集成检查
- 返回字段保持 `job_id`、`status`、`confidence`、`reason_code` 口径。
