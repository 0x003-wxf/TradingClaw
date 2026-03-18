# ai-orchestration-service/infra/clients/litellm_client.py 实现手顺

- 目标代码文件：`apps/ai-orchestration-service/src/tradingclaw_ai_orchestration/infra/clients/litellm_client.py`
- 职责：封装外部 LiteLLM 访问。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 类 `LiteLLMClient(base_url: str, api_key_ref: str, timeout_seconds: int)`：LiteLLM HTTP client。

## 函数
- `chat(model: str, messages: list[dict], tools: list[dict] | None = None) -> dict`：调用聊天接口。
- `healthcheck() -> bool`：检查路由服务可用性。

## 实现顺序
1. 把模型路由、超时、重试和错误映射集中在 client。

## 集成检查
- 业务域不得直接对接具体模型供应商 API。
