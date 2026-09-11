# FAQ and edge cases

## Can deploy skip tests?

Only when the same source commit already passed the required full test gate and project policy permits it. Record that relationship. Do not silently skip Enforcer, signing, SBOM, license, security, or repository checks.

## Why publish maintenance lines serially?

Concurrent SNAPSHOT deploys can contend on local or remote metadata. Serial order also makes failure attribution and rollback decisions clearer. A repository-specific proven-safe strategy may override this default.

## What proves a Git push?

Successful push output is useful, but the stronger proof is querying the target remote ref and matching its SHA to the intended local commit for every required remote.

## What is a valid clean-cache proof?

A new isolated Maven local repository, resolution from the intended remote, and a consumer build/test that exercises the published contract. Deleting `~/.m2/repository` is unnecessary and unsafe.

## How should SNAPSHOTs be identified?

Record the logical SNAPSHOT version and, where the repository exposes it, the timestamped version/build number resolved by the consumer. This connects downstream evidence to the specific publication.

## Are Maven 4 duplicate-model warnings a release failure?

Not automatically. Classify whether they are owned by the project, affect the effective model, or violate an explicit gate. Reactor/test/consumer success and unresolved model-governance debt must remain separate facts.

## What if one line fails after an earlier line published?

Stop later lines, preserve logs, mark the failed line `failed` and later lines `not_run`. Inspect remote state and retention/overwrite policy before proposing retry, cleanup, or rollback.

## Is `dependency:go-offline` sufficient?

No. It is useful resolution evidence, but a minimal external compile/test or startup path is stronger proof that parent, BOM, dependencies, resources, and runtime adapters work together.

## Does publication prove production readiness?

No. Maven publication, CI acceptance, deployment, configuration readiness, and runtime smoke/acceptance tests are distinct gates.

## How are secrets handled?

Use established settings or secret injection. Never print passwords, access tokens, private repository credentials, or sensitive URLs. Redact them in copied commands and logs.

## What if release inputs are incomplete?

Perform safe read-only discovery and produce a draft matrix. Before mutation, report `缺少[具体项]；请提供或确认[补充方式]` rather than guessing.

## Can the same consumer validate every line?

Only if its compiler/runtime and dependency constraints are compatible with every line. Otherwise use one minimal consumer per toolchain or compatibility contract.
