# trade-gateway-service/infra/clients/adapter_clients.py 实现手顺

- 目标代码文件：`apps/trade-gateway-service/src/tradingclaw_trade_gateway/infra/clients/adapter_clients.py`
- 职责：按 `provider_code + adapter_auth_mode + adapter_session_mode` 路由证券或数字资产适配器。
- 依赖：`交易网关详细设计.md`

## 类
- 类 `AdapterClientRouter()`：适配器路由器。

## 函数
- `create_adapter_session(account_context: dict) -> AdapterSessionResult`：启动适配侧认证或签名校验。
- `submit_adapter_auth_step(auth_flow_id: str, step_code: str, fields: dict) -> AdapterSessionResult`：推进认证步骤。
- `place_channel_order(order: Order, rule_profile: TradingRuleProfile) -> ChannelOrderResult`：向适配域下单。
- `cancel_channel_order(order: Order) -> ChannelOrderResult`：向适配域撤单。

## 实现顺序
1. 先实现路由键解析，再实现证券和数字资产两个下游 client。
2. 路由层返回统一结果对象。

## 集成检查
- 所有真实交易写操作都必须通过这里离开统一交易域。
