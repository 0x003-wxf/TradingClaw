# api-gateway/settings.py 实现手顺

- 目标代码文件：`apps/api-gateway/src/tradingclaw_api_gateway/settings.py`
- 职责：定义本服务专属配置模型、默认值和跨字段校验。
- 依赖：`配置与密钥管理详细设计.md`、`工程配置字段字典.md`、对应模块详细设计

## 类
- 类 `ApiGatewaySettings(ServiceSettings)`：本服务配置模型。

## 函数
- `model_post_init(self, __context: object) -> None`：执行服务级跨字段校验。
- `required_dependencies(self, role: str) -> list[str]`：返回当前角色的必要依赖清单。
- `safe_summary(self) -> dict`：返回脱敏后的配置摘要。

## 实现顺序
1. 先建顶层命名空间字段，再补本服务专属配置段。
2. 把角色差异、端口和外部依赖约束放入校验逻辑。
3. 输出给日志的视图必须脱敏。

## 集成检查
- 本文件由 bootstrap 在启动阶段加载，不应包含业务逻辑。
