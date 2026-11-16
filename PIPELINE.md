# SDD Pipeline — Conductor Contract

This file is the single source of truth for **who runs when**, **which files they own**, and **how rework loops**. Agents must not invent alternate paths.

## 0. Roles & Models

| Level | Role | Prompt | Suggested model | Owns (write) | Reads |
|-------|------|--------|-----------------|--------------|-------|
| L1 | Architect | `prompts/L1_ARCHITECT.md` | Largest available, **strong quant** (avoid Q2 for design) | `specs/ARCHITECTURE.md`, `specs/MODULE_MATRIX.md`, `specs/contracts/**` | User brief / product goals |
| L2 | Tech Lead | `prompts/L2_TECH_LEAD.md` | Mid-size, high-quality quant (e.g. Q5–Q6) | `specs/TASK_MOD-XX_Name.md` | Architecture + contracts + matrix |
| L3 | Coder | `prompts/L3_CODER.md` | Small coder model OK if specs are concrete | `src/**` matching the task | One `TASK_*.md` + contracts it cites |
| L3 | Tester | `prompts/L3_TESTER.md` | Same family as Coder | `tests/**` | Task spec + `src/` for that module |
| L2 | Reviewer | `prompts/L2_REVIEWER.md` | Mid-size | `reviews/REVIEW_MOD-XX.md` | Spec + src + tests |
| L1 | Auditor | `prompts/L1_AUDITOR.md` | Same as Architect | `reviews/SYSTEM_AUDIT.md`, ADR under `reviews/adr/` | All `REVIEW_*.md` + architecture + contracts |

**Cursor IDE:** `.cursor/rules/` are thin helpers for *manual* sessions in the IDE. They must **not** replace this pipeline. Do not run L1–L3 personas via overlapping always-on rules on the same globs.

## 1. Naming Convention (strict)

| Artifact | Pattern | Example |
|----------|---------|---------|
| Architecture | `specs/ARCHITECTURE.md` | fixed name |
| Module matrix | `specs/MODULE_MATRIX.md` | fixed name |
| Shared contracts | `specs/contracts/CONTRACTS.md` (+ optional `MOD-XX.md`) | — |
| Task spec | `specs/TASK_MOD-XX_SnakeOrPascalName.md` | `TASK_MOD-01_PacketParser.md` |
| Source | `src/<module_path>/` as declared in the task | — |
| Tests | `tests/<mirror_or_named>/` | `tests/MOD-01/` or mirror of `src/` |
| Module review | `reviews/REVIEW_MOD-XX.md` | `REVIEW_MOD-01.md` |
| System audit | `reviews/SYSTEM_AUDIT.md` | fixed name |
| ADR | `reviews/adr/ADR-NNN_short_title.md` | `ADR-001_buffer_ownership.md` |

Module IDs are always `MOD-NN` (zero-padded, from the matrix). Never invent IDs not in `MODULE_MATRIX.md`.

## 2. Forward Flow (Top-Down Generation)

```text
[Human brief]
    → L1 Architect
         writes ARCHITECTURE.md, MODULE_MATRIX.md, contracts/**
    → [HUMAN GATE G1: approve architecture]
    → For each module in matrix (dependency order):
         L2 Tech Lead → TASK_MOD-XX_*.md
         → [HUMAN GATE G2 optional / batch OK]
         L3 Coder → src/**
         L3 Tester → tests/**
         L2 Reviewer → REVIEW_MOD-XX.md
         → if REJECTED: rework loop (§3)
         → if APPROVED: next module
    → L1 Auditor → SYSTEM_AUDIT.md (+ ADRs if needed)
    → [HUMAN GATE G3: ship / integrate]
```

### Dependency order
Process modules so that producers of shared contracts land before consumers. Order is listed in `MODULE_MATRIX.md` column **Build Order**.

### One-module rule
L3 Coder and L3 Tester handle **exactly one** `TASK_*.md` per invocation. No cross-module refactors unless a new L2 task says so.

## 3. Reject / Rework Loop

On `reviews/REVIEW_MOD-XX.md` with **Status: REJECTED**:

1. L2 Reviewer lists numbered fixes (file + symbol or section + exact required change).
2. L3 Coder applies **only** those fixes; must not change API beyond the original task unless the review explicitly amends the task (then L2 must update `TASK_*.md` first).
3. L3 Tester updates/extends tests for touched edge cases.
4. L2 Reviewer re-audits → new status in the same `REVIEW_MOD-XX.md` (append a **Revision History** section).

### Limits
| Parameter | Value |
|-----------|-------|
| Max L3 rework cycles per module | **3** |
| After 3× REJECTED | Stop. Escalate to L1 Architect with review + task + architecture excerpts |
| L1 structural change | Update architecture/contracts, bump matrix if needed, write ADR, re-issue affected `TASK_*.md` via L2 |

Do not silently keep regenerating past the limit.

## 4. Human Gates

| Gate | When | Pass criteria |
|------|------|----------------|
| G1 | After L1 | Goals, module list, and cross-module contracts look correct |
| G2 | After L2 (optional) | Task API + edge cases are implementable without guessing |
| G3 | After L1 Auditor | All targeted modules APPROVED; SYSTEM_AUDIT says READY or READY_WITH_ADRS |

Agents never mark the system “done” without G3 (or an explicit human override noted in `SYSTEM_AUDIT.md`).

## 5. File Ownership (no cross-writes)

| Writer | Must NOT write |
|--------|----------------|
| L1 Architect | `src/`, `tests/`, `TASK_*.md`, `REVIEW_*.md` |
| L2 Tech Lead | `src/`, `tests/`, architecture (except citing), reviews |
| L3 Coder | specs/, reviews/, tests/ |
| L3 Tester | specs/, src/ (read-only), reviews/ |
| L2 Reviewer | src/, tests/, specs/ (read-only unless amending task after escalate) |
| L1 Auditor | src/, tests/, TASK_*.md |

## 6. Shared Contracts Rule

- Any type, enum, error code, or message ID used by **more than one** module MUST live under `specs/contracts/`.
- Task specs **reference** contracts; they do not redefine them.
- Changing a shared contract requires: L1 update → ADR → L2 re-issue of all affected tasks → re-run L3+review for those modules.

## 7. Cursor Rules Policy

| Rule file | When it applies | Persona |
|-----------|-----------------|---------|
| `01-task-execution.mdc` | Editing `specs/TASK_*.md` or implementing from a named task | L3 Coder only |
| `02-code-review.mdc` | Editing `tests/**` or `reviews/**` | Tester / Reviewer helpers |

Never attach both personas to the same glob set for `src/**`. Implementation sessions use rule 01; audit sessions use rule 02. Pipeline runs should paste the matching `prompts/*.md` as the system prompt and ignore the other.

## 8. Definition of Done (per module)

- [ ] `TASK_MOD-XX_*.md` exists and cites contracts
- [ ] `src/` implements Section 2 API with no TODOs
- [ ] `tests/` cover Section 3 edge cases and are runnable
- [ ] `REVIEW_MOD-XX.md` Status = **APPROVED**
- [ ] Rework cycle count ≤ 3

## 9. Definition of Done (system)

- [ ] All matrix modules in scope are module-Done
- [ ] `reviews/SYSTEM_AUDIT.md` Status = **READY** or **READY_WITH_ADRS**
- [ ] Human Gate G3 recorded
