# platform-observability/tracing.py 实现手顺

- 目标代码文件：`packages/platform-observability/src/tradingclaw_platform_observability/tracing.py`
- 职责：统一 OpenTelemetry trace 初始化和跨协议上下文透传。
- 依赖：`运行启动与环境编排详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `configure_tracing(service: str, endpoint: str | None) -> None`：初始化 tracer provider。
- `extract_trace_context(headers: dict[str, str]) -> dict`：从 HTTP 或消息头提取 trace 上下文。
- `inject_trace_context(carrier: dict[str, str]) -> dict[str, str]`：向 gRPC metadata 或事件头注入 trace 信息。

## 实现顺序
1. 保证 HTTP、gRPC、Kafka、Temporal 都能复用相同 trace 传播规则。
2. 初始化失败不应阻断服务主流程，但必须输出降级日志。

## 集成检查
- 所有外部 client 和消息 producer 都要调用 `inject_trace_context`。
