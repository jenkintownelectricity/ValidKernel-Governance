# ValidKernel Governance

ValidKernel Governance (VKG) is a deterministic governance layer for AI-assisted and human-assisted repository execution.

It provides a structured command protocol, a pre-execution runtime gate, execution receipts, a command registry, validation tooling, and CI enforcement so governed work can be tracked and verified consistently.

## Core execution loop

command
→ runtime gate
→ execution
→ receipt
→ registry
→ validator
→ CI

## What this repository contains

This repository is the standalone source-of-truth kernel for ValidKernel Governance.

### Governance specifications

Located in:

`docs/validkernel/`

Includes:

- `vkg-spec.md`
- `command-protocol.md`
- `command-execution-flow.md`
- `command-receipts.md`
- `command-registry.md`
- `governance-tools.md`

### Governance runtime

Located in:

`.validkernel/`

Includes:

- `authority/` — portable authority identity
- `registry/` — active command registry and legacy registry history
- `receipts/` — command execution receipts
- `tools/` — governance tooling
- `state/` — live governance state

### Canonical governance artifacts

Located in:

`canon/`

Includes:

- `commands/` — canonical command documents
- `install/` — install-related canonical artifacts
- `patterns/` — reusable governance patterns

### Governance CI

Located in:

`.github/workflows/vkg-governance-check.yml`

This workflow validates governance state on push and pull request.

## Repository structure

| Directory | Purpose |
|-----------|---------|
| `docs/validkernel/` | Governance specifications and documentation |
| `canon/` | Canonical governance artifacts (authoritative commands, patterns) |
| `.validkernel/tools/` | Runtime governance kernel (gate, validator, installer) |
| `.validkernel/state/` | Live governance state (repo-state, runtime-state) |
| `.validkernel/registry/` | Command registry |
| `.validkernel/receipts/` | Execution history |
| `.validkernel/authority/` | Portable authority identity |

## Included tools

### `runtime-gate.py`
Validates command structure before execution.

### `validate-receipt.py`
Validates a single receipt against the receipt model and registry consistency rules.

### `validate-all-receipts.py`
Validates all receipts in the repository and checks registry consistency.

### `update-governance-record.py`
Creates or updates command receipts and registry entries.

### `install-vkg.py`
Installs the ValidKernel governance kernel into another repository.

## Portable authority

Portable authority is defined in:

`.validkernel/authority/authority.json`

This allows the same governance identity to operate across multiple governed repositories.

## Registry and receipts

### Active registry

`.validkernel/registry/command-registry.json`

### Legacy imported registry history

`.validkernel/registry/command-registry.legacy-gpc-shopdrawings.json`

### Receipts

`.validkernel/receipts/`

## Example usage

Validate a command:

```powershell
python .validkernel\tools\runtime-gate.py docs\validkernel\examples\sample-command.md
```

Install governance into another repository:

```powershell
python .validkernel\tools\install-vkg.py --target-repo D:\repos\SomeRepo --initialize-authority --initialize-registry
```

Validate all receipts:

```powershell
python .validkernel\tools\validate-all-receipts.py
```

## License

Proprietary — Lefebvre Design Solutions LLC. All rights reserved.
