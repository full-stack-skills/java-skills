---
name: maven-snapshot-publication-recovery
license: Apache-2.0
description: Use when a Maven SNAPSHOT appears published but fresh consumers fail on missing parents, BOMs, aggregate POMs, transitive modules, metadata/authentication incompatibilities, or partial reactor uploads.
---

# Maven SNAPSHOT Publication Recovery

## Overview

Recover incomplete Maven SNAPSHOT publication by proving the failure from an isolated consumer, classifying the first unresolved coordinate, backfilling only verified gaps, and repeating the consumer probe until the dependency graph closes.

The core rule is: **publication is not proven by a successful local build or upload; it is proven by remote resolution from a new Maven local repository.**

Use `maven-release-validation` for planned release readiness and normal end-to-end publication. Use this skill after publication has become inconsistent or a fresh consumer exposes a missing remote dependency graph.

## Quick Start

Use this skill for requests such as:

- “这个 SNAPSHOT 已上传，但新机器还是解析失败，帮我恢复发布。”
- “用空缓存验证所有维护分支，只补齐缺失制品。”
- “Maven 4 deploy 被仓库 401 阻断，安全地改用 Maven 3 回填。”

Before any remote mutation, inspect the real Git root, branch, dirty state, POM hierarchy, repository settings, required JDK/Maven matrix, and any existing verification script. Do not switch branches, publish, or overwrite artifacts without the authorization already implied or explicitly given by the user.

## Capability Boundaries

### Well suited

- Diagnose a remotely incomplete SNAPSHOT graph from fresh-cache consumer failures.
- Recover missing parent, BOM, aggregate POM, module, classifier, or transitive artifacts.
- Work across maintenance lines while preserving each line's JDK, Maven, POM, and repository contract.

### Requires evidence or authorization

- Repository URL/server ID and usable settings must be supplied or discoverable without exposing secrets.
- Publishing or backfilling remote artifacts requires user authorization and exact target coordinates.
- Security, CI, signing, or repository-policy waivers must be explicit and recorded as `skipped`/waived, never passed.

### Out of scope

- Do not repair source compatibility merely to hide a missing remote artifact.
- Do not republish releases or overwrite immutable coordinates unless repository policy and the user explicitly allow it.
- Do not initialize release tooling, change branches, install software, or edit repository configuration without authorization.

## Recovery Loop

1. **Establish the matrix.** Record branch, expected coordinate/version, JDK, Maven, repository/server ID, consumer entry point, and required positive/negative checks. Treat each line independently.
2. **Probe remotely.** Run the existing repository verifier when present. Otherwise create the smallest external consumer that imports the published parent/BOM and resolves representative production dependencies. Give every branch/probe a newly created Maven local repository and use `-U` for SNAPSHOTs.
3. **Classify the first failure.** Separate authentication/metadata transport failures, missing POMs, missing binaries/classifiers, transitive gaps, checksum corruption, and source/build failures. The first unresolved coordinate is evidence, not necessarily the root publication target.
4. **Map provenance.** Prove which source repository, branch, module, packaging, and build output owns the coordinate. Confirm the candidate file's GAV from its effective/raw POM and inspect local or staged contents before upload.
5. **Backfill minimally.** Publish only the missing coordinate and required side artifacts. For `pom` packaging, upload the POM as a POM-only artifact. If `deploy-file` rejects a file inside the local repository, copy the verified artifact set to a fresh staging directory, preserving extensions/classifiers, then upload from there.
6. **Re-probe from another empty cache.** Never reuse the diagnostic cache as acceptance evidence. A new failure deeper in the graph means progress; classify and repeat rather than declaring success.
7. **Close the graph.** Require all configured positive consumers to resolve/build and all negative consumers to fail for the intended obsolete coordinate or policy. Capture remote metadata/timestamp and checksums where available.

Read [references/recovery-playbook.md](references/recovery-playbook.md) before publishing or backfilling. Use [references/evidence-report.md](references/evidence-report.md) for the final status. Consult [references/anti-patterns.md](references/anti-patterns.md) when a recovery attempt is looping or broadening, and [references/faq-deep.md](references/faq-deep.md) for edge cases involving metadata, concurrency, checksums, or toolchain substitutions.

## Non-Negotiable Gates

- Use a distinct local repository path per branch and per acceptance rerun; create it with a safe temporary-directory mechanism.
- Preserve the branch-specific toolchain. Do not make Java 8 lines consume Java 17 output or silently substitute Maven versions.
- Run multi-line reactor deploys serially unless independence is proven. Stop on the first partial failure; partial upload is not publication success.
- Never infer remote completeness from `~/.m2`, reactor output, HTTP 200, or a deploy start.
- Do not log settings contents, credentials, tokens, signed URLs, or secret-bearing command lines.
- Do not delete broad caches. Remove only explicit task-owned temporary repositories when cleanup is requested or safe.
- Distinguish source/test, local build, remote publication, empty-cache consumption, CI, and runtime readiness in every report.

## Decision Rules

| Evidence | Interpretation | Next action |
|---|---|---|
| `401/403` during metadata access | Transport/auth or Maven-repository compatibility | Verify server ID/settings without printing secrets; if artifacts already exist and policy permits, stage and use a compatible Maven `deploy-file` path |
| `Non-resolvable parent POM` | Parent chain incomplete | Verify and backfill the exact parent POM first |
| Missing aggregate module with `pom` packaging | Aggregate graph incomplete | Publish the exact POM-only coordinate |
| Missing JAR/classifier | Production artifact set incomplete | Locate verified build output; publish POM plus matching binary/classifier set |
| Next probe fails on a deeper coordinate | Previous backfill worked | Continue the loop on the newly exposed owner |
| Local build passes, empty-cache probe fails | Remote publication still incomplete | Do not mark complete |

## Information Gaps and Errors

When evidence is missing, continue with safe read-only discovery and report `缺少[具体项]；请提供或授权[补充方式]`. Do not invent coordinates, repository IDs, branch mappings, artifact filenames, or success states. If the same target produces ambiguous candidates, stop before publication and request the exact owner/coordinate decision.

## FAQ

**Can the existing local Maven cache prove publication?** No. It may contain reactor-installed or stale artifacts.

**Should the whole reactor be republished after one missing POM?** Usually no. First prove ownership and backfill the smallest complete coordinate set.

**Is a skipped security workflow a pass?** No. Record it as an explicit residual risk or waiver.

**Can positive consumer checks run in parallel?** Yes when their repositories and output paths are isolated; remote mutations should remain serial unless proven independent.

**What if each repair reveals another missing coordinate?** That is expected graph traversal. Record the sequence and continue until all acceptance consumers close.

**What if `deploy-file` rejects a file under the local repository?** Stage a verified copy in a task-owned temporary directory and upload that copy.
