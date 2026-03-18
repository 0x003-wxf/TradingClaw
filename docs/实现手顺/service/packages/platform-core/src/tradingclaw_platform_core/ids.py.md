# platform-core/ids.py 实现手顺

- 目标代码文件：`packages/platform-core/src/tradingclaw_platform_core/ids.py`
- 职责：统一生成 request_id、trace_id、session_id、order_id、strategy_instance_id 等稳定 ID。
- 依赖：`API字段字典.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `new_request_id(prefix: str = "req") -> str`：生成请求 ID。
- `new_trace_id(prefix: str = "trc") -> str`：生成链路追踪 ID。
- `new_domain_id(prefix: str) -> str`：生成领域资源 ID。
- `build_resource_ref(resource_type: str, resource_id: str, account_id: str | None = None) -> str`：构造风控、审计、恢复统一引用。

## 实现顺序
1. 统一前缀规则，避免各服务生成风格不一致。
2. 对 `resource_ref` 固定格式做测试，确保风控和审计检索稳定。

## 集成检查
- 订单、风险事件、通知等跨域主键应通过本文件统一生成。
