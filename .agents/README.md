# `.agents` Folder — Technical Conventions & Rules

This folder contains the specific technical conventions, rules, and guidelines that all code and agents in the HealthCore monorepo must follow.

**Purpose:** Centralize rules by topic so agents can quickly find "can I do X?" before coding.

---

## 📋 Documentation by Topic

### For Language-Specific Code

| Document | Purpose | When to Read |
|----------|---------|--------------|
| [`python-conventions.md`](./python-conventions.md) | Python naming, structure, imports, docstrings | Before writing Python code |
| [`typescript-conventions.md`](./typescript-conventions.md) | TypeScript naming, structure, types, React components | Before writing TypeScript code |

### For Data & Compliance

| Document | Purpose | When to Read |
|----------|---------|--------------|
| [`data-handling.md`](./data-handling.md) | How to classify and handle sensitive data (PHI, PII) | Before touching any patient/user data |
| [`compliance-rules.md`](./compliance-rules.md) | HIPAA, UK GDPR, audit requirements, never-break rules | Before any code review |

### For Organization

| Document | Purpose | When to Read |
|----------|---------|--------------|
| [`folder-structure.md`](./folder-structure.md) | Where things go, what not to change, structure rules | Before creating new folders/files |

---

## 🎯 Quick Reference

### "Can I do X?"

| Question | Answer | File to Read |
|----------|--------|--------------|
| Use `camelCase` for Python functions? | ❌ No, use `snake_case` | `python-conventions.md` |
| Create a new folder for a new service? | ✅ Yes, follow the pattern | `folder-structure.md` |
| Log a patient's name in an error? | ❌ No, violates HIPAA | `data-handling.md` |
| Use `any` in TypeScript? | ⚠️ Only if absolutely necessary | `typescript-conventions.md` |
| Encrypt patient emails? | ✅ Yes, they're PII | `data-handling.md` |
| Push directly to main? | ❌ No, see AGENTS.md | (refer to ../AGENTS.md) |

---

## 🔗 Related Documents

- **Agent Workflow:** [`../AGENTS.md`](../AGENTS.md)
- **Technical Context:** [`../docs/TECHNICAL_CONTEXT.md`](../docs/TECHNICAL_CONTEXT.md)
- **Business Context:** [`../CONTEXT.md`](../CONTEXT.md)
- **Instructions for Agents:** [`../.copilot-instructions.md`](../.copilot-instructions.md)

---

## 📝 How to Update These Rules

If a rule needs to be:
- **Clarified:** Edit the relevant document, create a PR
- **Changed:** Discuss in a PR, wait for team consensus
- **Added:** Create a new `.md` file, link it here, create a PR

All changes to these documents require review before merge.

---

**Last updated:** 2026-11-25
