---
name: maven-release-validation
license: Apache-2.0
description: Validate multi-branch Maven releases end to end, including branch-specific JDK/Maven contracts, source tests, Git remote synchronization, sequential repository publication, and isolated clean-cache downstream consumption. Use for Maven release readiness, private-repository publication, or release evidence audits; do not use for ordinary local builds.
---

# Maven Release Validation

Turn a Maven release request into an evidence-backed checklist. Treat source verification, Git synchronization, repository publication, clean-cache consumption, CI, and runtime readiness as separate gates. Never infer a later gate from an earlier one.

## Quick start

Typical requests:

- “用 `$maven-release-validation` 验证三个维护分支能否发布到阿里云 Maven。”
- “审计这次 SNAPSHOT 发布，确认 GitHub、Codeup 和远端构件一致。”
- “为下游项目做空缓存消费验证，并给出可复核的发布报告。”

Start by discovering the real repository/worktree, current branch, dirty state, wrapper, POM hierarchy, repository configuration, and existing release scripts. If `.codegraph/` exists, use CodeGraph before text search for code-structure questions. Do not create/switch branches, commit, push, deploy, delete caches, or modify repository settings unless the user requested that mutation.

## Inputs and release matrix

Build a matrix before executing:

| Field | Required evidence |
|---|---|
| Release line | exact branch/ref and working directory |
| Toolchain | required JDK, Maven/wrapper version, active `java -version` and Maven runtime |
| Coordinates | groupId/artifactId/version and SNAPSHOT vs release |
| Scope | expected reactor/modules and changed files |
| Remotes | exact Git remote names/URLs and target refs |
| Repository | repository ID and configured destination; never print credentials |
| Consumers | minimal external consumer(s) and artifacts/APIs they exercise |
| Gates | tests, Enforcer, signing/SBOM/license/security/CI requirements and approved skips |

If a material value is unknown, continue with read-only discovery. Before an external mutation, stop with `缺少[具体项]；请提供或确认[补充方式]`. Never guess credentials, repository IDs, branches, versions, or release commands.

## Gate sequence

Read [references/release-checklist.md](references/release-checklist.md) before running or auditing a release. Use its evidence table as the report skeleton.

1. **Baseline** — identify Git root/worktrees, preserve dirty/untracked work, enumerate exact changed files, and record local/tracking/remote SHAs.
2. **Per-line verification** — activate each line’s actual JDK/Maven contract and run the project’s full required verification. Record reactor/module counts and test totals, failures, errors, and skips. A compiler from another line is not proof.
3. **Source synchronization** — when authorized, commit branch-specific diffs separately and push to every required remote. Verify each remote ref resolves to the intended commit; upload output alone is insufficient.
4. **Publication** — publish lines serially unless repository semantics prove parallel publication safe. A robust default is oldest JDK line to newest. Use the repository’s established wrapper/settings/profile. If full tests already passed immediately beforehand, deploy may skip re-running tests only when project policy permits; do not silently bypass Enforcer, signing, license, SBOM, security, or repository gates.
5. **Remote proof** — require complete reactor success and confirm the expected POM/JAR plus required sources, javadoc, checksums, signatures, and metadata are visible remotely. Partial uploads or a running process are `in_progress`, not `passed`.
6. **Clean-cache consumption** — create a new isolated temporary Maven local repository for each proof. Resolve from configured remote repositories without relying on the producer checkout or ordinary local cache. Test parent/BOM import, direct dependency resolution, and a minimal compile/test or startup path for changed runtime adapters.
7. **Final reconciliation** — compare local HEAD, tracking ref, every required Git remote SHA, published version/timestamp, and consumer result. Report CI and runtime deployment separately if they were not observed.

Stop serial publication on the first failed line. Preserve logs and already published evidence, mark later lines `not_run`, and do not retry or overwrite artifacts without explicit authorization and a repository-safe recovery plan.

## Evidence rules

- Use `passed`, `failed`, `in_progress`, `not_run`, or `skipped` for every gate.
- A skip is never a pass. State who/what authorized it and the residual risk.
- Distinguish observed command output from inference. Do not claim current remote state from an older screenshot, summary, or local cache.
- Redact tokens, passwords, private repository credentials, and sensitive URLs from commands and logs.
- Prefer an isolated repository such as a directory created by `mktemp -d`; remove it only when safe and authorized. Never delete the user’s normal Maven repository for “clean-cache” proof.
- For SNAPSHOTs, capture the resolved timestamp/build number where available so the consumer proof can be tied to the publication.

## Output

Return a compact per-line table plus blockers and next actions. Include commands executed (with secrets redacted), log paths, module/test counts, exact SHAs, remote artifact evidence, consumer evidence, skips, and timestamps. If the request is checklist-only, produce planned commands with `not_run` status and do not imply execution.

For failure wording and misleading shortcuts, read [references/anti-patterns.md](references/anti-patterns.md). For edge cases such as SNAPSHOT metadata, partial publication, Maven 4 warnings, or CI/runtime boundaries, read [references/faq.md](references/faq.md).

## Capability boundaries

- **Well suited:** multi-branch release readiness; private Maven publication audits; empty-cache downstream proofs; Git/repository evidence reconciliation.
- **Needs project material:** credentials/settings; exact branch matrix; required quality gates; consumer examples; repository overwrite/retention policy.
- **Out of scope:** inventing credentials; bypassing required gates; destructive cache cleanup; declaring production runtime readiness from Maven publication alone; changing release architecture without a separate approved design.
