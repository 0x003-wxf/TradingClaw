# audit-risk-service/app/risk_service.py 实现手顺

- 目标代码文件：`apps/audit-risk-service/src/tradingclaw_audit_risk/app/risk_service.py`
- 职责：实现统一风险裁决。
- 依赖：`风控审计与通知详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 输入输出对象
- 类 `RiskCheckCommand(scene: str, resource_type: str, resource_id: str | None, resource_ref: str, account_id: str | None, instrument_id: str | None, action_type: str, payload: dict, request_id: str, trace_id: str | None, workflow_id: str | None, idempotency_key: str | None)`：风控检查输入对象。
- 类 `RiskDecisionView(decision: str, review_required: bool, severity: str | None, matched_rule_code: str | None, matched_rules: list[dict], risk_event_id: str | None, reason_code: str | None, workflow_id: str | None, resume_token: str | None)`：风控裁决结果对象。

## 函数
- `evaluate_order_risk(command: RiskCheckCommand) -> Result[RiskDecisionView]`：交易下单风控。
- `evaluate_strategy_risk(command: RiskCheckCommand) -> Result[RiskDecisionView]`：策略执行窗口风控。
- `load_active_rules(scene: str) -> list[RiskRule]`：加载场景规则。
- `persist_risk_event(decision: RiskDecisionView, command: RiskCheckCommand) -> RiskEvent | None`：命中时写风险事件。

## 细化函数
- `filter_rules_by_scene(scene: str, rules: list[RiskRule]) -> list[RiskRule]`：参数为场景和规则集合；返回匹配场景的规则列表；职责是过滤停用规则和不适用场景规则。
- `sort_rules_by_priority(rules: list[RiskRule]) -> list[RiskRule]`：参数为规则集合；返回按优先级排序后的规则；职责是保证决策顺序稳定。
- `evaluate_single_rule(rule: RiskRule, command: RiskCheckCommand) -> dict | None`：参数为规则和检查命令；返回命中结果或空；职责是执行单条规则匹配。
- `evaluate_account_status(command: RiskCheckCommand) -> dict | None`：参数为检查命令；返回规则命中结果或空；职责是检查账户状态和统一交易会话有效性。
- `evaluate_notional_limit(command: RiskCheckCommand) -> dict | None`：参数为检查命令；返回命中结果或空；职责是检查单笔下单金额或数量上限。
- `evaluate_daily_quota(command: RiskCheckCommand) -> dict | None`：参数为检查命令；返回命中结果或空；职责是检查日累计交易额度。
- `evaluate_leverage_limit(command: RiskCheckCommand) -> dict | None`：参数为检查命令；返回命中结果或空；职责是检查杠杆和保证金约束。
- `evaluate_submit_frequency(command: RiskCheckCommand) -> dict | None`：参数为检查命令；返回命中结果或空；职责是检查高频提交限流。
- `evaluate_market_blacklist(command: RiskCheckCommand) -> dict | None`：参数为检查命令；返回命中结果或空；职责是检查标的黑白名单和交易时段。
- `merge_rule_hits(hits: list[dict]) -> RiskDecisionView`：参数为所有命中规则；返回统一裁决；职责是按 `REJECT > REVIEW > ALLOW` 合并主命中和附加命中列表。
- `build_resume_token(command: RiskCheckCommand) -> str | None`：参数为风控检查命令；返回恢复令牌；职责是在 REVIEW 场景构造后续恢复引用。
- `publish_review_required(decision: RiskDecisionView, command: RiskCheckCommand) -> None`：参数为裁决结果和检查命令；无返回值；职责是发布 `risk.review_required` 事件。

## 仓储与外部协作
- `RiskRuleRepository.list_active(scene: str) -> list[RiskRule]`：读取生效规则。
- `RiskEventRepository.save(event: RiskEvent) -> RiskEvent`：保存风险事件。
- `TradeGatewayQueryClient.get_account_snapshot(account_id: str) -> dict`：读取统一余额和订单事实快照。
- `MessagingProducer.publish(envelope: EventEnvelope) -> None`：发布复核请求事件。

## 实现顺序
1. 先实现规则加载和匹配框架，再补首批 P0 规则。
2. 规则冲突时按 `REJECT > REVIEW > ALLOW`，同级取最严格。
3. `evaluate_order_risk` 和 `evaluate_strategy_risk` 复用同一套规则引擎，但使用不同 scene 和规则集。
4. `persist_risk_event` 只在命中 `REJECT` 或 `REVIEW` 时落库，`ALLOW` 默认不写事件，除非审计策略要求旁路留痕。
5. REVIEW 决策要同步生成 `workflow_id` 或 `resume_token` 级恢复引用，避免消费者自行拼请求。

## 集成检查
- 额度口径优先取 trade-gateway 的统一订单事实与余额快照。
- 风险事件必须持久化 `rule_code`、`rule_version`、`resource_ref`、`workflow_id`、`resume_token` 等恢复关键字段。
- 规则刷新要支持版本化加载并在 30 秒内完成全节点可见。
