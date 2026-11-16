# TASK_SPECIFICATION: MOD-XX_ModuleName

> Copy to `TASK_MOD-XX_ModuleName.md`. Owned by **L2 Tech Lead**.  
> Do not redefine shared types — cite `specs/contracts/CONTRACTS.md`.

## 1. Context & Scope

- Module ID: MOD-XX
- Target Standard: _[from ARCHITECTURE]_
- Objective: _[short functional goal]_
- Dependencies: _[headers / crates / packages + other MOD-IDs]_
- Shared Contracts: `specs/contracts/CONTRACTS.md` §§ _

## 2. API Contract & Data Types

_[Concrete structs, classes, enums, method signatures. Import shared types by reference.]_

## 3. Algorithmic Logic & Edge Cases

- Step-by-Step Logic:
  1. …
  2. …
- Edge Cases & Error Handling:
  - Case A: _exact action / return code_
  - Case B: _exact action / fallback_

## 4. Performance & Execution Constraints

- Allocation limits, thread-safety, const-correctness, cache rules.

## 5. Source & Test Layout

- Source path: `src/...`
- Test path: `tests/...`
- Test framework: _[GoogleTest / Catch2 / pytest / Jest / …]_

## 6. Directives for L3 Coder

> Implement the complete source based on this contract. Production code ONLY — no TODOs, placeholders, or explanations. Do not change Section 2 signatures. Obey shared contracts.
