# 📋 FlowPay — Project Board Conventions

> **Canonical execution surface:** [GitHub Project Board](https://github.com/orgs/flowpay-system/projects/1)
>
> This document defines conventions for managing the live backlog, milestone language, item lifecycle, and the separation between active work and historical documentation.

---

## Board Name

**FlowPay — Protocol Execution Board**

---

## Separation of Concerns

| Surface | Purpose |
|---|---|
| GitHub Project Board | Live backlog, sprint planning, active execution tracking |
| `ROADMAP.md` | Historical phase planning reference (Mar/2026) — read-only |
| `archive/` | Archived docs from previous iterations — historical context only |
| `PROJECT_CONVENTIONS.md` (this file) | Canonical workflow reference for contributors |

---

## Milestone Language

All items on the board must use consistent status labels:

| Status | Meaning |
|---|---|
| `todo` | Not started; accepted into the backlog |
| `in-progress` | Actively being worked on |
| `in-review` | PR open / awaiting review or QA |
| `done` | Merged, deployed, or resolved |
| `blocked` | Cannot progress; explicit blocker noted in the item |
| `historical` | Preserved for reference; no longer actionable |

**Rules:**
- Every item must carry exactly one status at all times.
- `historical` is reserved for items migrated from legacy docs that are kept for traceability but will not be actioned.
- Items from `ROADMAP.md` phases or `archive/monolith-root/NEXTSTEPS.md` that are still relevant must be re-created as fresh board items (not copy-pasted status).

---

## Item Anatomy

Each board item must include:

```
Title       : <verb> + <object>  (e.g. "Implement vendor metrics dashboard")
Status      : one of the values above
Milestone   : sprint or phase label (e.g. "Sprint 4", "Phase 6: Open Spec")
Priority    : critical | high | medium | low
Owner       : @github-handle or "unassigned"
Description : what done looks like (acceptance criteria, not tasks)
```

---

## Backlog Triage

- New issues are created directly on GitHub Issues and linked to the board on triage.
- Triage happens at the start of each sprint (weekly, every Monday).
- Items in `todo` with no owner after two sprints are moved to `backlog` (a secondary pool) and re-evaluated quarterly.

---

## Historical Document Policy

Documents that served as planning references but are no longer the active source-of-truth must:

1. Carry the `⚠️ CONTEXTO HISTÓRICO` banner at the top of the file.
2. Link to this `PROJECT_CONVENTIONS.md` and the live board.
3. Remain read-only in the repository (no further updates to content).
4. **Not** be deleted — they serve as a traceable record of past decisions.

Files currently classified as historical:

- [`ROADMAP.md`](./ROADMAP.md) — Phase 2–12 planning reference, Mar/2026
- [`archive/monolith-root/NEXTSTEPS.md`](./archive/monolith-root/NEXTSTEPS.md) — Post-deploy sprint notes from monolith era

---

## Contribution Workflow

```
1. Open a GitHub Issue (describe the problem or feature)
2. Issue gets triaged and added to the board with status=todo
3. Assignee picks up the item → status=in-progress
4. PR opened → status=in-review
5. PR merged & deployed → status=done
```

Never update `ROADMAP.md` or archived documents to reflect new work.
All execution state lives on the board.

---

## Contact

Core Architect: NΞØ MELLØ · <neo@neoprotocol.space>
