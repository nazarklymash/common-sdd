# MODULE_MATRIX

> Owned by: **L1 Architect**.  
> L2 may only create `TASK_MOD-XX_*.md` for IDs listed here.  
> **Build Order** = pipeline execution order (producers before consumers).

| Build Order | Module ID | Module Name | Responsibility | Inputs / Dependencies | Outputs / Artifacts | Shared Contracts |
|-------------|-----------|-------------|----------------|-----------------------|---------------------|------------------|
| 1 | MOD-01 | _Name_ | _Responsibility_ | _Deps_ | _Artifacts_ | `contracts/CONTRACTS.md` §… |
| 2 | MOD-02 | _Name_ | _Responsibility_ | MOD-01, … | _Artifacts_ | … |

## Notes

- Add/remove rows only via L1 + ADR if the system already has APPROVED modules.
- `Shared Contracts` column must name the file/section; do not redefine types in task specs.
