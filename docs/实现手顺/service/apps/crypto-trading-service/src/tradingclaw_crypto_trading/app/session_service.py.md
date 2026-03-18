# crypto-trading-service/app/session_service.py 实现手顺

- 目标代码文件：`apps/crypto-trading-service/src/tradingclaw_crypto_trading/app/session_service.py`
- 职责：实现交易所凭据校验、会话建立和能力快照刷新。
- 依赖：`数字资产交易详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `validate_credential(account_id: str, credential_ref: str) -> Result[AdapterSessionResult]`：校验 API Key/Secret。
- `refresh_session(account_id: str, session_ref: str) -> Result[AdapterSessionResult]`：刷新连通性和权限快照。
- `close_session(account_id: str, session_ref: str) -> Result[dict]`：关闭会话。

## 实现顺序
1. 先打通签名链路，再补充权限范围和时钟偏移校验。
2. 校验成功后发布 `exchange_credential.validated` 事件。

## 集成检查
- 真实 secret 通过密钥系统读取。
