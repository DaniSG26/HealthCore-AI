# Folder Structure Rules — What Goes Where

**Purpose:** Define the monorepo folder structure and prohibit deviations.

**Rule:** Follow the structure. Don't innovate folder layouts.

---

## 📁 Monorepo Structure (Authoritative)

```
HealthCore-AI/
├── CONTEXT.md / CONTEXT.es.md         ← Business context
├── AGENTS.md                          ← Agent workflow
├── README.md / README.es.md           ← Repo overview
├── .copilot-instructions.md           ← Instructions for agents
├── .agents/                           ← TECHNICAL CONVENTIONS (you are reading this)
│   ├── README.md                      ← Index of rules
│   ├── python-conventions.md
│   ├── typescript-conventions.md
│   ├── compliance-rules.md
│   ├── data-handling.md
│   └── folder-structure.md            ← This file
│
├── docs/                              ← DOCUMENTATION
│   ├── README.md                      ← Doc index (must have)
│   ├── TECHNICAL_CONTEXT.md           ← Technical decisions (must have)
│   └── [other docs as needed]
│
├── services/                          ← BACKEND APIS
│   ├── README.md                      ← Service overview
│   ├── api/                           ← Main FastAPI or Express app
│   │   ├── routers/                   ← API endpoints by domain
│   │   │   ├── clinical.py
│   │   │   ├── patient_access.py
│   │   │   └── [more routers]
│   │   ├── models/                    ← Data models
│   │   ├── services/                  ← Business logic
│   │   ├── utils/                     ← Helpers
│   │   ├── config.py
│   │   └── main.py
│   └── [other services if extracted later]
│
├── uis/                               ← USER INTERFACES
│   ├── README.md                      ← UI overview
│   ├── website/                       ← Public website
│   │   ├── src/
│   │   │   ├── components/
│   │   │   ├── pages/
│   │   │   ├── services/
│   │   │   ├── types/
│   │   │   └── App.tsx
│   │   ├── package.json
│   │   └── README.md
│   └── backoffice/                    ← Internal admin app
│       ├── src/
│       │   ├── components/
│       │   ├── pages/
│       │   ├── services/
│       │   ├── types/
│       │   └── App.tsx
│       ├── package.json
│       └── README.md
│
├── data/                              ← DATA & PIPELINES
│   ├── README.md
│   ├── raw/                           ← Original data sources (read-only)
│   │   ├── claims/
│   │   ├── patients/
│   │   └── README.md
│   ├── process/                       ← Processing scripts
│   │   ├── clean_claims.py
│   │   ├── validate_patients.py
│   │   └── README.md
│   ├── pipelines/                     ← Scheduled ETL jobs
│   │   ├── daily_claims_processor.py
│   │   └── README.md
│   └── eval/                          ← Evaluation datasets
│       ├── test_claims.csv
│       └── README.md
│
├── agents/                            ← AI AGENTS
│   ├── README.md                      ← Agents overview
│   ├── _template/                     ← Template for new agents
│   │   ├── agent.py
│   │   ├── README.md
│   │   └── tests/
│   └── [specific agent folders when created]
│       ├── README.md
│       ├── agent.py
│       └── tests/
│
├── skills/                            ← REUSABLE SKILLS
│   ├── README.md                      ← Skills overview
│   ├── _template/                     ← Template for new skills
│   │   ├── SKILL.md                   ← Skill definition
│   │   ├── examples/
│   │   ├── resources/
│   │   └── scripts/
│   ├── clinical-claims-analysis/      ← Skill 1
│   │   ├── SKILL.md
│   │   ├── examples/
│   │   ├── resources/
│   │   └── scripts/
│   └── [more skills as added]
│
├── mcps/                              ← MCP TOOL SERVERS
│   ├── README.md
│   └── [MCP implementations]
│
├── packages/                          ← SHARED CODE
│   ├── README.md
│   └── shared/
│       ├── package.json
│       ├── types/
│       │   └── index.ts
│       └── README.md
│
├── shared/                            ← SHARED RESOURCES
│   ├── README.md
│   └── [shared configs, constants]
│
├── workflows/                         ← AUTOMATION (n8n)
│   ├── README.md
│   └── [workflow definitions]
│
├── internal/                          ← INTERNAL TOOLS
│   ├── README.md
│   └── [scripts, CLIs for team]
│
├── infra/                             ← INFRASTRUCTURE
│   ├── README.md
│   ├── docker-compose.yml             ← Local dev orchestration
│   └── [deployment configs]
│
├── scripts/                           ← UTILITY SCRIPTS
│   ├── README.md
│   └── [one-off scripts]
│
├── .devcontainer/                     ← DEV CONTAINER CONFIG
├── .git/                              ← Git repo
├── .gitignore                         ← Git ignore rules
├── company-choise.md                  ← Company choice documentation
└── [root config files as needed]
```

---

## ✅ Rules by Folder

### `/` (Root)
**What goes here:**
- ✅ `CONTEXT.md` / `CONTEXT.es.md` — Business context
- ✅ `README.md` / `README.es.md` — Repo overview
- ✅ `AGENTS.md` — Agent workflow
- ✅ `.copilot-instructions.md` — Agent instructions
- ✅ `docker-compose.yml` — Local dev setup

**What doesn't go here:**
- ❌ Application code (goes in `services/`, `uis/`, or `agents/`)
- ❌ Data files (goes in `data/`)
- ❌ Documentation beyond README (goes in `docs/`)

---

### `.agents/` (Technical Conventions)
**What goes here:**
- ✅ Language-specific conventions (python-conventions.md, typescript-conventions.md)
- ✅ Data handling rules (data-handling.md)
- ✅ Compliance rules (compliance-rules.md)
- ✅ Folder structure (folder-structure.md)
- ✅ General conventions (README.md)

**What doesn't go here:**
- ❌ Actual code (goes in `services/`, `uis/`, etc.)
- ❌ Examples (goes in `skills/` or `agents/`)
- ❌ Documentation beyond conventions (goes in `docs/`)

---

### `docs/` (Documentation)
**What goes here:**
- ✅ `TECHNICAL_CONTEXT.md` — Technical decisions
- ✅ `README.md` — Doc index
- ✅ Architecture guides, decision logs
- ✅ Setup instructions, deployment guides

**What doesn't go here:**
- ❌ Code (goes in `services/`, `uis/`, etc.)
- ❌ Agent instructions (goes in root as `.copilot-instructions.md`)
- ❌ Conventions (goes in `.agents/`)

---

### `services/` (Backend APIs)
**What goes here:**
- ✅ One unified `api/` folder with FastAPI or Express
- ✅ Routers for different domains (clinical, billing, HR)
- ✅ Models, services, utils organized inside
- ✅ Each service gets its own subfolder + README

**What doesn't go here:**
- ❌ Frontend code (goes in `uis/`)
- ❌ AI agents (goes in `agents/`)
- ❌ Data processing (goes in `data/`)

**Example:**
```
services/
├── api/           ← Main application
│   ├── routers/
│   │   ├── clinical.py
│   │   └── billing.py
│   └── main.py
└── README.md
```

---

### `uis/` (User Interfaces)
**What goes here:**
- ✅ `website/` — Public patient-facing UI
- ✅ `backoffice/` — Internal staff portal
- ✅ Each has its own `src/`, `package.json`, `README.md`

**What doesn't go here:**
- ❌ Backend code (goes in `services/`)
- ❌ No new top-level apps without PM approval
- ❌ No `api/`, `backend/`, or `server/` folders

**Example:**
```
uis/
├── website/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   └── App.tsx
│   └── package.json
└── backoffice/
    ├── src/
    │   ├── components/
    │   ├── pages/
    │   └── App.tsx
    └── package.json
```

---

### `data/` (Data & Pipelines)
**What goes here:**
- ✅ `raw/` — Original data sources (never modify)
- ✅ `process/` — Processing scripts
- ✅ `pipelines/` — Scheduled ETL jobs
- ✅ `eval/` — Test/evaluation datasets

**What doesn't go here:**
- ❌ Application code (goes in `services/`)
- ❌ No unorganized CSV/JSON files at root

**Rule:** Data flows unidirectionally → `raw/` → `process/` → `pipelines/` → `services/`

---

### `agents/` (AI Agents)
**What goes here:**
- ✅ One subfolder per agent (e.g., `claims-analyzer/`, `onboarding-agent/`)
- ✅ Each agent has: `agent.py`, `README.md`, `tests/`
- ✅ Use `_template/` as reference

**What doesn't go here:**
- ❌ Skills (goes in `skills/`)
- ❌ General utilities (goes in `packages/shared/`)
- ❌ No top-level Python files

**Example:**
```
agents/
├── _template/
│   ├── agent.py
│   ├── README.md
│   └── tests/
└── claims-analyzer/        ← Specific agent
    ├── agent.py
    ├── README.md
    └── tests/
```

---

### `skills/` (Reusable Capabilities)
**What goes here:**
- ✅ One subfolder per skill
- ✅ Each skill has: `SKILL.md`, `examples/`, `resources/`, `scripts/`
- ✅ Use `_template/` as reference

**What doesn't go here:**
- ❌ Specific agents (goes in `agents/`)
- ❌ Application code (goes in `services/`)

**Example:**
```
skills/
├── _template/
│   ├── SKILL.md
│   ├── examples/
│   ├── resources/
│   └── scripts/
└── clinical-claims-analysis/     ← Specific skill
    ├── SKILL.md
    ├── examples/
    ├── resources/
    └── scripts/
```

---

### `packages/shared/` (Shared Types)
**What goes here:**
- ✅ Shared TypeScript types (`@repo/shared-types`)
- ✅ Common interfaces for Patient, Claim, etc.
- ✅ `package.json` and `tsconfig.json`

**What doesn't go here:**
- ❌ Application code
- ❌ Utilities (unless truly shared and minimal)

---

### `workflows/` (n8n Automation)
**What goes here:**
- ✅ Workflow definitions (n8n JSON exports)
- ✅ Automation between systems
- ✅ Scheduled tasks

**What doesn't go here:**
- ❌ Python/JavaScript code (goes in `services/` or `data/`)

---

### `infra/` (Infrastructure)
**What goes here:**
- ✅ `docker-compose.yml` — Local development
- ✅ Kubernetes manifests (if used)
- ✅ Terraform/CDK configs
- ✅ Deployment scripts

**What doesn't go here:**
- ❌ Application code

---

## 🚫 Prohibited Patterns

| ❌ Pattern | Why | Correct Approach |
|-----------|-----|-----------------|
| Create `backend/` folder | Already have `services/` | Use `services/` |
| Create `frontend/` folder | Already have `uis/` | Use `uis/website/` or `uis/backoffice/` |
| Put API code in `uis/` | Mixing concerns | Use `services/` |
| Put UI code in `services/` | Mixing concerns | Use `uis/` |
| Create `src/` at root | Unclear structure | Put in subfolder (services/api/src, uis/website/src) |
| Scatter Python scripts at root | Disorganized | Use `data/process/` or `scripts/` |
| Create new top-level folders | Structure already defined | Use existing folders |

---

## ✅ Checklist: Before Creating New Folder/File

- ✅ Read this document
- ✅ Check if the folder already exists
- ✅ Check if the structure allows what I need
- ✅ Ask PM if unsure (don't guess)
- ✅ Create in the right place
- ✅ Create a README.md for new subfolders
- ✅ Update parent README.md to link new folder

---

## 🔗 Related

- **Conventions:** [`.agents/`](./README.md)
- **Tech Context:** [`../docs/TECHNICAL_CONTEXT.md`](../docs/TECHNICAL_CONTEXT.md)
- **Agent Workflow:** [`../AGENTS.md`](../AGENTS.md)

---

**Last updated:** 2026-11-25  
**Review this:** Before creating any new folder or file  
**Rule:** No innovation. Follow the structure.
