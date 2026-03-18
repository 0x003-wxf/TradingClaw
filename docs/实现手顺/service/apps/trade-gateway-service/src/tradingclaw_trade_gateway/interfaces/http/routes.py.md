# trade-gateway-service/interfaces/http/routes.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/interfaces/http/routes.py`
- 职责：暴露交易网关自身 HTTP 接口，供网关转发或内部调试。
- 依赖：`交易网关详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `create_session(...) -> Envelope`：创建统一交易会话。
- `submit_auth_step(...) -> Envelope`：提交认证步骤。
- `refresh_session(...) -> Envelope`：刷新会话。
- `place_order(...) -> Envelope`：统一下单。
- `cancel_order(...) -> Envelope`：统一撤单。
- `get_order(...) -> Envelope`：订单详情。

## 实现顺序
1. 接口字段名严格跟 proto 对齐。
2. 只暴露统一语义，不暴露适配域原始接口。

## 集成检查
- 通常由 api-gateway 转发，对外统一入口仍是网关。
