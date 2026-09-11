# Evidence Report Template

Use one row per maintenance line and keep evidence tiers separate.

## Summary

```text
Recovery target:
Authorized mutation scope:
Repository/server ID:
Start state:
Final state:
```

## Matrix

| Branch | Expected GAV | JDK/Maven | Initial empty-cache failure | Backfill performed | Final new-cache result |
|---|---|---|---|---|---|

## Backfill ledger

For each coordinate:

```text
coordinate:
packaging/classifier:
source repository / branch / SHA:
source file and pre-stage checksum:
staged file and checksum:
upload tool/result:
remote metadata/timestamp:
fresh-cache proof:
```

## Gate status

Report each as `passed`, `failed`, `blocked`, `skipped`, or `not run`:

- source and focused tests
- local reactor build
- remote publication
- fresh-cache positive consumer
- negative/retired-coordinate consumer
- CI
- security/signing
- runtime acceptance

Never collapse these into one “green” status. State remaining risks and exact next action for every non-passed gate.
