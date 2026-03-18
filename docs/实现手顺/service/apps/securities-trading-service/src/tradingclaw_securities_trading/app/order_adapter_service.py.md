# securities-trading-service/app/order_adapter_service.py 实现手顺

- 目标代码文件：`apps/securities-trading-service/src/tradingclaw_securities_trading/app/order_adapter_service.py`
- 职责：实现券商下单、撤单、原始查询和回报同步。
- 依赖：`证券交易详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `place_order(command: ChannelOrderCommand) -> Result[ChannelOrderAccepted]`：发券商委托。
- `cancel_order(channel_order_id: str, account_id: str) -> Result[ChannelOrderAccepted]`：撤券商委托。
- `query_assets(account_id: str) -> Result[AssetSummary]`：查资产。
- `query_positions(account_id: str) -> Result[list[PositionSnapshot]]`：查持仓。
- `sync_reports(account_id: str) -> Result[list[ExecutionReport]]`：轮询或接收回报并标准化。

## 实现顺序
1. 先实现下单和撤单，再补资产/持仓查询和回报同步。
2. 所有原始报文都要归档到对象存储或归档表。

## 集成检查
- 发布 `execution.report_received` 事件供 trade-gateway 消费。
