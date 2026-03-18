# crypto-trading-service/app/order_adapter_service.py 实现手顺

- 目标代码文件：`apps/crypto-trading-service/src/tradingclaw_crypto_trading/app/order_adapter_service.py`
- 职责：实现数字资产普通订单、余额持仓查询和资金费率读取。
- 依赖：`数字资产交易详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `place_order(command: ChannelOrderCommand) -> Result[ChannelOrderAccepted]`：创建订单。
- `amend_order(command: AmendOrderCommand) -> Result[ChannelOrderAccepted]`：改单。
- `cancel_order(channel_order_id: str, account_id: str) -> Result[ChannelOrderAccepted]`：撤单。
- `query_balances(account_id: str) -> Result[list[BalanceSnapshot]]`：查询余额。
- `query_positions(account_id: str) -> Result[list[PositionSnapshot]]`：查询持仓。
- `query_funding_rate(symbol: str) -> Result[FundingRateSnapshot]`：查询资金费率。

## 实现顺序
1. 先实现普通订单，再补资金费率和扩展查询。
2. 返回体区分原始状态和适配标准化结果。

## 集成检查
- 本服务不维护完整网格策略状态机。
