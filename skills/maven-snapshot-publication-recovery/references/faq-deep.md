# Deep FAQ

## Why can a published BOM resolve while a consumer still fails immediately?

The BOM's parent, imported BOM, plugin parent, or an aggregate child POM may still be absent. Maven reveals dependency-graph gaps incrementally.

## Why verify obsolete coordinates negatively?

It prevents an accidental fallback to retired naming from making a positive build appear correct.

## What is the minimum backfill for `packaging=pom`?

The exact POM coordinate and repository metadata produced by the deployment mechanism. Do not attach a fabricated binary.

## When should a whole reactor be redeployed?

Only when the intended publication unit is the full reactor, its outputs are verified, remote policy permits repeated SNAPSHOT deployment, and serial failure handling is in place.

## Can Maven 3 upload artifacts built by Maven 4?

Potentially, when the artifacts are already verified and the incompatibility is confined to the repository transport/metadata path. Record this as a transport recovery, not a rebuild, and validate from a fresh consumer.

## What if remote metadata points to a newer timestamp than the staged artifact?

Do not overwrite blindly. Determine whether another publisher is active and whether the coordinate is concurrently changing; stop if provenance is uncertain.

## How should parallel verification be used?

Parallelize read-only consumers only when each has isolated cache/log/output paths. Keep dependent or overlapping remote uploads serial.

## What if the failure is checksum corruption rather than absence?

Compare staged, uploaded, and downloaded hashes. Follow repository repair policy; do not “fix” it by disabling checksum validation.

## What if a branch succeeds only with `-Denforcer.skip`?

That is diagnostic evidence, not a release or publication pass unless the user explicitly changed the acceptance contract.

## What should be retained after recovery?

Keep the matrix, commands with secrets redacted, logs, backfill ledger, remote timestamps/checksums, and final new-cache reports. Temporary caches/staging may be removed only when safe and desired.
