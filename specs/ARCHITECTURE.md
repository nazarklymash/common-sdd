# ARCHITECTURE

> Owned by: **L1 Architect** (`prompts/L1_ARCHITECT.md`).  
> Do not implement code here. Update only via L1 (or human + ADR).

## 1. Architecture Overview

- **Core System Goal:** _[fill]_
- **Non-Goals:** _[fill]_
- **Hardware & Performance Bounds:** _[latency, memory, throughput, platforms]_
- **Target Standards:** _[e.g. C++20 / Rust 2021 / TypeScript 5.x]_

## 2. System Diagrams

### 2.1 Topology

```mermaid
graph TD
  %% L1 replaces this with the real topology
  A[Client] --> B[MOD-01]
  B --> C[MOD-02]
```

### 2.2 Data & Control Flow

```mermaid
sequenceDiagram
  participant A as Client
  participant M1 as MOD-01
  participant M2 as MOD-02
  A->>M1: request
  M1->>M2: call
  M2-->>M1: result
  M1-->>A: response
```

## 3. Cross-Cutting Constraints

- Memory ownership: _[who allocates / frees]_
- Threading / concurrency model: _[single-thread / pool / async]_
- Error model: _[exceptions / Result / error codes — point to contracts]_
- Security / trust boundaries: _[fill]_

## 4. Directives for Level 2 Tech Lead

- High-risk bottlenecks and algorithms:
- Memory ownership, thread-safety, and execution order:
- Modules that must share types via `specs/contracts/`:

## 5. Change Log

| Date | Author | Summary |
|------|--------|---------|
| | L1 | Initial scaffold |
