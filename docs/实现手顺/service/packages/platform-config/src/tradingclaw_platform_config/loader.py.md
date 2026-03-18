# platform-config/loader.py 实现手顺

- 目标代码文件：`packages/platform-config/src/tradingclaw_platform_config/loader.py`
- 职责：实现渲染后配置文件读取、层次覆盖与最终运行快照构造。
- 依赖：`配置与密钥管理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `load_yaml(path: str) -> dict`：读取 YAML 文件。
- `merge_layers(base: dict, override: dict) -> dict`：按声明式规则合并配置层。
- `load_rendered_config(config_path: str) -> dict`：加载最终 `application.yaml`。
- `build_settings(model_type: type[ServiceSettings], payload: dict) -> ServiceSettings`：将配置字典转成类型化 Settings。

## 实现顺序
1. 明确后加载覆盖前加载，但不允许跨类型覆盖。
2. 对配置来源保留 `source_commit`、`rendered_at` 等元信息。

## 集成检查
- runner 在 `CONFIG_LOAD` 阶段调用本文件。
