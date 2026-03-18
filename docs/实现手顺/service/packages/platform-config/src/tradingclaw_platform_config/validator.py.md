# platform-config/validator.py 实现手顺

- 目标代码文件：`packages/platform-config/src/tradingclaw_platform_config/validator.py`
- 职责：统一执行 Pydantic、JSON Schema 和跨字段规则校验。
- 依赖：`配置与密钥管理详细设计.md`、`工程配置字段字典.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `validate_settings(settings: ServiceSettings) -> None`：执行模型层校验。
- `validate_schema(schema_path: str, payload: dict) -> None`：执行 JSON Schema 校验。
- `validate_runtime_constraints(service: str, role: str, settings: ServiceSettings) -> None`：执行角色与配置组合约束校验。

## 实现顺序
1. 先走 Pydantic，再走 JSON Schema，最后做跨字段和环境约束。
2. 错误信息要包含字段路径、期望值和当前值。

## 集成检查
- runner 在 `SCHEMA_VALIDATE` 阶段调用本文件。
