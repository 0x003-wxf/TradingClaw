# platform-core/exceptions.py 实现手顺

- 目标代码文件：`packages/platform-core/src/tradingclaw_platform_core/exceptions.py`
- 职责：定义全仓统一基础异常、领域异常和错误码承载对象，供 HTTP、gRPC、事件消费者统一映射。
- 依赖：`错误码字典.md`、`API字段字典.md`

## 类
- 类 `ErrorDetail(code: str, message: str, reason_code: str | None, extra: dict | None)`：统一错误详情对象。
- 类 `TradingClawError(detail: ErrorDetail, status_code: int = 500)`：全局异常基类。
- 类 `ValidationError(detail: ErrorDetail)`：参数或 Schema 校验异常。
- 类 `DomainConflictError(detail: ErrorDetail)`：状态冲突、幂等冲突异常。
- 类 `DependencyUnavailableError(detail: ErrorDetail)`：依赖不可用异常。

## 函数
- `build_error(code: str, message: str, reason_code: str | None = None, extra: dict | None = None) -> ErrorDetail`：构造标准错误详情。
- `raise_not_found(resource_type: str, resource_id: str) -> None`：封装资源不存在异常抛出逻辑。
- `raise_state_conflict(resource_type: str, current_status: str, expected: list[str]) -> None`：封装状态冲突异常。
- `map_exception(exc: Exception) -> TradingClawError`：把未知异常归一为平台异常。

## 实现顺序
1. 先定义 `ErrorDetail`，保证后续服务内异常都使用同一数据结构。
2. 异常类型不要耦合 FastAPI 或 gRPC，协议层单独映射。
3. 补单元测试覆盖错误码、状态码和 message 透传逻辑。

## 集成检查
- HTTP 异常处理器和 gRPC interceptor 必须依赖本文件。
