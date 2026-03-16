# TradingClaw 后端 Service 详细设计：API契约草案

## 1. 文档目标

- 给研发提供可直接落地的接口契约基线。
- 给前端与 CLI 提供统一调用口径。
- 保证核心链路错误码与状态语义一致。

## 2. 全局约定

## 2.1 版本与路径

- 网关前缀：`/api/v1`
- 内部接口前缀：`/internal/v1`
- MVP 仅开放需求文档中 MVP 能力；V1/V2 能力返回未开放错误码。

## 2.2 请求头

| Header | 必填 | 说明 |
| --- | --- | --- |
| Authorization | 是 | `Bearer <token>` |
| X-Request-Id | 是 | 请求唯一标识，用于链路追踪 |
| X-User-Id | 是 | 操作主体ID |
| X-Session-Id | 否 | 交易所会话标识，未传则使用默认会话 |
| Idempotency-Key | 写接口必填 | 幂等键，防重复写入 |

## 2.3 统一响应结构

```json
{
  "code": "OK",
  "message": "success",
  "request_id": "req_20260316_0001",
  "data": {},
  "timestamp": "2026-03-16T19:30:00Z"
}
```

## 2.4 分页结构

```json
{
  "items": [],
  "page": 1,
  "page_size": 20,
  "total": 153,
  "has_next": true
}
```

## 2.5 数据状态字段

- 行情/资讯类查询必须返回 `data_status`：
  - `fresh`：实时可用数据
  - `stale`：缓存回退数据
- 当 `data_status=stale` 时必须返回 `cache_at`。

## 3. 认证与用户域（identity-service）

## 3.1 注册

- 方法与路径：`POST /api/v1/auth/register`

请求体：

```json
{
  "username": "alice",
  "password": "******",
  "email": "alice@example.com"
}
```

响应 data：

```json
{
  "user_id": "u_10001",
  "username": "alice",
  "created_at": "2026-03-16T19:30:00Z"
}
```

## 3.2 登录

- 方法与路径：`POST /api/v1/auth/login`

请求体：

```json
{
  "username": "alice",
  "password": "******"
}
```

响应 data：

```json
{
  "user_id": "u_10001",
  "token": "jwt_xxx",
  "expires_at": "2026-03-17T19:30:00Z",
  "profile": {
    "level": "L2",
    "strategy_quota": 8,
    "points_total": 10000,
    "points_used": 2500,
    "points_available": 7500
  }
}
```

## 3.3 登出

- 方法与路径：`POST /api/v1/auth/logout`
- 响应 data：`{"session_invalidated": true}`

## 3.4 用户资料

- 方法与路径：`GET /api/v1/user/profile`

响应 data：

```json
{
  "user_id": "u_10001",
  "level": "L2",
  "strategy_quota": 8,
  "points_total": 10000,
  "points_used": 2500,
  "points_available": 7500,
  "accounts": [
    {
      "account_id": "acc_sz_001",
      "market": "securities",
      "is_default": true
    }
  ]
}
```

## 4. 行情域（market-service）

## 4.1 实时行情

- 方法与路径：`GET /api/v1/market/quotes/realtime`
- 查询参数：`symbol`、`asset_type(stock|fund)`

响应 data：

```json
{
  "symbol": "600519",
  "asset_type": "stock",
  "price": 168800,
  "ts": "2026-03-16T19:30:00Z",
  "data_status": "fresh"
}
```

## 4.2 历史行情

- 方法与路径：`GET /api/v1/market/quotes/history`
- 查询参数：`symbol`、`period(1m|5m|1d)`、`start_date`、`end_date`

响应 data：

```json
{
  "symbol": "600519",
  "period": "1d",
  "data_status": "stale",
  "cache_at": "2026-03-16T19:25:00Z",
  "bars": [
    {"ts": "2026-03-15", "open": 168000, "high": 169200, "low": 167500, "close": 168800, "volume": 123456}
  ]
}
```

## 4.3 均线指标

- 方法与路径：`GET /api/v1/market/indicators/ma`
- 查询参数：`symbol`、`windows=5,10,20`、`points=50`

响应 data：

```json
{
  "symbol": "600519",
  "series": [
    {"ts": "2026-03-16", "ma5": 168200, "ma10": 167950, "ma20": 167600}
  ],
  "computable": true
}
```

## 5. 证券交易域（securities-trade-service）

## 5.1 交易登录

- 方法与路径：`POST /api/v1/trade/securities/session/login`

请求体：

```json
{
  "account_id": "acc_sz_001",
  "captcha_code": "1234"
}
```

响应 data：

```json
{
  "session_id": "sess_sec_001",
  "state": "active",
  "expires_at": "2026-03-16T23:59:59Z"
}
```

## 5.2 资产与持仓

- 综合资产：`GET /api/v1/trade/securities/assets`
- 股票持仓：`GET /api/v1/trade/securities/positions/stocks`
- 基金持仓：`GET /api/v1/trade/securities/positions/funds`

## 5.3 下单

- 方法与路径：`POST /api/v1/trade/securities/orders`

请求体：

```json
{
  "account_id": "acc_sz_001",
  "symbol": "600519",
  "side": "buy",
  "order_type": "limit",
  "price": 168800,
  "quantity": 100
}
```

响应 data：

```json
{
  "order_id": "ord_sec_1001",
  "status": "submitted",
  "submitted_at": "2026-03-16T19:30:00Z"
}
```

## 5.4 撤单

- 方法与路径：`POST /api/v1/trade/securities/orders/{order_id}/cancel`
- 响应 data：`{"order_id":"ord_sec_1001","status":"cancelled"}`

## 5.5 流水查询

- 当日委托：`GET /api/v1/trade/securities/orders/today`
- 当日成交：`GET /api/v1/trade/securities/trades/today`
- 未完成委托：`GET /api/v1/trade/securities/orders/open`

## 6. 策略域（strategy-service）

## 6.1 创建策略

- 方法与路径：`POST /api/v1/strategy/instances`

请求体：

```json
{
  "strategy_type": "grid",
  "market": "stock",
  "symbol": "600519",
  "account_id": "acc_sz_001",
  "params": {
    "price_low": 150000,
    "price_high": 180000,
    "grid_count": 20
  },
  "start_mode": "pending_start"
}
```

响应 data：

```json
{
  "strategy_id": "str_1001",
  "state": "pending_start",
  "created_at": "2026-03-16T19:30:00Z"
}
```

## 6.2 策略状态动作

- 启动：`POST /api/v1/strategy/instances/{strategy_id}/start`
- 停止：`POST /api/v1/strategy/instances/{strategy_id}/stop`
- 下线：`POST /api/v1/strategy/instances/{strategy_id}/offline`
- 归档：`POST /api/v1/strategy/instances/{strategy_id}/archive`

响应 data 通用：

```json
{
  "strategy_id": "str_1001",
  "from_state": "running",
  "to_state": "stopped",
  "changed_at": "2026-03-16T19:35:00Z"
}
```

## 7. 交易计划域（planning-service，V1）

## 7.1 创建计划

- 方法与路径：`POST /api/v1/plans`

请求体：

```json
{
  "trade_date": "2026-03-17",
  "market": "stock",
  "symbol": "600519",
  "plan_name": "茅台日内计划"
}
```

响应 data：

```json
{
  "plan_id": "pln_1001",
  "plan_identity": "20260317_600519",
  "current_version": 1,
  "state": "draft"
}
```

## 7.2 列表与详情

- 列表：`GET /api/v1/plans?trade_date=2026-03-17&state=draft&page=1&page_size=20`
- 详情：`GET /api/v1/plans/{plan_id}`

## 7.3 状态迁移

- 提交评审：`POST /api/v1/plans/{plan_id}/submit-review`
- 评审通过：`POST /api/v1/plans/{plan_id}/approve`
- 发布检查：`POST /api/v1/plans/{plan_id}/publish-check`
- 发布提交：`POST /api/v1/plans/{plan_id}/publish`

## 7.4 校验与复盘

- 触发校验：`POST /api/v1/plans/{plan_id}/validations`
- 查询校验历史：`GET /api/v1/plans/{plan_id}/validations`
- 复盘聚合：`GET /api/v1/plans/{plan_id}/review-aggregate`

## 8. 智能能力域（intelligence-service，V1）

## 8.1 问答

- 方法与路径：`POST /api/v1/ai/chat`

请求体：

```json
{
  "question": "当前策略是否需要调仓？",
  "context": {
    "strategy_id": "str_1001",
    "symbol": "600519"
  }
}
```

响应 data：

```json
{
  "answer": "建议降低仓位10%，原因是波动率上升。",
  "model": "gpt-trading-v1",
  "tool_calls": ["strategy.summary", "market.volatility"]
}
```

## 8.2 模型配置

- 写入配置：`POST /api/v1/ai/models/config`
- 获取配置：`GET /api/v1/ai/models/config`
- 模型列表：`GET /api/v1/ai/models`

## 8.3 OCR 识别

- 方法与路径：`POST /api/v1/ai/ocr/captcha`
- 请求体：`{"image_base64":"..."}` 

## 9. 内部接口（unified-trade-kernel）

## 9.1 统一下单

- 方法与路径：`POST /internal/v1/trade-kernel/orders`

请求体：

```json
{
  "channel": "securities",
  "account_id": "acc_sz_001",
  "instrument": {"symbol":"600519","asset_type":"stock"},
  "order": {"side":"buy","type":"limit","price":168800,"quantity":100},
  "context": {"strategy_id":"str_1001","plan_id":"pln_1001"}
}
```

## 9.2 统一撤单

- 方法与路径：`POST /internal/v1/trade-kernel/orders/{order_id}/cancel`

## 9.3 统一查单/持仓/成交

- 查单：`GET /internal/v1/trade-kernel/orders/{order_id}`
- 持仓：`GET /internal/v1/trade-kernel/positions?account_id=acc_sz_001`
- 成交：`GET /internal/v1/trade-kernel/trades?account_id=acc_sz_001`

## 10. 标准错误码

| code | HTTP | 场景 |
| --- | --- | --- |
| OK | 200 | 成功 |
| AUTH_INVALID_TOKEN | 401 | 令牌无效或过期 |
| AUTH_SESSION_EXPIRED | 401 | 会话失效 |
| AUTH_SUBJECT_MISMATCH | 403 | 主体不一致 |
| PERMISSION_DENIED | 403 | 越权访问 |
| ACCOUNT_NOT_BOUND | 403 | 账户未绑定用户 |
| QUOTA_EXCEEDED | 409 | 策略额度不足 |
| POINTS_INSUFFICIENT | 409 | 可用积分不足 |
| STATE_INVALID_TRANSITION | 409 | 非法状态迁移 |
| PLAN_IDENTITY_CONFLICT | 409 | 同日同标的计划身份冲突 |
| VALIDATION_NOT_READY | 409 | 发布前校验未就绪 |
| FEATURE_NOT_AVAILABLE | 409 | 能力未到开放版本 |
| DEPENDENCY_DEGRADED | 503 | 外部依赖降级，已回退缓存 |
| DEPENDENCY_UNAVAILABLE | 503 | 外部依赖不可用 |
| IDEMPOTENT_REPLAY | 200 | 幂等重复请求，返回已存在结果 |
| INTERNAL_ERROR | 500 | 未分类内部错误 |

## 11. 落地约束

- 所有写接口必须校验 `Idempotency-Key`。
- 所有响应必须包含 `request_id`。
- 所有交易与状态迁移写接口必须记录状态日志与审计日志。
- 数据字段口径遵循整数化存储，金额/价格/百分比统一整数表达。
- V1/V2 未开放接口统一返回 `FEATURE_NOT_AVAILABLE` 与版本信息。
