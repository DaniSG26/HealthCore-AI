# Technical Context - HealthCore AI Engineering

**Last updated:** 2026-11-25  
**Purpose:** Foundation document for all technical decisions, conventions, and architecture patterns in the HealthCore monorepo.

---

## 📋 Table of Contents

1. [Stack Overview](#stack-overview)
2. [Code Conventions](#code-conventions)
3. [Architecture Patterns](#architecture-patterns)
4. [Data Handling & Compliance](#data-handling--compliance)
5. [Testing & Quality Assurance (TBD)](#testing--quality-assurance-tbd)
6. [Deployment & Infrastructure](#deployment--infrastructure)
7. [Tools & Development Environment](#tools--development-environment)

---

## 📦 Stack Overview

### Current State
The HealthCore monorepo uses a **decoupled structure** where each layer can adopt its own technology stack as needed:

| Layer | Technology | Status | Notes |
|-------|-----------|--------|-------|
| **Backend API** | TBD (FastAPI/Express candidate) | Planning | Central unified API for all departments |
| **Frontend (Public)** | TBD (React/Vue/Next.js candidate) | Planning | Public website for patient-facing features |
| **Frontend (Internal)** | TBD (React/Vue/Next.js candidate) | Planning | Backoffice for operations, HR, billing |
| **Data Processing** | Python (pandas/polars) | Planned | ETL pipelines, data analysis |
| **AI/ML** | Python (scikit-learn/transformers) | Planned | Agents, predictions, NLP models |
| **Shared Types** | TypeScript/Python | Active | `packages/shared/` - type definitions |

### Decision Principle
**Choose the right tool for the job, but default to:**
- **Backend**: One unified API (avoid microservices fragmentation)
- **Frontend**: React/Next.js for consistency (unless team decides otherwise)
- **Data**: Python for scientific computing
- **AI**: Python for ecosystem maturity

---

## 💻 Code Conventions

### Python (If Used)

**Naming:**
- `snake_case` for functions, variables, modules
- `PascalCase` for classes
- `UPPER_SNAKE_CASE` for constants
- Prefix private members with `_`

**Structure:**
```python
# File organization
src/
├── models/          # Pydantic models, database schemas
├── services/        # Business logic
├── routes/          # API endpoints
├── utils/           # Shared utilities
├── config.py        # Configuration
└── main.py          # Entry point
```

**Imports:**
- Standard library first
- Third-party libraries second
- Local imports last
- Alphabetically within each group

**Docstrings:**
```python
def analyze_claims(claims_data: list[dict]) -> dict:
    """
    Analyze clinical claims for rejection patterns.
    
    Args:
        claims_data: List of claim dictionaries with diagnosis/procedure codes
        
    Returns:
        Dictionary with rejection patterns and recommendations
        
    Raises:
        ValueError: If claims_data is empty or malformed
    """
```

### TypeScript/JavaScript (If Used)

**Naming:**
- `camelCase` for functions, variables, properties
- `PascalCase` for classes and components
- `UPPER_SNAKE_CASE` for constants/enums
- Prefix interfaces with `I` (optional but recommended)

**Structure:**
```typescript
// File organization
src/
├── components/      # React/Vue components
├── services/        # API calls, business logic
├── utils/           # Shared utilities
├── types/           # TypeScript interfaces
├── styles/          # CSS/SCSS
└── config.ts        # Configuration
```

**Type Safety:**
- Always use explicit types (no `any`)
- Use `interface` for objects, `type` for unions
- Export types from a dedicated types file

---

## 🏗️ Architecture Patterns

### API Design (Backend)

**Principle:** One unified FastAPI (or Express) application, organized by domain routers.

```
services/
└── api/
    ├── routers/
    │   ├── clinical.py        # Clinical operations endpoints
    │   ├── patient_access.py   # Appointment, reminders
    │   ├── revenue_cycle.py    # Billing, claims
    │   ├── compliance.py       # HIPAA/GDPR auditing
    │   ├── hr.py              # HR, CME tracking
    │   └── executive.py       # KPI dashboards
    ├── models/
    │   ├── clinical.py
    │   ├── billing.py
    │   └── patient.py
    ├── services/
    │   └── [business logic]
    └── main.py
```

**Routing Convention:**
- `/api/v1/clinical/...` - Clinical operations
- `/api/v1/patients/...` - Patient data
- `/api/v1/claims/...` - Billing & revenue
- `/api/v1/compliance/...` - Compliance monitoring
- `/api/v1/hr/...` - Human resources
- `/api/v1/executive/...` - Executive dashboards

### Frontend Architecture (UI)

**Public Website** (`uis/website/`):
- Patient-facing features (booking, reminders, health info)
- Public information, landing pages
- Authentication for patient accounts

**Backoffice** (`uis/backoffice/`):
- Internal staff portal (clinical, operations, billing)
- Dashboards and monitoring
- Administrative tools

### Data Pipelines

**Organization:**
```
data/
├── raw/        # Original data sources (read-only)
├── process/    # Processing scripts and logic
├── pipelines/  # Scheduled ETL jobs
└── eval/       # Datasets for evaluation/testing
```

**Convention:** Data flows unidirectionally → `raw` → `process` → `pipelines` → `services` (API)

---

## 🔐 Data Handling & Compliance

### HIPAA (US) & UK GDPR Requirements

**All code that touches patient data must:**
1. ✅ Assume patient data is **Protected Health Information (PHI)**
2. ✅ Implement **access control** (who can see what)
3. ✅ Maintain **audit logs** (who accessed what, when)
4. ✅ Support **data deletion** (right to be forgotten)
5. ✅ Encrypt **sensitive fields** at rest and in transit
6. ✅ Never log or expose PHI in error messages

### Classification of Data

| Data Type | Sensitivity | Handling |
|-----------|-------------|----------|
| Patient Name, DOB, SSN, MRN | 🔴 High | Encrypted at rest, minimal logs |
| Diagnosis, Procedures, Medications | 🟠 High | Encrypted, audit trail required |
| Appointment times, clinic location | 🟡 Medium | Regular database, logged access |
| Anonymized/aggregated metrics | 🟢 Low | Can be freely logged and analyzed |

### Code Example (Python)

```python
# ✅ GOOD - Respects data sensitivity
def analyze_claims(claims_data: list[dict]) -> dict:
    """Analyze claims without exposing PHI."""
    # Remove all PII before processing
    sanitized_claims = [
        {
            'claim_id': c['claim_id'],  # Safe to log
            'diagnosis_code': c['diagnosis_code'],  # Code, not full history
            'rejection_reason': c['rejection_reason']  # Safe to log
        }
        for c in claims_data
    ]
    # Analysis happens on sanitized data
    return analyze_patterns(sanitized_claims)

# ❌ BAD - Exposes PHI in error handling
def process_patient_record(patient: dict) -> dict:
    try:
        return update_record(patient)
    except Exception as e:
        logger.error(f"Failed to process patient {patient['name']}: {e}")  # Logs name!
        raise
```

### Documentation

All data handling rules are detailed in [`.agents/compliance-rules.md`](./.agents/compliance-rules.md).

---

## ✅ Testing & Quality Assurance (TBD)

**Status:** To be defined during project development.

**Placeholder decisions:**
- Testing framework: `pytest` (Python) or `Jest` (Node)?
- Linting: `black` + `flake8` (Python) or `ESLint` (Node)?
- Pre-commit hooks: `pre-commit` or `husky`?
- CI/CD: GitHub Actions?
- Coverage threshold: 80%+?

**Will be documented in:** `.github/workflows/` and `AGENTS.md` (validation step before PR)

---

## 🚀 Deployment & Infrastructure

**Current Setup:**
- Docker containers for local development (see `docker-compose.yml`)
- Dev container support (`.devcontainer/`)
- Database: TBD (PostgreSQL candidate for structured data)

**To be defined:**
- Staging vs. production environments
- Kubernetes orchestration (if needed)
- Scaling policies
- Monitoring and alerting

---

## 🛠️ Tools & Development Environment

### Local Development

**Required:**
- Git
- Docker & Docker Compose
- Node.js (if frontend/TypeScript involved)
- Python 3.11+ (if backend/AI involved)
- VS Code (recommended) with appropriate extensions

**Recommended:**
- Prettier (code formatting)
- ESLint (JS/TS linting)
- Black (Python formatting)
- Pre-commit hooks

### Version Management

| Tool | Version | Purpose |
|------|---------|---------|
| Node.js | 18+ LTS | JavaScript/TypeScript runtime |
| Python | 3.11+ | Backend, data, AI |
| Docker | Latest | Containerization |

---

## 🔗 Related Documents

- **Business Context:** [`CONTEXT.md`](../CONTEXT.md)
- **Agent Operations:** [`AGENTS.md`](../AGENTS.md)
- **Technical Conventions:** [`.agents/`](./.agents/)
- **Compliance Rules:** [`.agents/compliance-rules.md`](./.agents/compliance-rules.md)

---

## ❓ Questions or Clarifications?

If a technical decision is unclear:
1. Check the linked documents above
2. If still unclear, it's TBD — add it to this file with "(TBD)" label
3. Decisions will be formalized as the project progresses

**Last decision review:** 2026-11-25
