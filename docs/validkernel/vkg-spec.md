# ValidKernel Governance (VKG)

**Specification v0.1**

*A deterministic governance framework for coordinating human authority, AI execution, and verifiable system change.*

---

## 1. Overview

ValidKernel Governance (VKG) is a structured governance framework designed to control AI-assisted and human-assisted system execution through deterministic commands and verifiable outcomes.

VKG provides a governance layer that ensures:

- Authority traceability
- Bounded AI execution
- Deterministic commit gates
- Auditable execution records
- Reproducible repository state
- Portable governance identity
- Runtime command enforcement

VKG replaces unstructured prompts or instructions with governed commands that produce verifiable receipts and are indexed in a canonical registry.

---

## 2. Core Governance Model

VKG is built around a six-stage governance lifecycle.

```
Authority
   ↓
Instruction
   ↓
Runtime Gate
   ↓
Execution
   ↓
Receipt
   ↓
Verification
```

Each stage is represented by a VKG component.

The Runtime Gate ensures commands are validated and permitted before execution occurs.

---

## 3. VKG Components

### 3.1 Authority Layer

The Authority Layer defines the trusted source of commands.

Authority is defined by L0 governance context.

Example:

```
Authority: Armand Lefebvre
Organization: Lefebvre Design Solutions LLC
```

The authority layer establishes:

- Command ownership
- Governance responsibility
- Root trust for the repository

### 3.2 Portable Authority

Portable Authority extends the Authority Layer by allowing governance identity to be portable across repositories, systems, and organizations.

Instead of authority being tied to a single repository, VKG defines a Portable Authority Identity that can issue commands across multiple governed environments.

Portable Authority enables:

- Cross-repository governance
- Multi-system authority delegation
- Portable governance identities
- Distributed command issuance

A Portable Authority identity may include:

- `authority_id`
- `authority_name`
- `organization`
- `contact`
- `governance_scope`
- `authority_version`

Example:

```
authority_id: VKG-AUTH-001
authority_name: Armand Lefebvre
organization: Lefebvre Design Solutions LLC
governance_scope: construction-ai-systems
```

Portable Authority identities may be stored in:

```
.validkernel/authority/
```

Example file:

```
.validkernel/authority/authority.json
```

Portable Authority allows VKG systems to recognize the same governance authority across:

- Multiple repositories
- Distributed infrastructure
- CI/CD systems
- External partner systems

Future versions may support cryptographically signed authority identities.

### 3.3 Command Protocol

Commands are structured instructions issued under the ValidKernel Command Protocol.

Each command contains deterministic sections:

```
L0 Governance Context
Ring 1 — Mission Directive
Ring 2 — Deterministic Commit Gate
Ring 3 — Capability Bound
Execution Notes (optional)
End Command
```

Commands define:

- Intent
- Constraints
- Validation gates
- Execution permissions

Commands are fail-closed by design.

### 3.4 ValidKernel Runtime Gate (VKRT)

The ValidKernel Runtime Gate (VKRT) is the enforcement layer that evaluates commands before execution.

VKRT ensures that commands satisfy governance rules and are authorized to run.

The Runtime Gate performs:

- Authority verification
- Command structure validation
- Capability boundary checks
- Commit gate preconditions
- Repository safety checks

If any rule fails, execution is blocked.

Example VKRT evaluation:

```
if authority_valid
   and command_structure_valid
   and capability_bounds_respected
   and runtime_environment_safe
then
   execution_allowed
else
   execution_blocked
```

When execution is blocked:

```
status = BLOCKED
gate_result = FAIL
blocked_reason = VKRT rejection
```

VKRT may operate in:

- AI agents
- CI/CD systems
- Repository automation tools
- Governance orchestration systems

VKRT ensures that commands cannot execute outside governance rules.

### 3.5 Execution Layer

Execution occurs when a command passes through the Runtime Gate and is interpreted by an execution agent.

Execution agents may include:

- Humans
- AI systems
- Automated tooling
- CI pipelines

Execution must respect the Capability Bound defined in Ring 3.

### 3.6 Command Receipts

Every command execution produces a Command Receipt.

Receipts record:

- Command identity
- Authority
- Execution timestamp
- Branch and commit
- Files changed
- Gate result
- Execution status

Receipt storage location:

```
.validkernel/receipts/
```

Example receipt file:

```
.validkernel/receipts/L0-CMD-EXAMPLE-001.receipt.json
```

Receipts provide a verifiable record of governance execution.

### 3.7 Command Registry

The Command Registry is the canonical index of all commands and their lifecycle state.

Registry location:

```
.validkernel/registry/command-registry.json
```

The registry tracks:

- Issued commands
- Execution state
- Receipt linkage
- Supersession relationships
- Next actions

This enables governance queries such as:

- What commands exist?
- Which commands completed?
- Which commands were blocked?
- Which commands supersede earlier work?

---

## 4. Governance Rings

VKG uses a ring model to separate responsibilities.

| Ring | Role | Trust Level |
|------|------|-------------|
| L0 | Human authority | Root |
| L1 | Governance kernel | Trusted |
| L2 | Execution agents | Untrusted |

**Responsibilities:**

- **L0** — Issues commands and defines authority.
- **L1** — Defines governance rules, runtime gates, and validation policies.
- **L2** — Performs execution within defined capability bounds.

Portable Authority allows L0 identity to persist across multiple VKG environments.

---

## 5. Deterministic Commit Gates

Commit Gates enforce success criteria before a command can be declared complete.

Example gate:

```
Validation Checklist
 [ ] schema normalization implemented
 [ ] validation gates enforced
 [ ] audit logging operational
 [ ] repository state clean
```

If any gate fails:

```
execution_result = BLOCKED
```

This prevents premature completion.

---

## 6. Capability Boundaries

Capability boundaries restrict what execution agents can modify.

Two categories exist:

- **TOUCH-ALLOWED**
- **NO-TOUCH**

Example:

```
TOUCH-ALLOWED
- documentation
- governance files

NO-TOUCH
- application runtime code
- deployment infrastructure
```

This protects system stability.

---

## 7. Command Lifecycle

Commands move through defined lifecycle states.

```
ISSUED
   ↓
IN_PROGRESS
   ↓
EXECUTED
```

Alternative paths:

- `BLOCKED`
- `PARTIAL`
- `SUPERSEDED`
- `ARCHIVED`

The lifecycle state is recorded in:

- Command receipts
- Command registry

---

## 8. Governance File Structure

A VKG-enabled repository typically contains:

```
docs/
  validkernel/
    vkg-spec.md
    command-protocol.md
    command-receipts.md
    command-registry.md
    templates/

.validkernel/
  authority/
  receipts/
  registry/
```

This structure separates governance metadata from application code.

---

## 9. Repository Governance Rules

Repositories using VKG should follow these principles:

1. Commands must follow the ValidKernel Command Protocol.
2. Commands must pass the ValidKernel Runtime Gate (VKRT) before execution.
3. Execution results should produce a Command Receipt.
4. Commands should be indexed in the Command Registry.
5. Receipts should reference the commit hash representing the executed state.
6. Commands that replace earlier commands should mark them `SUPERSEDED`.
7. Portable Authority identity should be defined in:

```
.validkernel/authority/authority.json
```

---

## 10. Failure Handling

When execution cannot complete, the execution agent must produce:

```
status = BLOCKED
gate_result = FAIL
blocked_reason = explanation
next_action = remediation
```

The receipt must reflect the failure state.

---

## 11. Verification

Verification confirms that execution followed governance rules.

Verification may include:

- Commit inspection
- Receipt validation
- Registry reconciliation
- Authority identity verification
- Runtime gate audit

Future tooling may automate verification.

---

## 12. Design Principles

VKG is designed around several principles.

**Determinism** — Execution outcomes must be predictable.

**Auditability** — Every command produces traceable evidence.

**Authority Traceability** — Command authority must be explicit and portable.

**Runtime Safety** — Commands must pass runtime enforcement before execution.

**AI Safety** — AI agents operate under bounded capability rules.

**Simplicity** — Governance structures remain human-readable.

---

## 13. Future Extensions

Future VKG versions may introduce:

- Cryptographic command signing
- Automated receipt validation
- Distributed command authorities
- Portable governance identities
- Cross-repository governance
- Authority verification systems
- Automated runtime gate engines

These additions will extend VKG without breaking compatibility with v0.1.

---

## 14. Versioning

This specification defines:

> **ValidKernel Governance (VKG) v0.1**

Future revisions should maintain backward compatibility where possible.

---

## 15. Relationship to ValidKernel

VKG functions as the governance layer of ValidKernel systems.

- **ValidKernel** defines the deterministic system kernel.
- **VKG** defines the governance protocol controlling that kernel.
- **VKRT** enforces governance decisions before execution occurs.

Together they form a system capable of managing complex AI-assisted development while maintaining deterministic authority and auditability.

---

### Repository Placement

This specification should be stored at:

```
docs/validkernel/vkg-spec.md
```

README example:

> This repository implements ValidKernel Governance (VKG) v0.1.

---

*ValidKernel Governance (VKG) v0.1 — Lefebvre Design Solutions LLC*
