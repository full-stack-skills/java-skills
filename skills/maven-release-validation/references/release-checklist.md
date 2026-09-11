# Release checklist

Use this checklist per release line. Replace examples with repository-native commands; do not copy commands blindly.

## 1. Baseline and contract

- [ ] Resolve repository root and all relevant worktrees.
- [ ] Record branch, local HEAD, upstream/tracking ref, dirty/untracked files, and exact release diff.
- [ ] Read repository instructions, wrapper config, root/parent POMs, distribution management, settings/profile requirements, and existing release scripts.
- [ ] Record required JDK/Maven versions and confirm the active runtime.
- [ ] Record coordinates/version and expected reactor/module scope.
- [ ] Identify required gates and any explicitly authorized skips.

## 2. Source verification

- [ ] Run the project’s required full verification under the line’s real toolchain.
- [ ] Record command, start/end time, exit code, log path, reactor summary, and test totals.
- [ ] Confirm failures/errors/skips match policy.
- [ ] Exercise changed packaging/resources/adapters, not only the aggregate build.
- [ ] Classify warnings: release-blocking, accepted compatibility debt, or informational. Keep Maven 4 model diagnostics distinct from successful compilation/tests.

## 3. Git synchronization

- [ ] Enumerate exact files included in each line; do not assume identical patches across branches.
- [ ] When authorized, commit each branch separately.
- [ ] Push every required remote.
- [ ] Query each remote ref and prove it equals the intended local commit.
- [ ] Keep local, tracking, GitHub, Codeup, or other remote SHAs in the evidence table.

## 4. Repository publication

- [ ] Publish serially across maintenance lines unless proven safe otherwise.
- [ ] Use the correct wrapper, JDK, settings/profile, and repository ID.
- [ ] Preserve required Enforcer/signing/SBOM/license/security gates.
- [ ] Record complete reactor result; do not accept “upload started” or a subset of modules.
- [ ] Verify expected artifact set and metadata remotely.
- [ ] For SNAPSHOTs, record resolved timestamp/build number if available.

## 5. Isolated downstream validation

- [ ] Create a fresh temporary local repository, for example `repo_dir=$(mktemp -d)` and pass `-Dmaven.repo.local="$repo_dir"`.
- [ ] Ensure the consumer does not inherit producer reactor outputs or the normal local cache.
- [ ] Resolve the parent POM and import the BOM where applicable.
- [ ] Resolve representative POM/JAR artifacts, including every changed public module.
- [ ] Run `dependency:go-offline` or equivalent resolution proof when useful.
- [ ] Compile/test a minimal external consumer; for framework adapters, run the smallest meaningful startup/integration test.
- [ ] Capture the remote repository source and SNAPSHOT timestamp/build resolved.

## 6. Evidence table

| Line | Source verify | Git remotes | Publication | Clean-cache consumer | CI | Runtime | Notes |
|---|---|---|---|---|---|---|---|
| `<branch / JDK / Maven>` | status + modules/tests | status + SHAs | status + version/timestamp | status + consumer/log | status | status | skips/risks |

Allowed statuses: `passed`, `failed`, `in_progress`, `not_run`, `skipped`.

## 7. Completion rule

A Maven release is release-green only when every required gate is `passed`, or an explicitly non-required gate is transparently `skipped` with residual risk. Publication-green does not imply CI-green or runtime-green.
