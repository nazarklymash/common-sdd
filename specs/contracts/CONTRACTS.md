# SHARED CONTRACTS

> Owned by: **L1 Architect**.  
> Any type / enum / error code / message ID used by **≥2 modules** lives here.  
> Task specs **reference** these definitions; they must not duplicate or diverge.

## 1. Version

- **Contract revision:** `0.1.0`
- **Compatible with architecture:** see `specs/ARCHITECTURE.md`

## 2. Common Types

```text
// Language-agnostic sketch — L1 fills concrete syntax for the target standard.
// Example:
// struct RequestId { uint64_t value; };
```

## 3. Error Codes / Results

| Code | Name | Meaning | Typical handler |
|------|------|---------|-----------------|
| 0 | OK | Success | continue |
| | | | |

## 4. Inter-Module Messages / DTOs

| DTO | Producer | Consumer(s) | Fields (name: type) |
|-----|----------|-------------|---------------------|
| | MOD-XX | MOD-YY | |

## 5. Invariants

- _[e.g. buffers are caller-owned; no cross-module raw pointer without lifetime note]_

## 6. Change Policy

1. L1 updates this file.
2. Write `reviews/adr/ADR-NNN_*.md`.
3. L2 re-issues every affected `TASK_MOD-XX_*.md`.
4. Re-run L3 Coder → Tester → L2 Reviewer for those modules.
