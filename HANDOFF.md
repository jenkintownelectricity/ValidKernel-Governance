# HANDOFF — 2026-03-06
## Previous Command: L0-CMD-SHOPDRAWINGS-FINAL-HARDENING-001 (End-to-End Hardening — COMPLETE)
## Branch: claude/finalize-shop-drawings-wTMH9

---

## ARCHITECTURE

| Layer | Tech | Location |
|-------|------|----------|
| Frontend | React 18 CDN SPA (no build, no JSX, no Babel) | `ui/shopdrawing.html` (~457KB assembled) |
| Frontend chunks | 29 JSON files assembled by Python script | `ui/chunks/00-shell.json` through `28-cad-editor.json` |
| CAD Editor | SVG-based, 11 drawing tools, vanilla JS | `ui/chunks/28-cad-editor.json` (95.6KB) |
| Frontend assembler | Python script merges chunks to BOTH html files | `ui/assemble.py` |
| Backend | FastAPI + Uvicorn (Python 3.11) | `server/app/main.py` (42+ endpoints, 8 routers) |
| Database | PostgreSQL 16 + SQLAlchemy 2.0 async + Alembic | `server/app/models/` (7 tables) |
| Auth | Clerk JWT via PyJWT + JWKS | Frontend: `00-shell.json` / Backend: `server/app/middleware/clerk_auth.py` |
| AI | Claude Vision + Groq Llama 4 Scout | `server/app/services/claude_service.py`, `groq_service.py` |
| Normalizer | Canonical data lock (raw AI → schema enums) | `server/app/services/normalizer.py` |
| Audit | Structured event logging for critical path | `server/app/services/audit.py` |
| Billing | Stripe (backend ready, NOT wired to frontend) | `server/app/services/stripe_service.py` |
| Deploy (FE) | Vercel | `https://shopdrawings.ai`, `https://gpc-shop-drawings.vercel.app` |
| Deploy (BE) | Railway | `https://gpcshopdrawings-production.up.railway.app` |

**Key patterns:**
- All JS uses `React.createElement()` -- NO Babel, NO JSX
- View system: `VIEW_REGISTRY` object, `registerView(id, Component)` in each chunk
- Navigation: `handleNavigate(viewId, params)` sets `window._workflowNavState` then `setCurrentView(viewId)`
- Assembly: `python ui/assemble.py` writes BOTH `shopdrawing.html` AND `index.html`
- Global utilities in chunk 00 HTML: `safeText()`, `ErrorBoundary`, `gateDownload()`, `isSampleTier()`, `addWatermark()`
- Settings persist in `localStorage` under key `sd_settings`
- Workflow step persists in `localStorage` under key `sd_current_step`

---

## WHAT CHANGED (L0-CMD-SHOPDRAWINGS-FINAL-HARDENING-001)

### Subtask A: Canonical Data Lock
- NEW `server/app/services/normalizer.py` (~240 lines)
- `normalize_analysis()` maps raw AI output to `detail_analysis_schema.json` enums
- Substrate, keynote, product, and continuity normalization
- `validate_for_generation()` — pre-DXF gate
- `validate_for_delivery()` — pre-delivery gate
- Wired into `server/app/routers/analyze.py` after Claude Vision returns

### Subtask B: Validation Gates on Generation
- `server/app/routers/generate.py` — single and batch DXF blocked if analysis incomplete
- Blocked details return `status: "blocked"` with reason list

### Subtask C: Validation Gates on Delivery
- NEW endpoint `POST /api/generate/delivery` — full delivery package builder
- Per-detail `validate_for_delivery()` check, ZIP assembly with manifest
- Email delivery fields supported (email_to, email_subject, email_body)

### Subtask D: Real Validation & Delivery Panels
- `ui/chunks/19-validation.json` — real API calls to `/api/validate/local` and `/api/validate/batch`
- Shows detail selector, per-detail validate buttons, PASS/FAIL/WARN banners, per-rule breakdown
- `ui/chunks/20-delivery.json` — real API call to `/api/generate/delivery`
- Handles blocked state with per-detail blocker list
- ZIP download via Clerk-authenticated fetch

### Subtask E: Audit Event Capture
- NEW `server/app/services/audit.py` (~100 lines)
- `[AUDIT]` prefixed structured logs: `log_analysis()`, `log_validation()`, `log_generation()`, `log_download()`, `log_delivery()`
- Wired into analyze, validate, generate, and download endpoints

### Subtask F: Workflow Step Persistence
- `ui/chunks/26-workflow.json` — saves `currentStep` to `localStorage` as `sd_current_step`
- Restores step on mount/refresh

### Subtask G: Frontend State Tracking
- `server/app/routers/projects.py` — `_detail_to_dict()` includes `validation_overall` and `dxf_path`
- `ui/chunks/26-workflow.json` — passes `project` prop to ValidationPanel and DeliveryPanel

---

## VALIDATION RULES (VR-001 through VR-010)

| Rule | Description | Enforcement |
|------|-------------|-------------|
| VR-001 | Keynote mapping | FAIL if keynotes exist but unmapped (no `gcp_product_key`) |
| VR-002 | Substrate/product match | FAIL if no products identified |
| VR-003 | Warranty boundaries | FAIL if non-GCP products missing warranty note |
| VR-004 | Lap minimums | WARN on lap flags |
| VR-005 | Air barrier continuity | FAIL on continuity gaps |
| VR-006 | Primer requirements | Checks `primer_required` on products |
| VR-007 | Lineweight hierarchy | Checks if DXF exists |
| VR-008 | Detail completeness | PASS if detail present |
| VR-009 | Title block accuracy | WARN if no `detail_title` |
| VR-010 | GCP logo | N/A (deferred to delivery) |

---

## REGISTERED VIEWS (8 total, 29 chunks)

| View ID | Component | Chunk | Status |
|---------|-----------|-------|--------|
| dashboard | DashboardView | 05 | Working |
| new-project | EmailParserView | 06 | Working (editable) |
| products | ProductLibraryView | 14 | Stub (hardcoded) |
| projects | DetailViewerView | 21 | Working (export wired) |
| admin | AdminView | 22 | Stub (hardcoded) |
| billing | BillingView | 23 | Working (premium pricing) |
| project-workflow | ProjectWorkflowView | 26 | Working (8 steps, persistent) |
| settings | SettingsView | 27 | Working (localStorage) |

---

## WHAT'S STILL STUBBED

- ProductLibraryView (chunk 14) -- hardcoded data, no API CRUD
- AdminView (chunk 22) -- hardcoded admin data
- DxfGeneratorPanel (chunk 17) -- wired to API but backend pipeline needs real DXF generation
- Stripe checkout -- placeholder modals only
- Settings backend persistence -- currently localStorage only
- Clerk auth sign-in spinner -- renders but spins indefinitely
- Demo mode isolation -- `apiFetch()` should check `isDemoMode` before fetch

---

## NEW BACKEND SERVICES

| Service | File | Purpose |
|---------|------|---------|
| Normalizer | `server/app/services/normalizer.py` | Canonical data lock — maps raw AI output to schema enums |
| Audit Logger | `server/app/services/audit.py` | Structured `[AUDIT]` event logging for critical-path events |

---

## CORS ORIGINS (server/app/main.py)

```
cors_origins = [
    "https://shopdrawings.ai",
    "https://www.shopdrawings.ai",
    "https://gpc-shop-drawings.vercel.app",
    "http://localhost:8080",
    "http://localhost:3000",
    "http://127.0.0.1:8080",
]
```

---

## COMMAND GOVERNANCE

This repository uses the **ValidKernel Command Protocol v0.1** for AI/human execution governance. All commands follow the structured format defined in: `docs/validkernel/command-protocol.md`

Command executions produce **ValidKernel Command Receipts v0.1** -- structured JSON records stored in `.validkernel/receipts/`. Receipt spec: `docs/validkernel/command-receipts.md`

All commands are indexed in the **ValidKernel Command Registry v0.1** at `.validkernel/registry/command-registry.json`. Registry spec: `docs/validkernel/command-registry.md`

The overarching governance framework is defined in the **ValidKernel Governance (VKG) Specification v0.1** at `docs/validkernel/vkg-spec.md`. Runtime execution behavior is defined in `docs/validkernel/command-execution-flow.md`.

**Portable Authority** identity is materialized at `.validkernel/authority/authority.json`.

VKG operational tools: `.validkernel/tools/validate-receipt.py` (receipt validation), `.validkernel/tools/update-governance-record.py` (receipt/registry auto-update), `.validkernel/tools/validate-all-receipts.py` (batch validation). See `docs/validkernel/governance-tools.md`.

VKRT is executable: `.validkernel/tools/runtime-gate.py` validates command structure before execution (16 structural checks, PASS/FAIL with `--json` option).

VKG Installer: `.validkernel/tools/install-vkg.py` deploys the governance kernel into target repositories with fail-closed overwrite protection and optional authority, registry, and install receipt initialization. Target repos: ShopDrawing.AI, construction_dna, SUPA-SAINT, future governed repositories.

VKG governance is CI-enforced: `.github/workflows/vkg-governance-check.yml` runs receipt and registry validation on every push and pull request.

Repository architecture separates: kernel (`.validkernel/tools/`), canon (`canon/`), live state (`.validkernel/state/`), history (`.validkernel/receipts/`, `.validkernel/registry/`). Canon stores authoritative command artifacts; state tracks repo posture and runtime readiness.

---

## VERIFY STATE

```bash
git log --oneline -10
ls ui/chunks/*.json | wc -l   # Expected: 29
grep -c "registerView" ui/shopdrawing.html  # Expected: 10
diff ui/shopdrawing.html ui/index.html      # Expected: identical
grep -c "normalize_analysis\|validate_for_generation\|validate_for_delivery" server/app/services/normalizer.py  # Expected: 3
grep -c "log_analysis\|log_validation\|log_generation\|log_download\|log_delivery" server/app/services/audit.py  # Expected: 5
```

---

*END OF HANDOFF*
