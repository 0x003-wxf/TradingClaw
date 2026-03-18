# TradingClaw Service 实现手顺总览

- 目标代码文件：`monorepo target tree`
- 职责：按详细设计文档冻结目标代码树，并给开发人员提供按文件落地的实现说明。当前仓库尚未存在源码，因此本目录按 `docs/详细设计/service/` 规定的目标代码结构给出实现手顺。
- 依赖：`docs/详细设计/service/后端详细设计.md`、`docs/详细设计/service/工程目录与文件树详细设计.md`、`docs/详细设计/service/运行启动与环境编排详细设计.md`

## 类
- 本文件可以不定义领域类，优先组织流程函数或装配函数。

## 函数
- `phase_0() -> list[str]`：返回平台基线优先实现顺序，先完成共享包、启动器、配置和观测能力。
- `phase_1() -> list[str]`：返回第一批服务顺序，依次实现 `api-gateway`、`identity-service`、`account-service`、`market-service`、`news-service`。
- `phase_2() -> list[str]`：返回交易主链路顺序，依次实现 `audit-risk-service`、`trade-gateway-service`、`securities-trading-service`、`crypto-trading-service`。
- `phase_3() -> list[str]`：返回策略闭环顺序，依次实现 `strategy-service`、`strategy-runtime-service`、`portfolio-calculation-service`、`notification-service`。
- `phase_4() -> list[str]`：返回增强治理与 AI 服务顺序，依次实现 `capability-registry-service`、`ocr-service`、`ai-orchestration-service`。

## 实现顺序
1. 先写 `packages/platform-*` 和 `packages/contracts-*`，否则各服务会重复定义启动、配置、事件和错误口径。
2. 每个服务先完成 `settings` 与 `bootstrap`，再实现 `domain`、`app`、`infra`、`interfaces`。
3. HTTP、gRPC、事件消费者和 Temporal worker 都必须走统一 bootstrap，不允许在业务模块直接启动进程。
4. 所有命令接口要求透传 `request_id`、`trace_id`、`idempotency_key` 和 `operator/user` 上下文。
5. 实现完成后补齐迁移、Schema、proto、配置和启动 profile，才能进入联调。

## 集成检查
- 目录中的每个 `.md` 文件对应一个目标代码文件，程序员可按同路径创建源码。
- 如果实际实现新增代码文件，应同步在 `实现手顺/service/` 下补一份同路径手顺。
- 严禁绕开平台包直接在服务内复制公共逻辑。
