# Anti-patterns and Corrections

## Treating local success as remote proof

Bad: `mvn install` or a reactor build passed, so publication is complete.

Correct: use a consumer outside the reactor with a brand-new `-Dmaven.repo.local=...` path and the intended remote repository.

## Reusing the diagnostic cache

Bad: rerun the acceptance test against the cache that already downloaded or installed repair candidates.

Correct: create a second unused cache after every backfill round.

## Broad republish without graph evidence

Bad: redeploy every module whenever one aggregate POM is missing.

Correct: identify the exact unresolved coordinate, prove its owner and packaging, then upload the smallest complete artifact set.

## Uploading directly from the local repository

Bad: assume `deploy-file` accepts paths inside `~/.m2/repository` or work around its refusal destructively.

Correct: copy verified files to a fresh task-owned staging directory and compare checksums.

## Flattening maintenance-line toolchains

Bad: run all branches with the newest JDK/Maven because it is installed.

Correct: preserve the tested branch matrix; a toolchain mismatch can create misleading source or bytecode failures.

## Calling partial deploy success

Bad: report success after some reactor modules uploaded before a later module failed.

Correct: stop the serial flow, report the partial remote state, and require fresh-cache closure after recovery.

## Converting a waiver into a pass

Bad: label a skipped Security workflow “green.”

Correct: record `skipped` with authorization, scope, and residual risk while keeping all non-waived gates intact.

## Guessing coordinates or credentials

Bad: infer artifact filenames, server IDs, or repository credentials from conventions.

Correct: read POM/settings metadata safely, redact secrets, and stop when ownership is ambiguous.
