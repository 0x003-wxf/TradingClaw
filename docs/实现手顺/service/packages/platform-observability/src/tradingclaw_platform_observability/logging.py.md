# platform-observability/logging.py 实现手顺

- 目标代码文件：`packages/platform-observability/src/tradingclaw_platform_observability/logging.py`
- 职责：统一结构化日志初始化、上下文字段注入和敏感字段脱敏。
- 依赖：`运行启动与环境编排详细设计.md`、`配置与密钥管理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `configure_logging(service: str, role: str, level: str, json_mode: bool = True) -> None`：初始化日志框架。
- `bind_request_context(request_id: str, trace_id: str, user_id: str | None = None) -> None`：绑定请求上下文。
- `clear_request_context() -> None`：清理请求上下文。
- `sanitize_payload(payload: dict) -> dict`：对 token、密码、secret 做脱敏。

## 实现顺序
1. 日志字段最少包含 `service`、`role`、`request_id`、`trace_id`、`env`、`region`。
2. 把脱敏做成公共函数。

## 集成检查
- 所有服务入口和中间件都必须调用本文件。
