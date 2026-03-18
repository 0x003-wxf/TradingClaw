# platform-bootstrap/health.py 实现手顺

- 目标代码文件：`packages/platform-bootstrap/src/tradingclaw_platform_bootstrap/health.py`
- 职责：统一 startup、readiness、liveness、dependency 探针模型。
- 依赖：`运行启动与环境编排详细设计.md`

## 类
- 类 `HealthState(startup: bool, readiness: bool, liveness: bool, dependencies: dict[str, str])`：进程健康状态快照。
- 类 `DependencyCheck(name: str, required: bool, checker: Callable[[], bool])`：单项依赖探针定义。

## 函数
- `set_startup_ready(state: HealthState) -> None`：标记启动完成。
- `set_readiness(state: HealthState, ready: bool) -> None`：切换 readiness。
- `record_dependency(state: HealthState, name: str, status: str) -> None`：记录依赖状态。
- `as_http_payload(state: HealthState) -> dict`：生成健康检查 HTTP 返回体。

## 实现顺序
1. 将健康状态独立为线程安全对象。
2. 保证 `startup`、`readiness`、`liveness` 语义分离。

## 集成检查
- HTTP API、gRPC API、worker 角色都应使用同一健康状态源。
