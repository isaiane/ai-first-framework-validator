# Structure Rules (AI-FIRST)

## Purpose

Define structural rules so that humans and AI agents can:
- locate authoritative artifacts via paths in this document
- identify which layer owns which responsibility
- modify the system without creating duplicate or conflicting sources of truth

These rules are **framework-level** and remain **agnostic to product, stack, and runtime**.

---

## 1. Canonical Layers

Every compliant repository MUST organize artifacts into the following logical layers. Framework governance path names MAY vary; the Source of Truth MUST use the path `source-of-truth/` as specified below.

### 1.1 Framework Governance

Purpose: define how agents operate.

Typical artifacts:
- `AGENTS.md`
- `framework/principles/*`
- `framework/protocols/*`
- `skills/*`

Constraints:
- MUST NOT contain product-specific logic
- MUST NOT duplicate Source of Truth semantics

---

### 1.2 Source of Truth (SoT)

Purpose: define the system completely and deterministically.

Root: `source-of-truth/`

Required subdirectories:
- `source-of-truth/_meta/`
- `source-of-truth/contracts/`
- `source-of-truth/behavior/`
- `source-of-truth/acceptance/`
- `source-of-truth/scenarios/`

Constraints:
- MUST be the single authoritative definition of system behavior
- MUST meet every structural requirement in `references/validation-checklist.md` sections 2–11
- MUST NOT depend on runtime or tooling

---

### 1.3 Examples (Optional)

Purpose: demonstrate correct instantiation of the framework.

Location:
- `examples/*`

Constraints:
- MUST NOT redefine framework rules
- MUST NOT introduce alternative semantics
- MUST be derivable from the framework and the SoT at `source-of-truth/`

---

### 1.4 Implementation (Optional)

Purpose: concrete system built from the SoT.

Examples:
- application code
- services
- infrastructure

Constraints:
- MUST derive behavior from `source-of-truth/`
- MUST NOT introduce new business rules outside `source-of-truth/`

---

## 2. Boundary Rules

### 2.1 No Cross-Layer Authority Leakage

- Governance MUST NOT define system behavior
- Implementation MUST NOT define business rules
- Examples MUST NOT redefine contracts or behavior

Violation of this rule is BLOCKING.

---

### 2.2 No Duplicate Authority

A concept MUST have exactly one authoritative location.

Examples:
- acceptance criteria → `source-of-truth/acceptance/*`
- system behavior → `source-of-truth/behavior/*`
- schemas → `source-of-truth/contracts/*`

If the same concept appears in multiple places with normative weight, this is a BLOCKING issue.

---

### 2.3 Templates vs Instances

- `*.template.*` files are authoritative when defined as such
- derived files MUST NOT override templates
- protocols and skills MUST point to the authoritative artifact only

---

## 3. Naming and Discoverability

### 3.1 Role-Oriented Naming

Directories and files MUST reflect function, not technology.

Correct:
- `contracts/`
- `behavior/`
- `adapters/`

Incorrect:
- `kafka/`
- `postgres/`

Technology-specific names are only valid for adapters.

---

### 3.2 Stable Paths

Artifacts referenced by:
- protocols
- skills
- agents

MUST have stable, predictable paths.

Changing paths without updating all references is BLOCKING.

---

## 4. Dependency Direction

Dependencies MUST follow this direction:

```
Framework Governance
        ↓
Source of Truth (`source-of-truth/`)
        ↓
Implementation
```

Rules:
- SoT MUST NOT depend on implementation
- Governance MUST NOT depend on implementation
- Implementation MAY depend on SoT

Any reverse dependency is BLOCKING.

---

## 5. Deterministic Accessibility

All artifacts required for decision-making MUST be:

- versioned
- explicitly located
- accessible via file path

Prohibited:
- relying on chat history
- relying on conventions not written in the repository
- requiring external context not referenced in the repository

---

## 6. Generated Artifacts

Generated content MUST:

- be clearly marked
- be reproducible
- NOT be treated as authoritative

Manual edits to generated artifacts without regeneration pipeline are NOT allowed.

---

## 7. Agent-Facing Integrity

Artifacts used by agents (protocols, skills, references) MUST:

- point to the correct authoritative files under `source-of-truth/`
- NOT reference deprecated or removed artifacts
- NOT embed duplicated logic from SoT

BLOCKING if a protocol or skill references a path that does not exist in the repository or names a removed artifact. NON-BLOCKING if text is stale but paths resolve and no duplicate normative logic exists.

---

## 8. Validation Criteria

A repository satisfies structure rules if:

- `source-of-truth/` exists and contains all five required subdirectories
- no duplicated authority exists
- dependency direction is respected
- all agent references resolve to valid paths
- no layer violates its responsibility

---

## 9. Failure Conditions

The following are **BLOCKING** violations (do not assign READY, PARTIAL, or INVALID here; map findings to `references/validation-checklist.md`):

- `source-of-truth/` cannot be identified at repository root
- multiple authoritative sources exist for the same concept
- governance or implementation overrides SoT
- dependency direction is violated

---

## 10. Expected Outcome

A compliant structure ensures:

- one unambiguous tree for where to read or modify behavior under `source-of-truth/`
- deterministic execution by multiple agents against the same files
- controlled evolution without undocumented path changes
