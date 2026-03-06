# ShopDrawing.AI

AI-powered shop drawing production for building envelope contractors.

**Live:** https://shopdrawings.ai | **API:** https://gpcshopdrawings-production.up.railway.app

---

## Architecture

```
                     ┌──────────────────────────────────────────┐
                     │           ShopDrawing.AI                 │
                     │         https://shopdrawings.ai          │
                     └──────────────────────────────────────────┘

 ┌─────────────────────┐     HTTPS/JSON      ┌──────────────────────┐
 │  Vercel Frontend     │ ──────────────────> │  Railway Backend     │
 │  React 18 SPA        │ <────────────────── │  FastAPI + Uvicorn   │
 │  29 JSON chunks      │    CORS enabled     │  42 endpoints        │
 │  No JSX, No Babel    │                     │  Clerk JWT auth      │
 │  457 KB assembled    │                     │  8 routers           │
 └─────────────────────┘                     └──────────┬───────────┘
   shopdrawings.ai                                      │
                                          ┌─────────────┼─────────────┐
                                          │             │             │
                                    ┌─────▼─────┐ ┌────▼────┐ ┌──────▼──────┐
                                    │ PostgreSQL │ │  Groq   │ │  Anthropic  │
                                    │ (Railway)  │ │  API    │ │  Claude API │
                                    │ 7 tables   │ │  Llama  │ │  Vision     │
                                    └───────────┘ └─────────┘ └─────────────┘
```

---

## 8-Step Workflow

| Step | Name | Chunk | Description |
|------|------|-------|-------------|
| 1 | Email/Scope Intake | 06 | Paste scope email or upload files; edit products, sheets, specs inline |
| 2 | Spec Selection | 07 | CSI section grid with keyword suggestions + manual add |
| 3 | Document Upload | 08 | Drag-drop PDF/DWG/images with document management and notes |
| 4 | PDF Review | 09 | PDF.js canvas viewer with pan/zoom/select tools and region capture |
| 5 | AI Analysis | 11 | Claude Vision analysis with editable products and keynote mappings |
| 6 | Line Style | 13 | 27 ACI colors, 6 linetypes, SVG preview, presets (GCP/Carlisle/Custom) |
| 7 | DXF Generation | 17 | ezdxf pipeline via API, individual + batch ZIP download |
| 8 | Validation & Delivery | 19+20 | VR-001 through VR-010 checks + delivery package builder |

---

## Pricing Tiers

| Tier | Price | Projects | AI Credits | Target |
|------|-------|----------|------------|--------|
| SAMPLE | $0 | 1 demo | None | Evaluation |
| PER-PROJECT | $1,000 | 1 full project | Included | Single project |
| PRO | $2,500/mo | 10/month | $200/mo | Regular contractors |
| PRO UNLIMITED | $10,000/mo | Unlimited | $1,000/mo | Large firms |
| WHITE LABEL | $15,000/mo | Unlimited | $2,000/mo | Resellers |

---

## Tech Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Frontend Framework | React | 18 (via CDN, `React.createElement()` only) |
| Frontend Build | JSON chunk assembly (`assemble.py`) | 29 chunks -> 1 HTML |
| CAD Editor | SVG-based drawing tools (vanilla JS) | 11 tools, snap engine, undo/redo |
| PDF Viewer | PDF.js | 3.x (via CDN) |
| Icons | Lucide | via CDN |
| Backend Framework | FastAPI | >= 0.115.0 |
| Backend Runtime | Python + Uvicorn | 3.11+ |
| Database | PostgreSQL | 16 (asyncpg + SQLAlchemy 2.0) |
| Migrations | Alembic | >= 1.13.0 |
| Auth | Clerk | JWT via PyJWT + JWKS |
| Billing | Stripe | >= 8.0.0 (backend ready, frontend stub) |
| AI (fast) | Groq | Llama 4 Scout |
| AI (vision) | Anthropic | Claude Vision API |
| CAD Generation | ezdxf | Python DXF library |

---

## Deployment

| Component | URL | Platform |
|-----------|-----|----------|
| Frontend | https://shopdrawings.ai | Vercel |
| Frontend (alias) | https://gpc-shop-drawings.vercel.app | Vercel |
| Backend API | https://gpcshopdrawings-production.up.railway.app | Railway |
| Swagger Docs | https://gpcshopdrawings-production.up.railway.app/docs | Railway |
| Health Check | https://gpcshopdrawings-production.up.railway.app/health | Railway |
| PostgreSQL | Railway managed (internal) | Railway |

---

## Registered Views (8)

| View ID | Component | Chunk | Status |
|---------|-----------|-------|--------|
| dashboard | DashboardView | 05 | Working |
| new-project | EmailParserView | 06 | Working (editable) |
| products | ProductLibraryView | 14 | Stub (hardcoded) |
| projects | DetailViewerView | 21 | Working (CSV/XLSX export) |
| admin | AdminView | 22 | Stub (hardcoded) |
| billing | BillingView | 23 | Working (premium pricing) |
| project-workflow | ProjectWorkflowView | 26 | Working (8 steps) |
| settings | SettingsView | 27 | Working (localStorage) |

---

## Environment Variables

```
# Database (Railway provides automatically)
DATABASE_URL=postgresql+asyncpg://...

# Clerk Authentication
CLERK_SECRET_KEY=sk_live_...
CLERK_PUBLISHABLE_KEY=pk_live_...
CLERK_FRONTEND_API=<instance>.clerk.accounts.dev
CLERK_WEBHOOK_SECRET=whsec_...

# AI Providers
GROQ_API_KEY=gsk_...
ANTHROPIC_API_KEY=sk-ant-...

# Stripe Billing (backend ready, not yet configured)
STRIPE_SECRET_KEY=sk_live_...
STRIPE_PUBLISHABLE_KEY=pk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...
STRIPE_PRICE_PRO=price_...
STRIPE_PRICE_ENTERPRISE=price_...

# Frontend URL (for CORS)
FRONTEND_URL=https://shopdrawings.ai

# Storage
STORAGE_BACKEND=local
STORAGE_PATH=./storage

# Operator
OPERATOR_EMAILS=armand@lefebvredesign.com
```

---

## Local Development

### Backend
```bash
cd server
pip install -r requirements.txt
cp .env.example .env   # Edit with your credentials
# Start PostgreSQL locally
alembic upgrade head
uvicorn app.main:app --reload --port 8001
# Dev mode: use Bearer token 'dev-token' for auth
```

### Frontend
```bash
# Reassemble from chunks (if editing chunks)
python3 ui/assemble.py
# Open in browser
open ui/shopdrawing.html
# Click "Try Demo" on login screen for demo mode
```

---

## Project Structure

```
GPC_Shop_Drawings/
├── README.md                   # This file
├── HANDOFF.md                  # Session handoff state
├── PROGRESS.md                 # Build progress tracker
├── CLAUDE_LOG.md               # Full development log
├── TODO.md                     # Prioritized task list
├── MILESTONE-*.md              # Governance milestones
│
├── server/                     # Backend API (FastAPI + PostgreSQL)
│   ├── requirements.txt
│   ├── Procfile                # Railway: alembic upgrade head && uvicorn ...
│   ├── railway.toml            # Railway config
│   ├── alembic.ini
│   ├── alembic/                # Database migrations
│   └── app/
│       ├── main.py             # FastAPI app, CORS, 41 endpoints
│       ├── config.py           # Pydantic Settings
│       ├── database.py         # Async SQLAlchemy engine + session
│       ├── models/             # 7 SQLAlchemy models
│       ├── middleware/         # Clerk JWT auth + tenant context
│       ├── routers/            # 8 API routers
│       ├── services/           # AI, Stripe, DXF, LISP, email, normalizer, audit
│       └── utils/              # Cost calculator, file storage
│
├── ui/
│   ├── shopdrawing.html        # Assembled SPA (~406 KB)
│   ├── index.html              # Identical copy (Vercel entry point)
│   ├── assemble.py             # JSON chunk assembler (29 chunks -> 1 HTML)
│   └── chunks/                 # 29 JSON chunk files (00-shell through 28-cad-editor)
│
├── src/
│   ├── dxf_generator.py        # ezdxf DXF pipeline (CLI)
│   ├── visual_parser.py        # Claude Vision integration
│   ├── validation_engine.py    # VR-001 through VR-010
│   ├── material_schedule.py    # BOM generator (JSON + CSV)
│   └── cad_instructions.py     # CAD instruction set generator
│
├── data/
│   ├── product_rules.json      # 7 GCP products, keynote mapping
│   ├── detail_matrix.json      # 49 details with sheet assignments
│   └── detail_analysis_schema.json
│
├── details/                    # Per-detail analysis JSONs (13/49)
├── docs/                       # Architecture PDFs, commands, governance
└── templates/                  # CAD templates (future use)
```

---

## 42 API Endpoints

| Router | # | Endpoints |
|--------|---|-----------|
| auth | 2 | GET /api/auth/me, POST /api/auth/webhook |
| projects | 6 | GET/POST /api/projects, GET/PATCH/DELETE /api/projects/:id, POST /api/projects/:id/details |
| upload | 3 | POST /api/projects/:id/upload, GET /api/projects/:id/files, GET /api/files/:path |
| analyze | 3 | POST /api/analyze/email, POST /api/analyze/screenshot, POST /api/analyze/submittal |
| generate | 5 | POST /api/generate/dxf, POST /api/generate/dxf/batch, POST /api/generate/lisp, POST /api/generate/delivery, GET /api/generate/download/:path |
| billing | 5 | GET /api/billing/plans, POST /api/billing/checkout, POST /api/billing/portal, GET /api/billing/usage, POST /api/billing/webhook |
| admin | 9 | GET/PUT /api/admin/settings/:key, GET/POST/PUT/DELETE /api/admin/products, POST /api/admin/products/global, GET /api/admin/tenants |
| validate | 4 | GET /api/validate/rules, POST /api/validate/detail, POST /api/validate/local, POST /api/validate/batch |

---

## Command History

| # | Command ID | Description | Status |
|---|------------|-------------|--------|
| 1 | L0-CMD-2026-0208-002 | Shop Drawing Production System (parent) | In Progress |
| 2 | L0-CMD-2026-0209-001 | ezdxf Pipeline | Complete |
| 3 | L0-CMD-2026-0209-002 | SaaS Frontend (25 chunks) | Complete |
| 4 | L0-CMD-2026-0209-003 | Backend API (12 modules, 41 endpoints) | Complete |
| 5 | L0-CMD-2026-0209-003-A1 | DXF Viewer (client-side) | Complete |
| 6 | L0-CMD-2026-0209-004 | Clerk Auth Integration | Complete |
| 7 | L0-CMD-2026-0209-005 | CORS + Clerk Hotfix | Complete |
| 8 | L0-CMD-2026-0209-006 | CORS 500 Deep Fix | Complete |
| 9 | L0-CMD-2026-0209-007 | Documentation Update | Complete |
| 10 | L0-CMD-2026-0210-002 | Full System Audit (40/40 PASS) | Complete |
| 11 | L0-CMD-2026-0210-003 | Wire DXF Pipeline | Complete |
| 12 | L0-CMD-2026-0210-004 | UX Hardening (7 phases) | Complete |
| 13 | L0-CMD-2026-0210-005 | Documentation Update (Alpha) | Complete |
| 14 | L0-CMD-2026-0218-011 P1 | CAD Editor Phase 1: Canvas shell + layer system | Complete |
| 15 | L0-CMD-2026-0218-011 P2 | CAD Editor Phase 2: 11 drawing tools + snap engine | Complete |
| 16 | L0-CMD-SHOPDRAWINGS-FINAL-HARDENING-001 | End-to-end hardening (validation, normalization, audit) | Complete |

---

## Infrastructure

| Service | Plan | Cost |
|---------|------|------|
| Railway (API + PostgreSQL) | Starter | ~$5/mo + usage |
| Vercel (Frontend) | Hobby | $0 |
| Clerk (Auth) | Free tier | $0 (up to 10k MAU) |
| Groq (AI) | Free tier | $0 (rate limited) |
| Anthropic (AI) | Pay per use | Per token |
| Custom Domain | shopdrawings.ai | ~$12/yr |

---

## Governance

This project operates under LDS (Lefebvre Design Solutions) L0-command governance. All changes are tracked via numbered commands, each with PROGRESS.md and HANDOFF.md checkpoints. See `docs/lds-governance/` for full governance documentation.

**Current Milestone:** FINAL-HARDENING-COMPLETE (2026-03-06)

### Command Governance

This repository uses the **ValidKernel Command Protocol v0.1** for AI/human execution governance. All commands follow the structured format defined in: `docs/validkernel/command-protocol.md`

Command executions produce **ValidKernel Command Receipts v0.1** -- structured JSON records stored in `.validkernel/receipts/`. Receipt spec: `docs/validkernel/command-receipts.md`

All commands are indexed in the **ValidKernel Command Registry v0.1** at `.validkernel/registry/command-registry.json`. Registry spec: `docs/validkernel/command-registry.md`

VKG operational tools for receipt validation and governance record updates are available at `.validkernel/tools/`. See `docs/validkernel/governance-tools.md`.

The ValidKernel Runtime Gate (VKRT) is executable at `.validkernel/tools/runtime-gate.py` — validates command structure before execution.

VKG governance is CI-enforced via `.github/workflows/vkg-governance-check.yml` — receipt and registry validation runs on every push and pull request.

```
Command Format: ValidKernel Command Protocol v0.1
```

---

## License

Proprietary -- Lefebvre Design Solutions LLC. All rights reserved.
