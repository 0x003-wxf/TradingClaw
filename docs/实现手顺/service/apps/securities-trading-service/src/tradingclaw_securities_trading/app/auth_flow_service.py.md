# securities-trading-service/app/auth_flow_service.py 实现手顺

- 目标代码文件：`apps/securities-trading-service/src/tradingclaw_securities_trading/app/auth_flow_service.py`
- 职责：实现券商交互式认证流程。
- 依赖：`证券交易详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `start_auth_flow(account_id: str, credential_ref: str) -> Result[AdapterAuthFlowResponse]`：启动认证。
- `submit_step(auth_flow_id: str, step_code: str, fields: dict) -> Result[AdapterAuthFlowResponse]`：提交流程步骤。
- `refresh_session(account_id: str, broker_session_id: str) -> Result[AdapterSessionResult]`：会话续期或健康检查。
- `close_session(account_id: str, broker_session_id: str) -> Result[dict]`：关闭运行时会话。

## 实现顺序
1. 先接通 `credential_ref` 和真实密钥读取，再实现一步认证场景。
2. 再补图片验证码、短信验证码、OTP 等多步场景。

## 集成检查
- 每一步都返回 `required_fields` 和 `artifacts`。
