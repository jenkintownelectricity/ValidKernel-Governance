# canon/

Canonical governance artifacts for ValidKernel Governance.

---

## Purpose

`canon/` stores authoritative command artifacts that serve as the source of truth for governance operations. This directory is distinct from `docs/validkernel/examples/`, which holds examples and demos rather than canonical source-of-truth commands.

## Structure

```
canon/
  commands/    — canonical command documents
  install/     — install-related canonical artifacts
  patterns/    — reusable governance patterns
```

### commands/

Stores canonical command documents. These are authoritative governance commands that have been formalized beyond draft or example status.

### install/

Stores install-related canonical artifacts, such as canonical install commands, install configuration templates, and deployment specifications.

### patterns/

Stores reusable governance patterns — structural templates and conventions that can be applied across multiple commands or repositories.

---

## Relationship to Other Directories

| Directory | Purpose |
|-----------|---------|
| `canon/` | Authoritative governance artifacts (source of truth) |
| `docs/validkernel/` | Governance specifications and documentation |
| `docs/validkernel/examples/` | Examples and demos (not source of truth) |
| `.validkernel/tools/` | Runtime governance tooling |
| `.validkernel/state/` | Live governance state |
| `.validkernel/receipts/` | Execution history |
| `.validkernel/registry/` | Command registry |
