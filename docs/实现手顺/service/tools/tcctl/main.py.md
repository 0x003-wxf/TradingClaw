# tools/tcctl/main.py 实现手顺

- 目标代码文件：`tools/tcctl/main.py`
- 职责：提供仓库级命令行入口，负责配置渲染、环境编排、服务启动和健康检查命令分发。
- 依赖：`运行启动与环境编排详细设计.md`、`工程目录与文件树详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `main(argv: list[str] | None = None) -> int`：CLI 入口。
- `build_parser() -> argparse.ArgumentParser`：构建子命令解析器。
- `dispatch(args: argparse.Namespace) -> int`：把命令分派给 render、env、stack 子模块。

## 实现顺序
1. 先搭命令骨架，命令名至少覆盖 `config render`、`env up`、`service start`、`stack up`、`health check`。
2. 主入口只做参数解析和分发。

## 集成检查
- `tcctl` 是本地和 CI 统一入口，不直接操作生产集群资源。
