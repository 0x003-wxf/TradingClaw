# securities-trading-service/infra/clients/eastmoney_client.py 实现手顺

- 目标代码文件：`apps/securities-trading-service/src/tradingclaw_securities_trading/infra/clients/eastmoney_client.py`
- 职责：封装东方财富等交互式券商 HTTP 客户端。
- 依赖：`证券交易详细设计.md`

## 类
- 类 `EastMoneyClient(base_url: str, timeout_seconds: int)`：券商 HTTP client。

## 函数
- `start_login(login_principal: str) -> ChallengePayload`：发起登录流程并返回挑战材料。
- `submit_credentials(session_ref: str, login_password: str, captcha_text: str | None = None, sms_code: str | None = None, otp_code: str | None = None) -> BrokerLoginResult`：提交登录材料。
- `place_order(payload: dict) -> dict`：提交券商订单。
- `cancel_order(payload: dict) -> dict`：发起撤单。
- `fetch_positions(session_ref: str) -> list[dict]`：查询持仓。

## 实现顺序
1. 把 HTTP 请求、cookie/session 容器和原始字段映射集中在本文件。
2. 不要把业务状态机放进 client。

## 集成检查
- 后续新增券商时复制抽象而不是复制东方财富实现。
