# securities-trading-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/securities-trading-service/src/tradingclaw_securities_trading/interfaces/grpc/services.py`
- 职责：实现证券适配域 gRPC 服务。
- 依赖：`证券交易详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `StartAuthFlow(request, context) -> StartAuthFlowResponse`：启动认证流程。
- `SubmitAuthStep(request, context) -> SubmitAuthStepResponse`：提交认证步骤。
- `PlaceOrder(request, context) -> PlaceOrderResponse`：券商下单。
- `CancelOrder(request, context) -> CancelOrderResponse`：券商撤单。
- `QueryAssets(request, context) -> QueryAssetsResponse`：查询资产/持仓。

## 实现顺序
1. RPC 层不暴露券商私有术语给调用方。
2. 错误返回要保留结构化原因码。

## 集成检查
- trade-gateway 只依赖 gRPC，不直连券商 client。
