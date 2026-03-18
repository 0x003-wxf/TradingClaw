# platform-core/result.py 实现手顺

- 目标代码文件：`packages/platform-core/src/tradingclaw_platform_core/result.py`
- 职责：定义命令、查询、外部依赖调用统一返回模型。
- 依赖：`API字段字典.md`、`平台响应规范`

## 类
- 类 `Result[T](success: bool, data: T | None, error: ErrorDetail | None, meta: dict | None)`：统一返回包装。
- 类 `PageResult[T](items: list[T], page: int, page_size: int, total: int)`：分页查询结果。

## 函数
- `ok(data: T, meta: dict | None = None) -> Result[T]`：构造成功结果。
- `fail(error: ErrorDetail, meta: dict | None = None) -> Result[None]`：构造失败结果。
- `unwrap(result: Result[T]) -> T`：成功时返回数据，失败时抛异常。
- `to_pagination(page_result: PageResult[T]) -> dict`：转成 HTTP 分页元信息。

## 实现顺序
1. 先实现泛型结果对象，再补充分页结果。
2. `success=true` 时 `error=None`，失败时 `data=None`。

## 集成检查
- 应用服务、仓储、外部 client 都可返回 `Result`。
