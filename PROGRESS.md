# ShopDrawing.AI -- Progress Tracker

**Last Updated:** 2026-03-06 (Final Hardening)
**Live URL:** https://shopdrawings.ai

---

## Command Governance

This repository uses the **ValidKernel Command Protocol v0.1** for AI/human execution governance. All commands follow the structured format defined in: `docs/validkernel/command-protocol.md`

Command executions produce **ValidKernel Command Receipts v0.1** -- structured JSON records stored in `.validkernel/receipts/`. Receipt spec: `docs/validkernel/command-receipts.md`

All commands are indexed in the **ValidKernel Command Registry v0.1** at `.validkernel/registry/command-registry.json`. Registry spec: `docs/validkernel/command-registry.md`

Governance framework: **VKG Specification v0.1** at `docs/validkernel/vkg-spec.md`. Execution flow: `docs/validkernel/command-execution-flow.md`. Portable Authority: `.validkernel/authority/authority.json`.

VKG operational tools: `.validkernel/tools/validate-receipt.py`, `.validkernel/tools/update-governance-record.py`, `.validkernel/tools/validate-all-receipts.py`. See `docs/validkernel/governance-tools.md`.

VKRT is executable: `.validkernel/tools/runtime-gate.py` — validates command structure before execution.

VKG governance is CI-enforced: `.github/workflows/vkg-governance-check.yml` validates receipts and registry on every push and pull request.

VKG Installer: `.validkernel/tools/install-vkg.py` deploys the governance kernel into target repositories (ShopDrawing.AI, construction_dna, SUPA-SAINT, etc.).

---

## Command Registry (24 Commands)

| # | Command ID | Description | Status | Key Commit |
|---|------------|-------------|--------|------------|
| 1 | L0-CMD-2026-0208-002 | Shop Drawing Production System (parent) | In Progress | -- |
| 2 | L0-CMD-2026-0209-001 | ezdxf Pipeline | COMPLETE | -- |
| 3 | L0-CMD-2026-0209-002 | SaaS Frontend (25 chunks) | COMPLETE | 03e263d |
| 4 | L0-CMD-2026-0209-003 | Backend API (12 modules, 41 endpoints) | COMPLETE | 096c8ca |
| 5 | L0-CMD-2026-0209-003-A1 | DXF Viewer (client-side) | COMPLETE | -- |
| 6 | L0-CMD-2026-0209-004 | Clerk Auth Integration | COMPLETE | -- |
| 7 | L0-CMD-2026-0209-005 | CORS + Clerk Hotfix | COMPLETE | -- |
| 8 | L0-CMD-2026-0209-006 | CORS 500 Deep Fix | COMPLETE | -- |
| 9 | L0-CMD-2026-0209-007 | Documentation Update | COMPLETE | -- |
| 10 | L0-CMD-2026-0210-002 | Full System Audit (40/40 PASS) | COMPLETE | 267f817 |
| 11 | L0-CMD-2026-0210-003 | Wire DXF Pipeline | COMPLETE | 7bd66cd |
| 12 | L0-CMD-2026-0210-004 | UX Hardening (7 phases) | COMPLETE | 0fe2b56 |
| 13 | L0-CMD-2026-0210-005 | Alpha Documentation Update | COMPLETE | aff587b |
| 14 | L0-CMD-2026-0218-011 P1 | CAD Editor Phase 1: Canvas + layers | COMPLETE | 961bb3a |
| 15 | L0-CMD-2026-0218-011 P2 | CAD Editor Phase 2: 11 tools + snap | COMPLETE | 18834ac |
| 16 | L0-CMD-SHOPDRAWINGS-FINAL-HARDENING-001 | End-to-end hardening: validation gates, normalization, audit | COMPLETE | 4cfab0c |
| 17 | L0-CMD-VALIDKERNEL-PROTOCOL-INTEGRATION-001 | ValidKernel Command Protocol v0.1 integration | COMPLETE | 11da976 |
| 18 | L0-CMD-VALIDKERNEL-RECEIPTS-001 | ValidKernel Command Receipts v0.1 integration | COMPLETE | 905e8bd |
| 19 | L0-CMD-VALIDKERNEL-REGISTRY-001 | ValidKernel Command Registry v0.1 integration | COMPLETE | b501792 |
| 20 | L0-CMD-VKG-SPEC-001 | ValidKernel Governance Specification v0.1 | COMPLETE | 8d53d59 |
| 21 | L0-CMD-VKG-CLOSE-LOOP-001 | Close VKG loop: execution flow, authority, registry | COMPLETE | 32fef99 |
| 22 | L0-CMD-VKG-TOOLS-001 | VKG operational tooling: receipt validator + record updater | COMPLETE | cd0b0be |
| 23 | L0-CMD-VKG-CI-ENFORCEMENT-001 | CI enforcement for VKG receipt and registry validation | COMPLETE | 0402813 |
| 24 | L0-CMD-VKRT-EXECUTABLE-001 | Executable VKRT for pre-execution command validation | COMPLETE | 24a41a9 |
| 25 | L0-CMD-VKG-INSTALLER-001 | VKG installer for deterministic kernel deployment | COMPLETE | — |

---

## L0-CMD-SHOPDRAWINGS-FINAL-HARDENING-001 Breakdown

| Subtask | Description | Status | Commit |
|---------|-------------|--------|--------|
| A | Canonical Data Lock — `normalizer.py` (raw AI → schema enums) | COMPLETE | 4cfab0c |
| B | Validation gate on DXF generation | COMPLETE | 4cfab0c |
| C | Validation gate on delivery packaging | COMPLETE | 4cfab0c |
| D | ValidationPanel + DeliveryPanel wired to real APIs | COMPLETE | 4cfab0c |
| E | Audit event capture — structured `[AUDIT]` logging | COMPLETE | 4cfab0c |
| F | Workflow step persistence (localStorage) | COMPLETE | 4cfab0c |
| G | Frontend state tracking (validation_overall, dxf_path) | COMPLETE | 4cfab0c |

### Hardening Summary
- Added canonical normalization layer for AI analysis output
- Enforced validation gates on DXF generation and delivery packaging
- Replaced simulated validation/delivery UI behavior with live API calls
- Added structured audit logging for critical-path actions
- Added workflow step persistence and enriched detail serialization
- Implementation complete; end-to-end golden-path runtime verification still pending.

---

## L0-CMD-2026-0218-011 Phase Breakdown (CAD Editor)

| Phase | Description | Status | Commit |
|-------|-------------|--------|--------|
| Phase 1 | Canvas shell, 13 GPC layers + BASE-ARCH, zoom/pan, grid, snap | COMPLETE | 961bb3a |
| Phase 2 | 11 drawing tools, snap engine (4 modes), selection, undo/redo, properties | COMPLETE | 18834ac |

### Phase 2 Tool Status (AMD-002 Verified)

| # | Tool | Status | Notes |
|---|------|--------|-------|
| 01 | SELECT | PASS | Click, Shift+toggle, L→R window, R→L crossing, blue handles |
| 02 | LINE | PASS | Click-click, chaining, ortho support |
| 03 | POLYLINE | PASS | Click points, dblclick closes, Enter finishes open |
| 04 | ARC | PASS | 3-point (start/mid/end), circumcenter calculation |
| 05 | RECTANGLE | PASS | 2-click corners |
| 06 | CIRCLE | PASS | 2-click center/radius |
| 07 | TEXT | PASS | Click placement, overlay input, OK commits |
| 08 | LEADER | PASS | Click points, Enter commits, arrow+segments+text |
| 09 | DIM | PASS | 3-click, feet-inches format |
| 10 | HATCH | PASS | Click inside boundary, ANSI31/SOLID/DOTS patterns |
| 11 | ERASE | PASS | Click removes, undo restores |

---

## L0-CMD-2026-0210-004 Phase Breakdown

| Phase | Description | Status | Commit |
|-------|-------------|--------|--------|
| Phase 1 | Defensive Hardening (ErrorBoundary, null guards, safeText) | COMPLETE | bf2e0a5 |
| Phase 2 | Fix Step 4 PDF Viewer | COMPLETE | 21f2742 |
| Phase 3 | Edit Capability (Steps 1, 2, 5) | COMPLETE | 56a33bb |
| Phase 4 | Projects Export + Document Management | COMPLETE | 41a6f33 |
| Phase 5 | Settings Page (7 tabs) | COMPLETE | 938f990 |
| Phase 5.5 | Premium Pricing + Anti-Capture | COMPLETE | d92de1f |
| Phase 6 | Assembly + Deploy + Verify | COMPLETE | 0fe2b56 |

---

## Build Metrics

| Metric | Value |
|--------|-------|
| Frontend chunks | 29 |
| Assembled HTML size | 457.4 KB |
| Registered views | 8 |
| Backend endpoints | 41 |
| Backend routers | 8 |
| Database models | 7 |
| Backend tests | 75/75 passing |
| Bracket balance | 7354 open / 7354 close (0 diff) |
| safeText references | 29 |
| ErrorBoundary references | 3 |
| Anti-capture utilities | 4 (gateDownload, isSampleTier, addWatermark, sd-watermark CSS) |
| Backend services | normalizer.py, audit.py (new), + existing AI/DXF/Stripe/email |
| Validation rules | VR-001 through VR-010 (real FAIL/WARN enforcement) |

---

## Deployment Status

| Component | Status | URL |
|-----------|--------|-----|
| Frontend (Vercel) | LIVE | https://shopdrawings.ai |
| Frontend (alias) | LIVE | https://gpc-shop-drawings.vercel.app |
| Backend (Railway) | LIVE | https://gpcshopdrawings-production.up.railway.app |
| PostgreSQL (Railway) | LIVE | 7 tables via Alembic |
| CORS | CONFIGURED | shopdrawings.ai, www.shopdrawings.ai, gpc-shop-drawings.vercel.app, localhost |
| Clerk Auth | CONFIGURED | Keys in Railway, sign-in needs debug |
| Groq AI | CONFIGURED | API key in Railway |
| Anthropic AI | CONFIGURED | API key in Railway |
| Stripe Billing | NOT CONFIGURED | Backend ready, no keys/products/webhooks |
| Custom Domain | LIVE | shopdrawings.ai (DNS configured) |

---

## Frontend Views Status

| View | Chunk | Status | API Wired | Editable |
|------|-------|--------|-----------|----------|
| DashboardView | 05 | Working | Demo data | -- |
| EmailParserView | 06 | Working | Local parse | Yes (products, sheets, specs) |
| SpecSelectorPanel | 07 | Working | -- | Yes (manual add) |
| DocUploadPanel | 08 | Working | -- | Yes (notes) |
| PdfViewer | 09 | Working | -- | -- |
| AiAnalysisPanel | 11 | Working | Demo fallback | Yes (products, keynotes) |
| LineStylePicker | 13 | Working | -- | Yes (colors, linetypes) |
| DxfGeneratorPanel | 17 | Wired | POST /dxf, /dxf/batch | -- |
| ValidationPanel | 19 | Working | POST /validate/local, /validate/batch | VR-001–VR-010 |
| DeliveryPanel | 20 | Working | POST /generate/delivery | ZIP/PDF/Email |
| ProductLibraryView | 14 | Stub | Not wired | -- |
| DetailViewerView | 21 | Working | GET /projects | CSV/XLSX export |
| AdminView | 22 | Stub | Not wired | -- |
| BillingView | 23 | Working | -- | 5 premium tiers |
| ProjectWorkflowView | 26 | Working | -- | 8-step orchestrator |
| SettingsView | 27 | Working | localStorage | 7 tabs |

---

## Middletown MHS -- Shop Drawing Production

**Command:** L0-CMD-2026-0208-002
**Deadline:** 2026-02-20
**GCP Engineer:** Nicole Calpin

### Detail Analysis Progress

| Sheet | In Scope | Analyzed | Approved | CAD Done | Validated |
|-------|----------|----------|----------|----------|-----------|
| A6.1.1 | 6 | **6** | 0 | 0 | 0 |
| A6.1.3 | 7 | **1** | 0 | 0 | 0 |
| A6.2.1 | 6 | 0 | 0 | 0 | 0 |
| A6.2.2 | 5 | 0 | 0 | 0 | 0 |
| A6.2.3 | 5 | 0 | 0 | 0 | 0 |
| A6.3.1 | 8 | **6** | 0 | 0 | 0 |
| A7.2.3 | 6 | 0 | 0 | 0 | 0 |
| A7.2.4 | 6 | 0 | 0 | 0 | 0 |
| **TOTAL** | **49** | **13** | **0** | **0** | **0** |

### Open Flags

| Flag | Detail | Severity | Description |
|------|--------|----------|-------------|
| FLAG-001 | A6.1.1-01 | WARNING | Architect's 3" lap < GCP 4" minimum |
| FLAG-002 | A6.1.1-03 | INFO | Below-grade waterproofing outside GCP scope |
| FLAG-003 | A6.1.1-04 | INFO | NPS Detail at sheathing-to-steel transition |
| FLAG-004 | A6.1.1-06 | INFO | Complex multi-view detail, confirm keynotes |
| FLAG-005 | A6.1.1-06 | INFO | Expansion joint may need Trinity silicone strip |
| FLAG-006 | Multiple | INFO | CUT END lapping at wedge panel joints |
| FLAG-007 | A6.3.1-04, A6.1.3-08 | INFO | Corner wrap reinforcement question |
| FLAG-008 | A6.3.1-05 | WARNING | Window jamb transition -- no explicit GCP keynote |

---

## Next Actions

1. **P0:** Deploy and run golden-path demo (end-to-end runtime verification pending)
2. **P0:** Fix Clerk auth sign-in spinner + demo mode isolation
3. **P1:** Wire remaining stub views (ProductLibrary, Admin)
4. **P1:** Configure Stripe billing (5 tiers)
5. **P1:** CAD Editor Phase 3: Export to DXF, import from DXF viewer
6. **Middletown:** L0 to review/approve 13 detail JSONs, upload remaining 36 screenshots
