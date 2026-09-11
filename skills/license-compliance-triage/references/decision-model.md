# 判定模型与证据契约

## 决策顺序

1. 组件是否进入最终发布物？若否，记录排除依据，不将其伪装成许可证兼容。
2. 是否拥有完整坐标和版本？若否，判为“需补证据”。
3. 原始声明能否可靠映射到 SPDX？若否，检查官方 POM/LICENSE；仍未知则阻断或补证。
4. 表达式是单许可证、`OR`、`AND`，还是工具生成的无语义列表？
5. `OR` 中若存在满足组织政策的分支，明确选择该分支并保存官方证据；不能只删除不喜欢的分支。
6. 许可证义务是否与实际使用方式兼容？弱 copyleft、例外条款和修改源码必须单独评估。
7. 证据是否绑定当前版本且不可漂移？版本变化时旧记录失效。

## 五类结论

- **通过**：政策明确允许，证据完整，没有额外未履行义务。
- **有条件通过**：可用，但交付前必须履行并验证具体义务。
- **需补证据**：现有事实不足，不能可靠通过或阻断。
- **阻断**：没有兼容分支，或使用方式与政策明确冲突。
- **不在发布物**：经 SBOM/制品核验后确认只属于构建、测试、provided 或被排除作用域。

## 精确选择记录

每条例外至少包含：

```text
coordinate
declared_expression
selected_spdx
evidence_url
evidence_type
usage_mode
obligations
justification
reviewed_at
```

`coordinate` 必须包含版本；`selected_spdx` 必须出现在声明或官方证据中；证据应为 HTTPS 官方来源并固定到版本。拒绝空值、`unknown`、`n/a`、`TODO`、默认分支 URL、正则坐标和 group/artifact 级宽泛例外。

## 验证用例

- 单一允许许可证应通过。
- `EPL-2.0 OR LGPL-2.1` 选择 EPL 且证据完整时不应因 LGPL 词样误报。
- `Apache-2.0 AND GPL-3.0` 不得按 `OR` 处理。
- 缺版本、选择不在声明中、漂移 URL、陈旧记录和未知许可证应失败。
- 只在构建工具中出现且未进入 SBOM 的项可标为“不在发布物”，但要保存排除依据。
- 真实报告、SBOM 和最终制品应在规则单测后再验证。

## 定制参数

用户可提供 `policy_goal`、`distribution_model`、`allowed_obligations`、`forbidden_families`、`evidence_sources` 和 `output_format`。不得把一个项目的允许集合自动推广到其他组织或产品。
