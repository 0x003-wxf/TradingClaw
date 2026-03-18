# crypto-trading-service/interfaces/grpc/services.py 实现手顺

- 目标代码文件：`apps/crypto-trading-service/src/tradingclaw_crypto_trading/interfaces/grpc/services.py`
- 职责：实现数字资产适配域 gRPC 服务。
- 依赖：`数字资产交易详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `ValidateCredential(request, context) -> ValidateCredentialResponse`：凭据校验。
- `PlaceOrder(request, context) -> PlaceOrderResponse`：创建订单。
- `AmendOrder(request, context) -> AmendOrderResponse`：改单。
- `CancelOrder(request, context) -> CancelOrderResponse`：撤单。
- `QueryBalances(request, context) -> QueryBalancesResponse`：查询余额/持仓。

## 实现顺序
1. RPC 接口保持统一字段口径，避免把交易所私有字段上抬。

## 集成检查
- trade-gateway 是主要调用方。
