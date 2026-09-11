# Recovery Playbook

Read this reference only when the task may mutate a Maven repository.

## 1. Build the publication matrix

For every line, capture:

| Field | Required evidence |
|---|---|
| Source | repository root, branch/ref, clean/dirty status, commit SHA |
| Coordinate | groupId, artifactId, version, packaging, classifier if any |
| Toolchain | exact Java and Maven versions used by that line |
| Repository | URL, server ID, snapshot policy; never print credentials |
| Consumer | verifier/script or minimal external POM and expected behavior |
| Gates | local build/test, publication, fresh-cache resolution, CI/security/runtime status |

Do not merge rows merely because versions look similar.

## 2. Construct a trustworthy probe

Prefer a maintained repository verifier. Inspect its inputs and confirm it really:

- creates or accepts a unique Maven local repository;
- targets the intended remote repository rather than reactor/local output;
- selects the correct JDK/Maven pair;
- forces SNAPSHOT refresh with `-U`;
- imports the parent/BOM and resolves representative production artifacts;
- writes branch-specific logs/reports;
- optionally checks that retired coordinates remain unavailable.

If no verifier exists, create a temporary consumer outside the producer reactor. Keep it minimal and parameterize the repository URL, server ID, GAV, and local repository path. Do not bake credentials into files.

## 3. Read failures as a dependency graph

Normalize the first decisive error into one of these records:

```text
kind: auth | metadata | missing-pom | missing-binary | checksum | build | unknown
coordinate: groupId:artifactId:packaging[:classifier]:version
requested_by: direct consumer | parent | BOM | module | transitive dependency
source_owner: repository / branch / module, or unresolved
evidence: log path and concise error excerpt
```

Parent and BOM failures prevent Maven from revealing deeper gaps. After each repair, expect the graph frontier to move.

## 4. Validate the candidate before upload

For every proposed backfill:

1. Resolve the source owner and branch.
2. Confirm raw/effective POM GAV and packaging.
3. Confirm the file was produced by the intended commit/toolchain, not an older local install.
4. For a JAR, inspect archive integrity and pair it with its POM; include required sources/Javadoc/classifiers only when repository policy or the release contract requires them.
5. For an aggregate module, verify `packaging=pom`; do not fabricate an empty JAR.
6. Record checksums before staging and compare after copying.

If provenance cannot be established, stop before mutation.

## 5. Choose the narrow recovery action

Use the normal reactor deploy only when the reactor is known complete, the repository path is compatible, and republishing the entire line is intended. Otherwise prefer a targeted upload of already verified outputs.

When a compatible Maven `deploy-file` path is required:

- use the same repository URL and matching server ID from settings;
- keep settings and credentials out of logs;
- copy files from the local repository/build output into a new staging directory when the deploy plugin refuses local-repository paths;
- preserve packaging, extension, classifier, and generated POM behavior explicitly;
- serialize uploads and fail fast;
- never call an upload successful until a fresh remote consumer resolves it.

## 6. Acceptance rerun

Create a second, unused Maven local repository. Run the complete branch matrix with the original toolchain mapping. Record:

- positive consumer result;
- expected negative-coordinate result, if configured;
- deepest resolved artifact set or representative modules;
- remote metadata timestamp and SHA-256 where accessible;
- log/report paths and command exit status.

If a line is blocked by a newly exposed upstream coordinate, mark that line `BLOCKED: remote graph incomplete`, not failed source compatibility and not complete.

## Stop conditions

Stop and ask for direction when:

- remote publication authorization is absent;
- coordinate ownership or provenance is ambiguous;
- recovery would overwrite a release or immutable artifact;
- settings/server ID cannot be matched safely;
- repository policy requires signing or approvals not available;
- the requested waiver changes an existing acceptance criterion.

Stop successfully only when every in-scope line passes a new-cache consumer check and residual skipped gates are explicitly reported.
