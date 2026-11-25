# AGENTS.md — How AI Agents Operate in HealthCore

**Purpose:** Define the mandatory workflow and rules for all AI agents operating in this monorepo.

**Status:** Active  
**Last updated:** 2026-11-25

---

## 📖 Table of Contents

1. [What is an Agent?](#what-is-an-agent)
2. [Mandatory Workflow](#mandatory-workflow)
3. [The 5-Document Rule](#the-5-document-rule)
4. [Prohibited Actions](#prohibited-actions)
5. [When to Use Skills vs. Write Custom Code](#when-to-use-skills-vs-write-custom-code)
6. [PR Checklist](#pr-checklist)
7. [Error Handling & Escalation](#error-handling--escalation)

---

## 🤖 What is an Agent?

An **agent** is an AI system that:
- ✅ Reads code and documentation to understand context
- ✅ Makes decisions about what to build based on requirements
- ✅ Writes code, scripts, documentation, or infrastructure
- ✅ Follows a process BEFORE committing changes
- ✅ Operates within explicit constraints and rules

An agent is **NOT:**
- ❌ A tool that writes code without understanding
- ❌ A system that pushes directly to main without review
- ❌ A tool that ignores compliance or security rules
- ❌ A system that makes up folder structures or conventions

---

## 🔄 Mandatory Workflow

Every agent must follow this workflow **before ANY commit to this repository**.

### Step 1: Read the Context Documents (5 minutes)

**Before touching ANY code, read these in order:**

```
1. CONTEXT.md                 → Business context (what & why)
2. docs/TECHNICAL_CONTEXT.md  → Technical decisions (how)
3. AGENTS.md                  → This file (the process)
4. .agents/*                  → Specific conventions
5. skills/                    → Available skills
```

**Why?** Every decision the agent makes must align with:
- HealthCore's business goals
- Technical decisions already made
- Compliance requirements (HIPAA, UK GDPR)
- Code conventions the team will recognize

**Proof of reading:** Agent can cite specific sections from these documents in PR description.

---

### Step 2: Create a Feature Branch (1 minute)

```bash
git checkout -b feature/[feature-name]
# Example: feature/claims-analysis-skill
# NOT: main, production, or any other branch
```

**Why?** Prevents accidental changes to main. Allows review before merge.

---

### Step 3: Write Code / Documentation (Variable)

Agent writes code, scripts, or documentation according to:
- Requirements stated in the task
- Conventions from `.agents/` folder
- Code style from `TECHNICAL_CONTEXT.md`
- Business logic from `CONTEXT.md`

**During coding, agent must:**
- ✅ Ask clarifying questions if requirements are unclear
- ✅ Check compliance rules before handling patient data
- ✅ Follow the folder structure (read `.agents/folder-structure.md`)
- ✅ Use available skills where appropriate (don't reinvent)
- ✅ Comment code for complex logic

---

### Step 4: Validate Against Conventions (5 minutes)

**Checklist (agent runs this mentally or with tools):**

| Check | How | Required? |
|-------|-----|-----------|
| Code follows naming conventions | Read `.agents/python-conventions.md` or `.agents/typescript-conventions.md` | ✅ Yes |
| No compliance rules broken | Read `.agents/compliance-rules.md` | ✅ Yes |
| Data handling is correct | Read `.agents/data-handling.md` | ✅ Yes |
| Folder structure preserved | Read `.agents/folder-structure.md` | ✅ Yes |
| Linting passes | Run local linter (if configured) | ⏳ TBD |
| Tests pass | Run local tests (if configured) | ⏳ TBD |

**If any check fails:** Stop, fix the issue, re-validate.

---

### Step 5: Write Clear PR Description (5 minutes)

**PR title format:**
```
[Category] Brief description

Examples:
- [Feature] Add clinical claims analysis skill
- [Fix] Correct HIPAA compliance rule in data-handling.md
- [Docs] Update TECHNICAL_CONTEXT.md with deployment guide
```

**PR description must include:**

```markdown
## 📋 Summary
One paragraph explaining what changed and why.

## 🎯 Objective
What problem does this solve? Link to CONTEXT.md if relevant.

## 📝 Changes
- Bullet point for each change
- Clear and specific

## 🧪 Testing
How did you verify this works? (even if no automated tests yet)
- Tested manually with [example data]
- Reviewed against [specific convention]
- Validated compliance with [specific rule]

## ⚠️ Compliance & Security
- Does this handle patient data? How?
- Any HIPAA or UK GDPR concerns? None / [describe precautions]
- Any breaking changes? None / [describe]

## 🔗 References
- Read CONTEXT.md section X
- Followed .agents/compliance-rules.md guideline Y
- Used skill: clinical-claims-analysis (if applicable)
```

**Why detailed PRs?**
- Helps humans understand why the agent made decisions
- Creates a record of reasoning
- Catches mistakes before merge

---

### Step 6: Create PR (Don't Merge) (1 minute)

```bash
git push origin feature/[feature-name]
# Then create PR through GitHub UI or CLI
# 🚫 DO NOT merge yourself
```

**Why wait for review?**
- Humans catch edge cases agents might miss
- Compliance rules are verified manually
- Architecture decisions are validated

---

### Step 7: Respond to Feedback (Variable)

If a human reviewer asks for changes:
1. ✅ Acknowledge the feedback
2. ✅ Ask clarifying questions if needed
3. ✅ Make the requested changes
4. ✅ Push updates to the same feature branch
5. ✅ Comment on the PR explaining the fix
6. ✅ Wait for final approval

---

### Step 8: Merge & Complete (1 minute)

Once approved by a human:
1. ✅ Merge the PR (human does this)
2. ✅ Delete the feature branch
3. ✅ Confirm changes are live

---

## 📖 The 5-Document Rule

An agent **must have read and understood** these 5 documents before making ANY decision:

### 1. CONTEXT.md (Business)
**File:** [`CONTEXT.md`](./CONTEXT.md)

**Questions it answers:**
- What is HealthCore?
- What problems does each department have?
- Why do we build what we build?

**Agent must cite:** A specific problem from a specific department.

---

### 2. TECHNICAL_CONTEXT.md (Architecture)
**File:** [`docs/TECHNICAL_CONTEXT.md`](./docs/TECHNICAL_CONTEXT.md)

**Questions it answers:**
- What stack do we use?
- How is code organized?
- What conventions apply?
- What's TBD vs. decided?

**Agent must cite:** A specific convention or architecture pattern.

---

### 3. AGENTS.md (Process)
**File:** This file

**Questions it answers:**
- What workflow must I follow?
- What's prohibited?
- What must my PR include?

**Agent must cite:** The workflow step it's on.

---

### 4. .agents/ (Specific Rules)
**Files:** [`.agents/`](./.agents/)

Includes:
- `python-conventions.md` — Python naming, structure, docstrings
- `typescript-conventions.md` — TypeScript naming, structure, types
- `compliance-rules.md` — HIPAA, UK GDPR, never break these
- `data-handling.md` — What data is sensitive, how to handle it
- `folder-structure.md` — Where things go, don't innovate

**Agent must cite:** A specific rule from at least one of these files.

---

### 5. skills/ (Available Capabilities)
**Folder:** [`skills/`](./skills/)

**Questions it answers:**
- What pre-made capabilities exist?
- When should I use a skill vs. write custom code?
- How do skills work?

**Agent must decide:** "Do I need to write this from scratch, or can I combine existing skills?"

---

## 🚫 Prohibited Actions

| Action | Why It's Prohibited | What to Do Instead |
|--------|-------------------|-------------------|
| **Push directly to main** | No review gate, can break things | Create feature branch + PR |
| **Merge own PR** | No external validation | Wait for human approval |
| **Change folder structure** | Breaks conventions, confuses team | Read `.agents/folder-structure.md`, propose changes in a separate meta-discussion |
| **Ignore compliance rules** | Violates HIPAA/GDPR, legal liability | Read `.agents/compliance-rules.md`, escalate if unsure |
| **Expose patient data in logs** | Violates HIPAA, security breach | Read `.agents/data-handling.md`, sanitize before logging |
| **Copy-paste code without review** | May introduce bugs, inconsistencies | Understand why the code works before using it |
| **Write vague PR descriptions** | No context for review | Follow PR checklist in Step 5 above |
| **Skip the 5-document rule** | May violate requirements, conventions | Read all 5 before starting |
| **Write code that doesn't match conventions** | Inconsistent with team work | Check `.agents/[language]-conventions.md` before coding |

---

## 🛠️ When to Use Skills vs. Write Custom Code

**Decision tree:**

```
Task arrives
  │
  ├─→ Is this a known, recurring task?
  │   ├─→ YES: Check skills/ folder
  │   │   ├─→ Skill exists and fits my task? Use it!
  │   │   └─→ Skill doesn't exist? Write custom code
  │   └─→ NO: Write custom code
  │
  └─→ Continue workflow
```

**Examples:**

| Task | Skill Available? | Action |
|------|-----------------|--------|
| Analyze rejection patterns in clinical claims | ✅ Yes (`clinical-claims-analysis`) | Use the skill |
| Create a new department service API | ❌ No | Write custom code |
| Process patient appointment data | ✅ Possibly (depends on scope) | Check the skill first |
| Write compliance audit report | ✅ Possibly (depends on scope) | Check the skill first |

---

## ✅ PR Checklist

Before creating a PR, the agent must verify:

**Workflow:**
- ✅ Read the 5 documents (CONTEXT, TECHNICAL_CONTEXT, AGENTS, .agents/, skills/)
- ✅ Created feature branch (not main)
- ✅ All changes are in the feature branch
- ✅ Cited specific documents in PR description

**Code Quality:**
- ✅ Code follows naming conventions (`.agents/[language]-conventions.md`)
- ✅ No compliance rules broken (`.agents/compliance-rules.md`)
- ✅ No patient data exposed in logs (`.agents/data-handling.md`)
- ✅ Folder structure preserved (`.agents/folder-structure.md`)
- ✅ No hardcoded secrets or credentials

**Documentation:**
- ✅ PR title clearly describes what changed
- ✅ PR description includes all sections (Summary, Objective, Changes, Testing, Compliance, References)
- ✅ Code comments explain complex logic
- ✅ README updated if new feature/module added

**Review Readiness:**
- ✅ PR is ready for human review (no "WIP" unless explicitly stated)
- ✅ No merge conflicts
- ✅ Related issues/PRs linked if applicable

---

## 🆘 Error Handling & Escalation

If an agent encounters a situation that doesn't fit the rules:

**Scenario 1: "The requirement contradicts a convention"**
```
Action:
1. Document the contradiction in the PR description
2. Ask for clarification in a PR comment
3. Wait for human guidance before proceeding
```

**Scenario 2: "The rule is unclear"**
```
Action:
1. Check the linked documents
2. If still unclear, it's probably TBD
3. Document your assumption in the PR
4. Proceed with best judgment
5. Wait for review to validate
```

**Scenario 3: "Data sensitivity is unclear"**
```
Action:
1. Read TECHNICAL_CONTEXT.md data classification table
2. If still unclear, assume PHI (most sensitive)
3. Encrypt, log access, avoid exposing in errors
4. Document the classification in code comments
5. Escalate in PR review if needed
```

**Scenario 4: "The PR is blocked waiting for feedback"**
```
Action:
1. Check if 48 hours have passed without response
2. Add a comment: "@reviewer Please check when possible"
3. Continue working on other tasks
4. Do NOT merge without approval
```

---

## 📋 Approval & Merge Authority

**Who can approve PRs?**
- 👤 Team leads (designated in GitHub settings)
- 👤 Humans with write access
- 🚫 NOT the agent that created the PR

**Who can merge?**
- 👤 Humans only (via GitHub UI or CLI)
- 🚫 NOT agents (even if they have permission)

---

## 🎯 Success Criteria

An agent's work is complete and successful when:

✅ **Process:** Followed the 8-step workflow without shortcuts  
✅ **Documents:** Read and cited all 5 required documents  
✅ **Code:** Follows conventions from `.agents/`  
✅ **Compliance:** Passes all checks in `.agents/compliance-rules.md`  
✅ **Communication:** PR description is clear and detailed  
✅ **Review:** Merged only after human approval  
✅ **Quality:** Code is production-ready and maintainable  

---

## 🔗 Related Documents

- **Business Context:** [`CONTEXT.md`](./CONTEXT.md)
- **Technical Context:** [`docs/TECHNICAL_CONTEXT.md`](./docs/TECHNICAL_CONTEXT.md)
- **Conventions:** [`.agents/`](./.agents/)
- **Skills:** [`skills/`](./skills/)
- **Agent Instructions:** [``.copilot-instructions.md``](./.copilot-instructions.md)

---

## ❓ Common Questions

**Q: Can an agent merge its own PR?**  
A: No. Always wait for human review and approval.

**Q: What if linting/testing is not set up yet?**  
A: That's TBD. Document what you tested manually in the PR.

**Q: Can an agent break conventions if it makes code shorter?**  
A: No. Conventions exist for team consistency, not optimization.

**Q: What if a skill is close but not exact?**  
A: Use the skill as a starting point, extend it, document the changes.

**Q: Who do I ask if I have questions?**  
A: Document questions in the PR, wait for review. This is feedback for humans too.

---

**Last updated:** 2026-11-25  
**Review this document:** Before starting any new agent task  
**Questions:** Add a comment in a PR or create an issue
