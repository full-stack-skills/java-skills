# Anti-patterns

## Treating partial evidence as completion

- Bad: “Several artifacts returned HTTP success, so deploy passed.”
- Better: wait for the full reactor exit and remote artifact/metadata checks; otherwise report `in_progress`.

## Reusing the ordinary local cache

- Bad: run the consumer against `~/.m2/repository` and call it clean-cache validation.
- Better: use a newly created isolated `maven.repo.local`, prove downloads came from the target remote, and retain logs.

## Flattening branch contracts

- Bad: build all maintenance lines with the newest installed JDK/Maven and apply one identical patch.
- Better: preserve each branch’s wrapper/JDK/POM contract and enumerate its actual diff and reactor scope.

## Equating local success with release success

- Bad: a green build means Git remotes, Aliyun artifacts, CI, and production are ready.
- Better: report each gate independently with SHAs, remote artifacts, clean-cache consumer output, CI result, and runtime evidence.

## Hiding skipped controls

- Bad: use broad skip flags or `-Denforcer.skip` and report green.
- Better: preserve mandatory gates. If a skip is explicitly authorized, label it `skipped`, name the scope, and record residual risk.

## Destructive “clean cache” validation

- Bad: delete the user’s entire Maven repository.
- Better: create a temporary isolated local repository. Never expose credentials in logs or reports.

## Retrying publication blindly

- Bad: rerun the whole multi-line release after a partial failure without inspecting remote state.
- Better: stop, reconcile uploaded artifacts/metadata and repository overwrite policy, then request authorization for a safe recovery.
