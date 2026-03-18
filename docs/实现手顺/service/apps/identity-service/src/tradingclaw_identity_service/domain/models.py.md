# identity-service/domain/models.py 实现手顺

- 目标代码文件：`apps/identity-service/src/tradingclaw_identity_service/domain/models.py`
- 职责：定义身份域聚合、值对象和状态迁移方法。
- 依赖：`用户与账户详细设计.md`、`状态字段枚举表.md`

## 类
- 类 `User(user_id: str, principal: str, status: str, level: str, created_at: datetime)`：用户聚合根。
- 类 `Session(session_id: str, user_id: str, auth_session_status: str, access_token: str, expires_at: datetime)`：登录会话。
- 类 `QuotaSnapshot(user_id: str, strategy_quota_total: int, strategy_quota_used: int)`：策略额度快照。
- 类 `PointsSummary(user_id: str, points_total: Decimal, points_available: Decimal)`：积分聚合对象。

## 函数
- `activate_session(self) -> None`：`CREATED -> ACTIVE`。
- `expire_session(self, now: datetime) -> None`：会话过期。
- `revoke_session(self, revoked_at: datetime) -> None`：注销或吊销。
- `assert_active(self, now: datetime) -> None`：校验会话可用。

## 实现顺序
1. 领域模型只表达状态和规则，不依赖 ORM。
2. 严格按 `auth_session_status` 允许迁移实现方法。

## 集成检查
- 仓储层再把这里的模型映射到数据库表。
