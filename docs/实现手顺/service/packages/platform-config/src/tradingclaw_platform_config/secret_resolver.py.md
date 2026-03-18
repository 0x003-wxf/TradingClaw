# platform-config/secret_resolver.py 实现手顺

- 目标代码文件：`packages/platform-config/src/tradingclaw_platform_config/secret_resolver.py`
- 职责：解析 `vault://`、文件挂载路径和只读环境变量，生成运行时密钥快照。
- 依赖：`配置与密钥管理详细设计.md`

## 类
- 类 `ResolvedSecret(path: str, value: str, version: str | None)`：解析后的密钥对象。

## 函数
- `resolve_secret(ref: str, secrets_path: str) -> ResolvedSecret`：解析单个密钥引用。
- `resolve_payload(payload: dict, secrets_path: str) -> dict`：递归解析配置中的密钥引用。
- `mask_secret(value: str) -> str`：返回脱敏值。

## 实现顺序
1. 真实密钥只在内存和只读挂载目录出现，不写回配置文件。
2. 对不存在或版本错误的密钥抛出启动失败异常。

## 集成检查
- 日志、异常和调试输出必须调用 `mask_secret`。
