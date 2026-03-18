# platform-config/settings_base.py 实现手顺

- 目标代码文件：`packages/platform-config/src/tradingclaw_platform_config/settings_base.py`
- 职责：定义所有服务共享的 Settings 基类和顶层命名空间模型。
- 依赖：`配置与密钥管理详细设计.md`、`工程配置字段字典.md`

## 类
- 类 `BootstrapSettings(env: str, region: str, service_name: str, role: str, config_version: str)`：引导配置。
- 类 `AppSettings(app: dict, http: dict | None, grpc: dict | None, database: dict | None, redis: dict | None, messaging: dict | None, temporal: dict | None, security: dict | None, observability: dict | None)`：平台顶层配置。
- 类 `ServiceSettings(AppSettings)`：服务专属 Settings 基类。

## 函数
- `load_bootstrap_from_env() -> BootstrapSettings`：从 `TC_BOOTSTRAP_*` 环境变量读取引导参数。
- `model_post_init(self, __context: object) -> None`：执行跨字段校验。
- `to_safe_dict(self) -> dict`：返回已脱敏配置视图。

## 实现顺序
1. 先冻结顶层命名空间，再给每个服务继承扩展。
2. 把脱敏、默认值、跨字段校验集中到基类。

## 集成检查
- 后续每个服务 `settings.py` 都应继承这里的基类。
