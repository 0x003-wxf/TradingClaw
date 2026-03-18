# tools/tcctl/render.py 实现手顺

- 目标代码文件：`tools/tcctl/render.py`
- 职责：实现配置渲染与输出产物校验。
- 依赖：`配置与密钥管理详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `render_config(env: str, region: str, service: str, role: str) -> Path`：渲染单个可部署单元配置。
- `load_source_layers(env: str, region: str, service: str, role: str) -> list[dict]`：读取所有配置层。
- `write_rendered_config(target: Path, payload: dict) -> None`：写入渲染产物。
- `verify_rendered_config(target: Path) -> None`：校验必需元字段和 schema。

## 实现顺序
1. 严格按 `shared -> env -> region -> service -> role -> runtime override -> env var -> CLI` 顺序实现。
2. 渲染产物必须带 `config_version`、`rendered_at`、`source_commit`。

## 集成检查
- 输出路径建议位于 `ops/config/runtime/rendered/`。
