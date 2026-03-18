# platform-bootstrap/registry.py 实现手顺

- 目标代码文件：`packages/platform-bootstrap/src/tradingclaw_platform_bootstrap/registry.py`
- 职责：维护服务角色枚举与装配函数注册表。
- 依赖：`运行启动与环境编排详细设计.md`

## 类
- 类 `RoleDefinition(service: str, role: str, builder: Callable[[BootstrapContext], object], healthcheck: Callable | None = None)`：单个角色定义。
- 类 `RoleRegistry()`：服务角色注册表。

## 函数
- `register(definition: RoleDefinition) -> None`：注册角色。
- `get(service: str, role: str) -> RoleDefinition`：按服务与角色获取装配定义。
- `list_roles(service: str | None = None) -> list[RoleDefinition]`：列出角色。
- `validate(service: str, role: str) -> None`：校验角色是否合法。

## 实现顺序
1. 先实现内存注册表，再补充自动发现或显式注册。
2. 注册表只管理装配函数，不管理运行态对象。

## 集成检查
- 各服务 bootstrap 模块在 import 时向注册表注册角色。
