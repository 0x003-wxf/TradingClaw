# platform-bootstrap/runner.py 实现手顺

- 目标代码文件：`packages/platform-bootstrap/src/tradingclaw_platform_bootstrap/runner.py`
- 职责：实现统一引导入口，串起配置装载、密钥解析、日志追踪初始化、依赖预检、角色装配和优雅停机。
- 依赖：`运行启动与环境编排详细设计.md`、`配置与密钥管理详细设计.md`

## 类
- 类 `BootstrapContext(service: str, role: str, env: str, region: str, config: dict, started_at: datetime)`：统一启动上下文。
- 类 `BootstrapRunner(role_registry: RoleRegistry)`：协调启动生命周期。

## 辅助对象
- 类 `BootstrapPhaseResult(phase: str, success: bool, detail: str, elapsed_ms: int)`：记录单个启动阶段结果。
- 类 `DependencyPreflightItem(name: str, required: bool, timeout_seconds: int, checker: Callable[[BootstrapContext], None])`：单个依赖预检定义。
- 类 `BootstrapHooks(before_start: list[Callable], after_start: list[Callable], before_stop: list[Callable], after_stop: list[Callable])`：角色装配前后钩子集合。

## 函数
- `run(service: str, role: str, argv: list[str] | None = None) -> int`：统一 CLI 入口。
- `build_context(service: str, role: str) -> BootstrapContext`：读取引导环境变量并生成上下文。
- `preflight(context: BootstrapContext) -> None`：执行数据库、Redis、Kafka、Temporal 等依赖检查。
- `serve(context: BootstrapContext) -> None`：调用角色注册器启动对应角色。
- `shutdown(context: BootstrapContext, signal_name: str) -> None`：触发优雅停机与资源释放。

## 细化函数
- `load_settings(context: BootstrapContext) -> BootstrapContext`：参数为启动上下文；返回填充好 `settings` 与 `config` 的新上下文；职责是读取渲染配置、解析密钥、完成 schema 校验。
- `init_observability(context: BootstrapContext) -> None`：参数为上下文；无返回值；职责是初始化结构化日志、trace、metrics，并输出启动摘要。
- `build_preflight_plan(context: BootstrapContext) -> list[DependencyPreflightItem]`：参数为上下文；返回当前角色所需依赖清单；职责是按角色裁剪依赖检查范围。
- `run_phase(context: BootstrapContext, phase: str, handler: Callable[[BootstrapContext], None]) -> BootstrapPhaseResult`：参数为上下文、阶段名、处理函数；返回阶段执行结果；职责是统一记录耗时、异常和阶段状态。
- `wire_role(context: BootstrapContext) -> object`：参数为上下文；返回角色运行对象；职责是从 `RoleRegistry` 获取 builder 并完成装配。
- `wait_readiness(context: BootstrapContext, runtime: object, timeout_seconds: int) -> None`：参数为上下文、运行对象、超时秒数；无返回值；职责是等待 Kafka consumer group、gRPC bind、workflow 注册等 readiness 条件满足。
- `install_signal_handlers(context: BootstrapContext, runtime: object) -> None`：参数为上下文和运行对象；无返回值；职责是注册 `SIGTERM`、`SIGINT` 优雅停机逻辑。
- `update_health_state(context: BootstrapContext, phase: str, success: bool, detail: str | None = None) -> None`：参数为上下文、阶段名、结果；无返回值；职责是同步 startup/readiness/dependency 状态。
- `emit_startup_log(context: BootstrapContext, result: BootstrapPhaseResult) -> None`：参数为上下文和阶段结果；无返回值；职责是输出统一结构化启动日志。

## 协作接口
- `RoleRegistry.get(service: str, role: str) -> RoleDefinition`：读取角色装配定义。
- `loader.load_rendered_config(config_path: str) -> dict`：装载渲染后的 `application.yaml`。
- `secret_resolver.resolve_payload(payload: dict, secrets_path: str) -> dict`：解析密钥引用。
- `validator.validate_settings(settings: ServiceSettings) -> None`：执行启动期配置校验。
- `health.set_startup_ready(state: HealthState) -> None`：更新启动探针状态。

## 实现顺序
1. 按 `BOOTSTRAP_INIT -> CONFIG_LOAD -> SECRET_RESOLVE -> OBSERVABILITY_INIT -> SCHEMA_VALIDATE -> DEPENDENCY_PREFLIGHT -> APP_WIRE -> READINESS_WAIT -> SERVING` 的顺序落地。
2. `run` 中只编排流程，不写业务逻辑。
3. 每个阶段都要通过 `run_phase` 执行，禁止手写散落的 try/except 和耗时日志。
4. 依赖预检要区分必要依赖和可选依赖；必要依赖失败直接中止，非必要依赖失败只标记降级。
5. `serve` 只负责切换到运行态，不隐式执行数据库迁移、建 topic、修复历史数据。

## 集成检查
- 所有服务的启动入口最终都要落到本文件。
- 启动失败时日志里至少能看到失败阶段、依赖名、原因码、耗时和退出码。
- readiness 必须晚于 role 装配成功和必要依赖可用，不能仅以端口监听成功作为完成条件。
