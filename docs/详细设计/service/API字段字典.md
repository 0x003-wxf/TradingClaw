# TradingClaw 后端 Service 联调字段字典

## 1. 使用说明

- 本文档用于前后端与 CLI 联调阶段字段对齐。
- 字段命名统一使用 snake_case。
- 金额、价格、比例统一采用整数口径，单位见字段约束列。
- 时间统一使用 ISO8601 UTC 字符串。

## 2. 全局字段字典

## 2.1 请求头

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| Authorization | string | 是 | Bearer jwt_xxx | Bearer 模式；token 过期返回 AUTH_INVALID_TOKEN |
| X-Request-Id | string | 是 | req_20260316_0001 | 全局唯一；建议长度 8~64 |
| X-User-Id | string | 是 | u_10001 | 与 token 主体一致，否则 AUTH_SUBJECT_MISMATCH |
| X-Session-Id | string | 否 | sess_sec_001 | 未传使用默认会话；传入需校验有效性 |
| Idempotency-Key | string | 写接口必填 | idem_ord_1001 | 同一业务动作重复提交必须返回同结果 |

## 2.2 统一响应

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| code | string | 是 | OK | 见错误码字典 |
| message | string | 是 | success | 面向人类可读 |
| request_id | string | 是 | req_20260316_0001 | 原样回传 X-Request-Id |
| data | object | 是 | {} | 业务数据容器 |
| timestamp | string | 是 | 2026-03-16T19:30:00Z | ISO8601 UTC |

## 2.3 分页对象

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| items | array | 是 | [] | 当前页数据 |
| page | integer | 是 | 1 | >=1 |
| page_size | integer | 是 | 20 | 1~200 |
| total | integer | 是 | 153 | >=0 |
| has_next | boolean | 是 | true | 是否存在下一页 |

## 2.4 公共枚举

| 枚举名 | 值 | 说明 |
| --- | --- | --- |
| data_status | fresh/stale | fresh 实时数据；stale 缓存回退 |
| asset_type | stock/fund/crypto | 资产类型 |
| order_side | buy/sell | 买卖方向 |
| order_type | limit/market | 委托类型 |
| strategy_state | pending_start/running/stopped/offline/archived | 策略状态 |
| plan_state | draft/in_review/approved/published/archived | 计划状态 |

## 3. 认证与用户域字段字典

## 3.1 POST /api/v1/auth/register

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| username | string | 是 | alice | 4~32，字母数字下划线 |
| password | string | 是 | P@ssw0rd123 | 8~64，必须包含数字与字母 |
| email | string | 是 | alice@example.com | RFC 邮箱格式 |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| user_id | string | 是 | u_10001 | 系统生成唯一ID |
| username | string | 是 | alice | 与注册值一致 |
| created_at | string | 是 | 2026-03-16T19:30:00Z | ISO8601 UTC |

## 3.2 POST /api/v1/auth/login

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| username | string | 是 | alice | 已注册用户名 |
| password | string | 是 | P@ssw0rd123 | 明文传输必须走 HTTPS |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| user_id | string | 是 | u_10001 | 登录用户ID |
| token | string | 是 | jwt_xxx | JWT 或等价令牌 |
| expires_at | string | 是 | 2026-03-17T19:30:00Z | token 过期时间 |
| profile.level | string | 是 | L2 | 用户等级 |
| profile.strategy_quota | integer | 是 | 8 | 可运行策略上限 |
| profile.points_total | integer | 是 | 10000 | 总积分，单位分 |
| profile.points_used | integer | 是 | 2500 | 占用积分，单位分 |
| profile.points_available | integer | 是 | 7500 | 可用积分=total-used |

## 3.3 GET /api/v1/user/profile

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| user_id | string | 是 | u_10001 | 用户ID |
| level | string | 是 | L2 | 等级口径统一 |
| strategy_quota | integer | 是 | 8 | >=0 |
| points_total | integer | 是 | 10000 | 整数存储 |
| points_used | integer | 是 | 2500 | 不得超过 total |
| points_available | integer | 是 | 7500 | 自动计算字段 |
| accounts | array<object> | 是 | [{...}] | 绑定账户列表 |
| accounts[].account_id | string | 是 | acc_sz_001 | 用户绑定账户 |
| accounts[].market | string | 是 | securities | securities/crypto |
| accounts[].is_default | boolean | 是 | true | 默认会话对应账户 |

## 4. 行情域字段字典

## 4.1 GET /api/v1/market/quotes/realtime

查询参数：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| symbol | string | 是 | 600519 | 证券或基金代码 |
| asset_type | string | 是 | stock | stock/fund |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| symbol | string | 是 | 600519 | 回传请求代码 |
| asset_type | string | 是 | stock | stock/fund |
| price | integer | 是 | 168800 | 价格整数，单位厘 |
| ts | string | 是 | 2026-03-16T19:30:00Z | 数据时间 |
| data_status | string | 是 | fresh | fresh/stale |
| cache_at | string | 条件必填 | 2026-03-16T19:25:00Z | data_status=stale 时必填 |

## 4.2 GET /api/v1/market/quotes/history

查询参数：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| symbol | string | 是 | 600519 | 资产代码 |
| period | string | 是 | 1d | 1m/5m/15m/1h/1d |
| start_date | string | 是 | 2026-03-01 | yyyy-MM-dd |
| end_date | string | 是 | 2026-03-16 | yyyy-MM-dd，>=start_date |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| symbol | string | 是 | 600519 | 资产代码 |
| period | string | 是 | 1d | 同请求 |
| data_status | string | 是 | stale | fresh/stale |
| cache_at | string | 条件必填 | 2026-03-16T19:25:00Z | stale 必填 |
| bars | array<object> | 是 | [{...}] | 按 ts 升序且去重 |
| bars[].ts | string | 是 | 2026-03-15 | K线时间 |
| bars[].open | integer | 是 | 168000 | 单位厘 |
| bars[].high | integer | 是 | 169200 | 单位厘 |
| bars[].low | integer | 是 | 167500 | 单位厘 |
| bars[].close | integer | 是 | 168800 | 单位厘 |
| bars[].volume | integer | 是 | 123456 | >=0 |

## 4.3 GET /api/v1/market/indicators/ma

查询参数：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| symbol | string | 是 | 600519 | 资产代码 |
| windows | string | 是 | 5,10,20 | 逗号分隔整数窗口 |
| points | integer | 否 | 50 | 默认 50，范围 1~500 |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| symbol | string | 是 | 600519 | 资产代码 |
| series | array<object> | 是 | [{...}] | 指标序列 |
| series[].ts | string | 是 | 2026-03-16 | 时间点 |
| series[].ma5 | integer | 否 | 168200 | 对应窗口均线 |
| series[].ma10 | integer | 否 | 167950 | 对应窗口均线 |
| series[].ma20 | integer | 否 | 167600 | 对应窗口均线 |
| computable | boolean | 是 | true | 样本不足时为 false |
| reason | string | 条件必填 | sample_not_enough | computable=false 时必填 |

## 5. 证券交易域字段字典

## 5.1 POST /api/v1/trade/securities/session/login

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| account_id | string | 是 | acc_sz_001 | 必须属于当前用户 |
| captcha_code | string | 是 | 1234 | 券商验证码 |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| session_id | string | 是 | sess_sec_001 | 交易会话ID |
| state | string | 是 | active | active/expired |
| expires_at | string | 是 | 2026-03-16T23:59:59Z | 会话过期时间 |

## 5.2 POST /api/v1/trade/securities/orders

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| account_id | string | 是 | acc_sz_001 | 账户归属校验通过 |
| symbol | string | 是 | 600519 | 证券代码 |
| side | string | 是 | buy | buy/sell |
| order_type | string | 是 | limit | limit/market |
| price | integer | 条件必填 | 168800 | limit 必填，单位厘 |
| quantity | integer | 是 | 100 | >0，股票按 100 整倍数 |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| order_id | string | 是 | ord_sec_1001 | 订单ID |
| status | string | 是 | submitted | submitted/rejected/filled/cancelled |
| submitted_at | string | 是 | 2026-03-16T19:30:00Z | 提交时间 |
| fail_reason | string | 条件必填 | balance_not_enough | status=rejected 时必填 |

## 5.3 POST /api/v1/trade/securities/orders/{order_id}/cancel

路径参数：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| order_id | string | 是 | ord_sec_1001 | 已存在订单ID |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| order_id | string | 是 | ord_sec_1001 | 回传 |
| status | string | 是 | cancelled | cancelled 或 reject |
| fail_reason | string | 条件必填 | already_filled | 撤单失败时必填 |

## 6. 策略域字段字典

## 6.1 POST /api/v1/strategy/instances

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| strategy_type | string | 是 | grid | grid/martingale/rebalance/dca |
| market | string | 是 | stock | stock/etf/crypto/fund |
| symbol | string | 是 | 600519 | 标的代码 |
| account_id | string | 是 | acc_sz_001 | 账户归属校验通过 |
| params | object | 是 | {...} | 与 strategy_type 对应 |
| params.price_low | integer | 条件必填 | 150000 | grid 必填，单位厘 |
| params.price_high | integer | 条件必填 | 180000 | grid 必填，>price_low |
| params.grid_count | integer | 条件必填 | 20 | grid 必填，2~200 |
| start_mode | string | 是 | pending_start | pending_start/running |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| strategy_id | string | 是 | str_1001 | 策略ID |
| state | string | 是 | pending_start | 见 strategy_state |
| created_at | string | 是 | 2026-03-16T19:30:00Z | 创建时间 |

## 6.2 POST /api/v1/strategy/instances/{strategy_id}/start|stop|offline|archive

路径参数：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| strategy_id | string | 是 | str_1001 | 策略ID |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| strategy_id | string | 是 | str_1001 | 策略ID |
| from_state | string | 是 | running | 原状态 |
| to_state | string | 是 | stopped | 目标状态 |
| changed_at | string | 是 | 2026-03-16T19:35:00Z | 变更时间 |

## 7. 交易计划域字段字典（V1）

## 7.1 POST /api/v1/plans

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| trade_date | string | 是 | 2026-03-17 | yyyy-MM-dd |
| market | string | 是 | stock | stock/fund/crypto |
| symbol | string | 是 | 600519 | 标的代码 |
| plan_name | string | 是 | 茅台日内计划 | 1~64 字符 |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| plan_id | string | 是 | pln_1001 | 计划ID |
| plan_identity | string | 是 | 20260317_600519 | 同日同标的唯一 |
| current_version | integer | 是 | 1 | 初始为 1 |
| state | string | 是 | draft | 见 plan_state |

## 7.2 POST /api/v1/plans/{plan_id}/publish-check

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| plan_id | string | 是 | pln_1001 | 计划ID |
| review_ready | boolean | 是 | true | 评审是否就绪 |
| validation_ready | boolean | 是 | true | 校验是否就绪 |
| blockers | array<string> | 是 | [] | 阻塞原因列表 |

## 7.3 POST /api/v1/plans/{plan_id}/publish

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| plan_id | string | 是 | pln_1001 | 计划ID |
| version | integer | 是 | 1 | 发布版本 |
| state | string | 是 | published | 发布后状态 |
| published_at | string | 是 | 2026-03-16T20:00:00Z | 发布时间 |

## 8. 智能能力域字段字典（V1）

## 8.1 POST /api/v1/ai/chat

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| question | string | 是 | 当前策略是否需要调仓？ | 1~2000 字符 |
| context | object | 否 | {...} | 结构化上下文 |
| context.strategy_id | string | 否 | str_1001 | 策略上下文 |
| context.symbol | string | 否 | 600519 | 标的上下文 |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| answer | string | 是 | 建议降低仓位10%... | 模型回复文本 |
| model | string | 是 | gpt-trading-v1 | 实际使用模型标识 |
| tool_calls | array<string> | 否 | ["strategy.summary"] | 工具调用轨迹 |

## 8.2 POST /api/v1/ai/ocr/captcha

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| image_base64 | string | 是 | iVBORw0KGgo... | Base64 编码图片 |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| text | string | 条件必填 | 8K2P | 识别成功返回 |
| success | boolean | 是 | true | 是否识别成功 |
| error_reason | string | 条件必填 | low_confidence | success=false 时必填 |

## 9. 内部统一交易接口字段字典

## 9.1 POST /internal/v1/trade-kernel/orders

请求体：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| channel | string | 是 | securities | securities/crypto |
| account_id | string | 是 | acc_sz_001 | 账户ID |
| instrument.symbol | string | 是 | 600519 | 标的代码 |
| instrument.asset_type | string | 是 | stock | stock/fund/crypto |
| order.side | string | 是 | buy | buy/sell |
| order.type | string | 是 | limit | limit/market |
| order.price | integer | 条件必填 | 168800 | limit 必填 |
| order.quantity | integer | 是 | 100 | >0 |
| context.strategy_id | string | 否 | str_1001 | 策略上下文 |
| context.plan_id | string | 否 | pln_1001 | 计划上下文 |

响应 data：

| 字段 | 类型 | 必填 | 示例 | 约束 |
| --- | --- | --- | --- | --- |
| order_id | string | 是 | ord_kernel_1001 | 统一订单ID |
| channel_order_id | string | 否 | ord_sec_1001 | 通道侧订单ID |
| status | string | 是 | submitted | 统一状态 |
| accepted_at | string | 是 | 2026-03-16T19:30:00Z | 受理时间 |

## 10. 错误码字典

| code | HTTP | 示例 message | 约束 |
| --- | --- | --- | --- |
| OK | 200 | success | 成功 |
| AUTH_INVALID_TOKEN | 401 | token invalid | 需重新登录 |
| AUTH_SESSION_EXPIRED | 401 | session expired | 需重建会话 |
| AUTH_SUBJECT_MISMATCH | 403 | subject mismatch | 请求主体不一致 |
| PERMISSION_DENIED | 403 | permission denied | 无权限 |
| ACCOUNT_NOT_BOUND | 403 | account not bound | 账户归属校验失败 |
| QUOTA_EXCEEDED | 409 | strategy quota exceeded | 策略额度不足 |
| POINTS_INSUFFICIENT | 409 | points insufficient | 可用积分不足 |
| STATE_INVALID_TRANSITION | 409 | invalid transition | 非法状态迁移 |
| PLAN_IDENTITY_CONFLICT | 409 | plan identity conflict | 计划身份冲突 |
| VALIDATION_NOT_READY | 409 | validation not ready | 发布前校验未通过 |
| FEATURE_NOT_AVAILABLE | 409 | feature not available in MVP | 版本未开放 |
| DEPENDENCY_DEGRADED | 503 | dependency degraded, use cache | 已降级回退 |
| DEPENDENCY_UNAVAILABLE | 503 | dependency unavailable | 外部依赖不可用 |
| IDEMPOTENT_REPLAY | 200 | idempotent replay | 返回历史成功结果 |
| INTERNAL_ERROR | 500 | internal error | 未分类异常 |

## 11. 联调检查清单

- 每个接口入参是否按“类型+必填+约束”校验并返回明确错误码。
- 每个写接口是否强制要求 `Idempotency-Key`。
- 每个响应是否包含 `request_id` 与统一响应结构。
- 行情与资讯接口在回退缓存时是否返回 `data_status=stale` 与 `cache_at`。
- 状态迁移接口是否保证 from_state/to_state 可追踪。
