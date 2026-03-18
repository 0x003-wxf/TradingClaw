# crypto-trading-service/domain/models.py 实现手顺

- 目标代码文件：`apps/crypto-trading-service/src/tradingclaw_crypto_trading/domain/models.py`
- 职责：定义交易所会话、余额快照、持仓快照、原始订单和资金费率快照。
- 依赖：`数字资产交易详细设计.md`

## 类
- 类 `ExchangeSession(session_ref: str, account_id: str, credential_ref: str, adapter_session_status: str, expires_at: datetime | None, capabilities: dict)`：交易所连接上下文。
- 类 `CryptoOrderRecord(channel_order_id: str, account_id: str, symbol: str, raw_status: str, request_ref: str | None, response_ref: str | None)`：原始订单记录。
- 类 `FundingRateSnapshot(symbol: str, funding_rate: Decimal, occurred_at: datetime, source_id: str)`：资金费率快照。

## 函数
- `mark_available(self) -> None`：会话可用。
- `mark_degraded(self, reason_code: str) -> None`：会话降级。
- `mark_expired(self) -> None`：会话过期。

## 实现顺序
1. 签名型适配器不强依赖浏览器式 session，但仍需输出可用性上下文。
2. 密钥实体不进入领域模型。

## 集成检查
- 能力快照供账户域投影使用，但账户域主权不在本服务。
