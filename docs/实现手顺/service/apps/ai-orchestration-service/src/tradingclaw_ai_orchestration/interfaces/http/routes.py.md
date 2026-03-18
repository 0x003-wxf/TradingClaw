# ai-orchestration-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/ai-orchestration-service/src/tradingclaw_ai_orchestration/interfaces/http/routes.py`
- 职责：暴露 AI 问答和模型列表接口。
- 依赖：`智能能力与治理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `chat(...) -> Envelope`：AI 问答。
- `list_models(...) -> Envelope`：模型列表。

## 实现顺序
1. AI 聊天是同步增强接口，返回 `conversation_id` 作为主键。

## 集成检查
- 未开放能力时返回降级说明。
