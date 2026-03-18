# api-gateway/interfaces/http/routes_ai.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/interfaces/http/routes_ai.py`
- 职责：代理 AI 聊天、模型列表和 OCR 请求入口。
- 依赖：`网关与平台基础详细设计.md`、`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `chat(...) -> Envelope`：代理 AI 问答请求。
- `list_models(...) -> Envelope`：代理模型列表。
- `create_ocr_job(...) -> Envelope`：代理 OCR 任务创建。
- `get_ocr_job(...) -> Envelope`：代理 OCR 任务结果查询。

## 实现顺序
1. AI 和 OCR 接口都必须经过能力注册中心判定。
2. OCR 创建接口要求幂等键。

## 集成检查
- AI 结果可增强展示，但网关不能让 AI 越权执行真实交易写操作。
