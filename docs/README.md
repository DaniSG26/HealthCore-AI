# HealthCore Documentation Index

This folder contains all documentation for the HealthCore AI Engineering project.

---

## 📚 Core Documents (Read These First)

### For Everyone (Agents & Humans)

| Document | Purpose | Audience |
|----------|---------|----------|
| [`../CONTEXT.md`](../CONTEXT.md) | **Business context & company briefing** — What is HealthCore, what problems we solve, which departments exist | Everyone |
| [`../CONTEXT.es.md`](../CONTEXT.es.md) | Business context in Spanish | Spanish speakers |
| [`../AGENTS.md`](../AGENTS.md) | **Mandatory workflow for AI agents** — How to operate, PR process, what's prohibited | AI Agents + Developers |
| [`TECHNICAL_CONTEXT.md`](./TECHNICAL_CONTEXT.md) | **Technical decisions & conventions** — Stack, code style, architecture patterns, compliance rules | Developers + AI Agents |
| [`../.agents/`](../.agents/) | **Specific technical conventions** — Python/TypeScript rules, data handling, folder structure | Developers + AI Agents |

### For AI Agents Specifically

If you're an AI agent working on this repository, **READ IN THIS ORDER:**

1. 📄 [`../CONTEXT.md`](../CONTEXT.md) — Business context
2. 📄 [`TECHNICAL_CONTEXT.md`](./TECHNICAL_CONTEXT.md) — Technical context
3. 📄 [`../AGENTS.md`](../AGENTS.md) — Agent workflow
4. 📁 [`../.agents/`](../.agents/) — Specific conventions
5. 📁 [`../skills/`](../skills/) — Available skills

**→ Start here:** [`../.copilot-instructions.md`](../.copilot-instructions.md)

---

## 🛠️ Technical Architecture

### Services & APIs

**Location:** [`../services/`](../services/)

- **Purpose:** Backend APIs serving the entire company
- **Status:** In development
- **To be documented:** Endpoint reference, service dependencies, deployment guides

### User Interfaces

**Location:** [`../uis/`](../uis/)

- **Purpose:** Patient-facing & internal admin applications
- **Status:** In planning
- **To be documented:** UI frameworks, component architecture, design system

### Data Pipelines & Processing

**Location:** [`../data/`](../data/)

- **Purpose:** Data collection, cleaning, analysis, evaluation
- **Status:** Framework defined (raw → process → pipelines → eval)
- **To be documented:** ETL workflows, data schemas, lineage

### Agents & AI

**Location:** [`../agents/`](../agents/), [`../skills/`](../skills/), [`../mcps/`](../mcps/)

- **Purpose:** AI agents, reusable skills, MCP tool servers
- **Status:** Skill templates ready
- **To be documented:** Agent capabilities, integration patterns, testing

### Shared Resources

**Location:** [`../packages/shared/`](../packages/shared/)

- **Purpose:** Shared types, SDKs, schemas across all services
- **Status:** TypeScript type definitions ready (`@repo/shared-types`)
- **To be documented:** Type reference, usage examples

---

## 🔐 Compliance & Security

**Key Rules:**
- All patient data is Protected Health Information (PHI)
- HIPAA compliance required for US operations
- UK GDPR compliance required for UK operations
- Never expose PHI in logs, error messages, or non-audit contexts

**Read:** [`TECHNICAL_CONTEXT.md#-data-handling--compliance`](./TECHNICAL_CONTEXT.md#-data-handling--compliance)

---

## 🎯 Available Skills

**Location:** [`../skills/`](../skills/)

Current skills:
- `clinical-claims-analysis` — Analyze rejection patterns in medical claims

More skills to be added as the project grows.

---

## 🏗️ Folder Structure Reference

```
HealthCore-AI/
├── CONTEXT.md / CONTEXT.es.md         ← Business context (read first!)
├── AGENTS.md                          ← Agent workflow
├── .copilot-instructions.md           ← Instructions for AI agents
├── .agents/                           ← Technical conventions
│   ├── python-conventions.md
│   ├── typescript-conventions.md
│   ├── compliance-rules.md
│   ├── data-handling.md
│   └── folder-structure.md
├── docs/                              ← Documentation (you are here)
│   ├── README.md
│   └── TECHNICAL_CONTEXT.md           ← Technical decisions
├── services/                          ← Backend APIs
├── uis/                               ← Frontends
├── data/                              ← Datasets & pipelines
├── agents/                            ← AI agents
├── skills/                            ← Reusable agent skills
├── packages/shared/                   ← Shared types
└── [other folders per monorepo structure]
```

---

## 🚀 Getting Started

### If you're a developer:
1. Clone the repository
2. Read [`CONTEXT.md`](../CONTEXT.md)
3. Read [`TECHNICAL_CONTEXT.md`](./TECHNICAL_CONTEXT.md)
4. Read [`.agents/`](../.agents/)
5. Pick a folder from the table above and start coding

### If you're an AI agent:
1. Read [`../.copilot-instructions.md`](../.copilot-instructions.md)
2. Follow the 5-step reading process
3. Follow the workflow in [`../AGENTS.md`](../AGENTS.md)
4. Never push directly to main

---

## 📋 Decision Log

**Last reviewed:** 2026-11-25

| Decision | Status | Rationale |
|----------|--------|-----------|
| Use monorepo structure | Active | Single company across many milestones |
| One unified backend API | Active | Avoid microservices complexity early |
| Centralized git flow (feature branches + PRs) | Active | Quality gate for agent work |
| Data classification (PHI, PII, etc.) | Active | Compliance requirement |
| Stack decisions (Python, Node, etc.) | TBD | To be decided during development |
| Testing/CI framework | TBD | To be decided during development |

---

## ❓ Common Questions

**Q: Where do I put a new service?**  
A: [`../services/`](../services/) — Read the README there first

**Q: Where do new agents go?**  
A: [`../agents/`](../agents/) — One agent per subfolder

**Q: I want to create a new skill, where?**  
A: [`../skills/`](../skills/) — Use the `_template/` as a guide

**Q: What about frontend code?**  
A: [`../uis/website/`](../uis/website/) for public, [`../uis/backoffice/`](../uis/backoffice/) for internal

**Q: I found an error in a document, what do I do?**  
A: Create a PR with the fix — documentation is code too

---

## 🔗 External Resources

- **4Geeks Academy:** https://4geeksacademy.com
- **HealthCore Business Context:** See [`CONTEXT.md`](../CONTEXT.md)
- **GitHub:** Repository + PR process

---

**Last updated:** 2026-11-25
