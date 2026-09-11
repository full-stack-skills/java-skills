# 证据契约

## 身份键

每条证据至少绑定以下身份：

```text
repository + worktree + branch + commit SHA + toolchain + command/config identity + timestamp
```

缺任一关键身份时标为 `unknown`，并写明如何补齐。报告中的事实、推断和建议分开：命令退出码是事实；“疑似端口冲突”是推断；“重跑完整 Reactor”是建议。

## 门禁验收字段

| 门禁 | 必需字段 | 通过条件 |
|---|---|---|
| 工具链 | `java -version`、Maven/Wrapper 版本、分支契约来源 | 实际版本与契约一致 |
| 依赖来源 | 精确 GAV、依赖路径、scope、解析命令 | 引入路径可复现 |
| 许可证来源 | 固定版本 URL/文件、内容语义、校验时间 | 来源支持该精确版本的结论 |
| 策略 | 命令、配置身份、退出码、违规清单 | 最终 SHA 上退出码 0 且无未授权例外 |
| SBOM | 格式、生成命令、范围、路径、SHA-256 或等价摘要、坐标抽查 | 最终 SHA 重新生成且依赖身份匹配 |
| Reactor | 命令、工具链、预期模块清单、成功/失败/跳过集合、日志、退出码 | 预期范围完整执行且退出码 0；恢复执行时两次模块集合可核对为完整并集 |
| Git | diff 范围、commit、local/tracking SHA、每个 required remote 的 ref/SHA | 本地、跟踪与所有必需远端 SHA 相同且变更范围受控 |
| CI | workflow/job、run URL/ID、head SHA、最终 conclusion | 最终 SHA 的所有必需 job 为 success |

## 异常分类

- `change-related`：许可证、依赖、POM、生成配置或测试因本次变更失败；修复后产生新证据。
- `environment-transient`：端口、临时网络或 runner 抖动；必须用完整重跑或可审计恢复执行闭合。
- `infrastructure`：仓库、凭据、runner、镜像或上游服务不可用；状态通常为 `blocked` 或 `failed`，不能绿化。
- `unknown`：尚无足够日志。先保留原始失败，再获取证据，不猜根因。

## 完成判定

```text
branch_passed = all(required_gate == passed)
overall_passed = all(target_branch == branch_passed)
```

`skipped` 只有在门禁预先定义为非必需且已有授权时，才不阻断流程；报告仍保留风险。任何目标 SHA 改变，都重新评估所有依赖代码/配置身份的证据。
