# ValidKernel Governance

ValidKernel Governance (VKG) is a deterministic governance system in which commands are validated by a Runtime Gate, executed within defined capability bounds, recorded through receipts, and indexed in the Command Registry to produce an auditable execution history.

VKG governs **Governed Environments** — systems in which commands may be issued, validated, executed, recorded, and verified under VKG governance rules. Git repositories are the reference implementation of VKG in version 0.1.

VKG provides a structured command protocol, a pre-execution runtime gate, execution receipts, a command registry, validation tooling, and CI enforcement so governed work can be tracked and verified consistently.

## How VKG works

Every governed action follows the canonical VKG governance loop:

```
Authority → Command → Runtime Gate → Execution → Receipt → Command Registry → Verification
```

1. An **Authority** issues a structured **Command** describing what must be done, what must not be touched, and how to verify success.
2. The **Runtime Gate** validates the command before any work begins. If validation fails, execution is blocked.
3. An execution agent performs the work within defined **Capability Bounds**.
4. A **Receipt** is created recording what happened — the command identity, files changed, branch, commit, and outcome.
5. The **Command Registry** is updated to reflect the execution result.
6. **Verification** confirms that the receipt and registry are consistent and that governance rules were followed.

No governed action may execute without passing the Runtime Gate. VKG operates under **FAIL_CLOSED** semantics: if validation fails or system state is uncertain, execution must not proceed.

## VKG governance loop diagram

```
┌───────────┐     ┌─────────┐     ┌──────────────┐     ┌───────────┐
│ Authority │────>│ Command │────>│ Runtime Gate │────>│ Execution │
└───────────┘     └─────────┘     └──────┬───────┘     └─────┬─────┘
                                         │                     │
                                    FAIL │                PASS │
                                         │                     │
                                         v                     v
                                    ┌─────────┐         ┌─────────┐
                                    │ BLOCKED │         │ Receipt │
                                    └─────────┘         └────┬────┘
                                                             │
                                                             v
                                                   ┌─────────────────┐
                                                   │Command Registry │
                                                   └────────┬────────┘
                                                            │
                                                            v
                                                   ┌──────────────┐
                                                   │ Verification │
                                                   └──────────────┘
```

## Command ring structure

VKG commands use a layered ring structure that separates governance concerns:

```
┌─────────────────────────────────────────┐
│  L0 — Governance Context                │
│  (Authority, ID, Date, Risk Class,      │
│   Scope, Command Format)                │
│  ┌───────────────────────────────────┐  │
│  │  L1 — Mission Directive           │  │
│  │  (Objective, Required Outcomes,   │  │
│  │   Constraints, Non-goals)         │  │
│  │  ┌─────────────────────────────┐  │  │
│  │  │  L2 — Deterministic         │  │  │
│  │  │       Commit Gate           │  │  │
│  │  │  (Validation Checklist,     │  │  │
│  │  │   Gate Rule)                │  │  │
│  │  │  ┌───────────────────────┐  │  │  │
│  │  │  │  L3 — Capability      │  │  │  │
│  │  │  │       Bound           │  │  │  │
│  │  │  │  (TOUCH-ALLOWED,      │  │  │  │
│  │  │  │   NO-TOUCH,           │  │  │  │
│  │  │  │   ENFORCEMENT MODE)   │  │  │  │
│  │  │  └───────────────────────┘  │  │  │
│  │  └─────────────────────────────┘  │  │
│  └───────────────────────────────────┘  │
└─────────────────────────────────────────┘
```

| Ring | Name | Purpose |
|------|------|---------|
| L0 | Governance Context | Identifies authority, command ID, date, risk class, and scope |
| L1 | Mission Directive | Defines objective, required outcomes, constraints, and non-goals |
| L2 | Deterministic Commit Gate | Pass/fail checklist that must all be satisfied before completion |
| L3 | Capability Bound | Hard limits on what the executor may and may not modify |

## Runtime flow

The runtime flow describes the internal execution sequence:

```
Command → Runtime Gate → Execution → Receipt → Registry Update
```

Each stage is mandatory. Skipping a stage is a governance violation. If any stage fails, the flow halts.

## Receipt and Command Registry relationship

```
┌───────────────────┐         ┌──────────────────────────┐
│     Receipt       │         │    Command Registry      │
│                   │         │                          │
│  command_id  ─────┼────────>│  command_id              │
│  status           │         │  status                  │
│  gate_result      │         │  gate_result             │
│  branch           │         │  branch                  │
│  commit_hash      │         │  commit_hash             │
│  files_changed    │         │  receipt_path ───────────┼──> .validkernel/receipts/
│  summary          │         │  receipt_status          │
│  blocked_reason   │         │  supersedes              │
│  next_action      │         │  superseded_by           │
└───────────────────┘         │  next_action             │
                              └──────────────────────────┘

The registry indexes receipts but does not duplicate them.
Receipt contains the detailed execution record.
Registry contains the summary state and links to receipts.
```

## Governed Environments

A **Governed Environment** is a system in which commands may be issued, validated, executed, recorded, and verified under VKG governance rules.

Examples of Governed Environments include:

- Git repositories
- CI/CD pipelines
- infrastructure management systems
- databases
- AI execution environments

Git repositories are the reference implementation of VKG in version 0.1.

## Command example

The following is a complete command using the ValidKernel Command Protocol structure:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
L0 — GOVERNANCE CONTEXT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Authority:     Armand Lefebvre, L0 — Lefebvre Design Solutions LLC
Document ID:   L0-CMD-2026-0306-001
Date:          2026-03-06
Risk Class:    Risk Class 1 (RC1)
Scope:         Update project documentation to improve clarity and consistency.
Command Format: ValidKernel Command Protocol v0.1

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
L1 — MISSION DIRECTIVE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Objective:
Revise the project README and specification documents to ensure
terminology and formatting are consistent across all files.

Required Outcomes:
1. README reflects current project structure
2. All specification files use consistent terminology
3. No behavioral changes to runtime tooling

Constraints:
- Do not modify runtime code or tooling scripts
- Do not alter CI workflow behavior

Non-goals:
- No new governance tools
- No changes to command protocol semantics

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
L2 — DETERMINISTIC COMMIT GATE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Validation Checklist:
[ ] README updated with current structure
[ ] specification terminology is consistent
[ ] no runtime files modified
[ ] no CI workflow changes
[ ] commit created

Gate Rule:
If any item fails → HALT and report exact blocker.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
L3 — CAPABILITY BOUND
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TOUCH-ALLOWED:
- README.md
- docs/validkernel/

NO-TOUCH:
- .validkernel/tools/
- .github/workflows/
- application runtime code

ENFORCEMENT MODE: FAIL_CLOSED

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
END COMMAND
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## What this repository contains

This repository is the standalone source-of-truth kernel for ValidKernel Governance.

### Governance specifications

Located in `docs/validkernel/`:

- `vkg-spec.md` — VKG specification
- `command-protocol.md` — command protocol definition
- `command-execution-flow.md` — runtime execution flow
- `command-receipts.md` — receipt specification
- `command-registry.md` — registry specification
- `governance-tools.md` — tooling documentation
- `glossary.md` — canonical terminology definitions
- `risk-classes.md` — risk class definitions

### Governance runtime

Located in `.validkernel/`:

- `authority/` — portable authority identity
- `registry/` — active command registry and legacy registry history
- `receipts/` — command execution receipts
- `tools/` — governance tooling
- `state/` — live governance state

### Canonical governance artifacts

Located in `canon/`:

- `commands/` — canonical command documents
- `install/` — install-related canonical artifacts
- `patterns/` — reusable governance patterns

### Governance CI

Located in `.github/workflows/vkg-governance-check.yml`

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
Validates command structure before execution. The Runtime Gate is the first checkpoint in the VKG execution flow.

### `validate-receipt.py`
Validates a single receipt against the receipt model and registry consistency rules.

### `validate-all-receipts.py`
Validates all receipts in the repository and checks registry consistency.

### `update-governance-record.py`
Creates or updates command receipts and registry entries.

### `install-vkg.py`
Installs the ValidKernel governance kernel into another repository.

### `verify-governance-docs.py`
Verifies that governance documentation satisfies the Ring 2 deterministic checkpoint. Runs automatically in CI when governance-critical files change. Can also be run locally.

## Continuous Governance Gate

Changes to governance-critical documentation are automatically checked in CI before merge. The Continuous Governance Gate detects when governance-critical files are modified and runs a deterministic Ring 2 verification against the documentation set.

- Governance-critical changes are automatically detected
- Failed governance checks block merge
- Documentation changes can be governance-critical
- The gate is deterministic and evidence-based

Run the gate locally:

```bash
python .validkernel/tools/verify-governance-docs.py
```

See `docs/validkernel/governance-tools.md` for the full list of governance-critical paths and checklist items.

## Portable authority

Portable authority is defined in:

`.validkernel/authority/authority.json`

This allows the same governance identity to operate across multiple Governed Environments.

## Registry and receipts

### Active registry

`.validkernel/registry/command-registry.json`

### Legacy imported registry history

`.validkernel/registry/command-registry.legacy-gpc-shopdrawings.json`

### Receipts

`.validkernel/receipts/`

## Example usage

Validate a command:

```bash
python .validkernel/tools/runtime-gate.py docs/validkernel/examples/sample-command.md
```

Install governance into another repository:

```bash
python .validkernel/tools/install-vkg.py --target-repo /path/to/SomeRepo --initialize-authority --initialize-registry
```

Validate all receipts:

```bash
python .validkernel/tools/validate-all-receipts.py
```

## Command Governance

This repository uses the **ValidKernel Command Protocol v0.1** for AI/human execution governance. All commands follow the structured format defined in: `docs/validkernel/command-protocol.md`

## License

Proprietary — Lefebvre Design Solutions LLC. All rights reserved.
