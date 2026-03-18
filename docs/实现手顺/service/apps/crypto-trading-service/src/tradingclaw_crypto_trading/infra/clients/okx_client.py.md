# crypto-trading-service/infra/clients/okx_client.py 实现手顺

- 目标代码文件：`apps/crypto-trading-service/src/tradingclaw_crypto_trading/infra/clients/okx_client.py`
- 职责：封装 OKX 等签名型交易所 client。
- 依赖：`数字资产交易详细设计.md`

## 类
- 类 `OkxClient(api_key: str, api_secret: str, passphrase: str, base_url: str)`：OKX HTTP client。

## 函数
- `sign_request(method: str, path: str, body: str, timestamp: str) -> dict[str, str]`：生成签名头。
- `validate_credentials() -> dict`：校验凭据与权限。
- `place_order(payload: dict) -> dict`：下单。
- `amend_order(payload: dict) -> dict`：改单。
- `cancel_order(payload: dict) -> dict`：撤单。
- `fetch_balances() -> list[dict]`：查余额。

## 实现顺序
1. 把签名细节、时钟偏移和错误码映射集中在 client。
2. 不要把统一业务字段强塞进 client。

## 集成检查
- 本文件是新增交易所接入的参考模板。
