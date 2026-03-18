# tools/tcctl/stack.py 实现手顺

- 目标代码文件：`tools/tcctl/stack.py`
- 职责：实现本地或 CI 的 profile 级启动编排。
- 依赖：`运行启动与环境编排详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `stack_up(profile: str) -> int`：按 profile 启动服务与依赖。
- `stack_down(profile: str) -> int`：停止 profile。
- `health_check(profile: str) -> int`：执行 profile 健康检查。
- `load_profile(profile: str) -> dict`：读取 `ops/runtime/profiles` 描述。

## 实现顺序
1. 先实现 profile 解析，再按依赖顺序启动服务。
2. 健康检查要复用平台探针定义。

## 集成检查
- 本地联调先做 `core-trading` 和 `full-parity` 两个 profile。
