# Source of Truth (SoT) Rules

## Purpose

Define rules for constructing, maintaining, and validating the Source of Truth (SoT) so that:

- system behavior is fully specified in `source-of-truth/` files
- all agents operate from the same authoritative definitions
- no semantic drift occurs across artifacts
- deterministic implementation is possible across independent agents

These rules are **normative** for any project using the AI-FIRST Framework.

---

## 1. Definition

The Source of Truth (SoT) is the **complete, authoritative, and machine-readable specification** of a system.

Root path: **`source-of-truth/`** (repository root).

The SoT MUST:

- define all inputs, outputs, entities, behavior, and acceptance conditions in files under `source-of-truth/`
- include every artifact required by `references/validation-checklist.md` sections 2–7
- be independent of runtime, tooling, or environment

The SoT is the only layer allowed to define **what the system does**.

---

## 2. Required Sections

A compliant SoT MUST include the following directories under `source-of-truth/`:

- `source-of-truth/_meta/`
- `source-of-truth/contracts/`
- `source-of-truth/behavior/`
- `source-of-truth/acceptance/`
- `source-of-truth/scenarios/`

Each section has a distinct responsibility and MUST NOT overlap.

---

## 3. Section Responsibilities

### 3.1 `source-of-truth/_meta/`

Defines global context and indexing.

Typical artifacts:
- `source-of-truth/_meta/source-of-truth.manifest.json`
- glossary
- project context

Rules:
- MUST list which SoT artifacts exist when a manifest is present
- MUST NOT define system behavior

---

### 3.2 `source-of-truth/contracts/`

Defines the structure of the system.

Includes:
- entities
- actions
- system input
- system output
- error codes

Rules:
- MUST be machine-readable (e.g., JSON Schema)
- MUST fully define all fields and types
- MUST NOT include behavior or business rules

---

### 3.3 `source-of-truth/behavior/`

Defines how the system behaves.

Includes:
- rules (DSL or equivalent)
- invariants
- state machine
- deterministic boundary definitions

Rules:
- MUST define all decision logic
- MUST NOT rely on implicit behavior
- MUST NOT delegate semantics to implementation

---

### 3.4 `source-of-truth/acceptance/`

Defines success and failure conditions.

Includes:
- acceptance criteria (template is authoritative)
- quality gates
- done definition

Rules:
- MUST define observable outcomes
- MUST meet checklist items in `references/validation-checklist.md` section 6
- MUST NOT duplicate behavior logic

---

### 3.5 `source-of-truth/scenarios/`

Defines executable validation artifacts.

Includes:
- scenario templates
- evaluation templates
- test matrices
- fixtures

Rules:
- MUST align with contracts and behavior
- MUST meet checklist items in `references/validation-checklist.md` section 7
- MUST NOT introduce new semantics

---

## 4. Single Source of Truth

For any concept, there MUST be exactly one authoritative definition.

Examples:

- field shape → `source-of-truth/contracts/*`
- business rules → `source-of-truth/behavior/*`
- success criteria → `source-of-truth/acceptance/*`

Violations:

- same rule defined in multiple sections
- schema duplicated in prose and diverging
- acceptance redefining behavior

All such violations are BLOCKING.

---

## 5. No Hidden Semantics

All semantics MUST be explicit in the SoT.

Prohibited:

- relying on implementation behavior
- relying on language defaults not written in SoT files
- relying on unstated conventions
- requiring external documentation not referenced from `source-of-truth/`

If a rule cannot be read from files under `source-of-truth/`, the SoT is incomplete.

---

## 6. Determinism Requirements

The SoT MUST guarantee that:

- the same input produces the same output
- all comparisons and evaluations are defined
- no behavior depends on hidden state

The SoT MUST explicitly define in `source-of-truth/behavior/` (including `deterministic-boundary.md`):

- time handling
- randomness (if any)
- string normalization (trim, case, Unicode form as applicable)
- numeric behavior when relevant

Delegating any of these to implementation without a matching written rule in `source-of-truth/behavior/` is BLOCKING.

---

## 7. DSL and Formal Logic

If a DSL is used:

- all operators MUST be defined
- all functions MUST be defined
- variable scope MUST be defined
- grammar MUST be unambiguous or operationally constrained

Undefined identifiers or implicit iteration are BLOCKING.

---

## 8. Consistency Rules

The following MUST hold:

- contracts and behavior are aligned
- behavior and acceptance are aligned
- scenarios and contracts are aligned
- error codes used are declared in contracts

Any contradiction is BLOCKING.

---

## 9. Manifest Authority

If `source-of-truth/_meta/source-of-truth.manifest.json` exists:

- it MUST list all required artifacts the framework expects for that repository
- it MUST list paths that exist on disk

Mismatch between manifest and files under `source-of-truth/` is BLOCKING.

---

## 10. Derived Artifacts

Derived artifacts (e.g., generated docs or examples):

- MUST NOT be treated as authoritative
- MUST be traceable to a source artifact under `source-of-truth/`
- MUST be reproducible

---

## 11. Validation Criteria

The following MUST hold for SoT content (interpret final `Status` only via `references/validation-checklist.md`):

- all required sections exist under `source-of-truth/`
- each section respects its responsibility
- no duplicated authority exists
- no hidden semantics exist
- checklist section 8 in `references/validation-checklist.md` passes with zero BLOCKING findings when section 8 is APPLICABLE

---

## 12. Failure Conditions

The following are **BLOCKING** violations (do not assign READY, PARTIAL, or INVALID here; map findings to `references/validation-checklist.md`):

- any required section is missing under `source-of-truth/`
- multiple authoritative definitions exist for the same concept
- behavior depends on implementation details not written in SoT
- checklist section 8 records a BLOCKING failure when evaluated per `references/validation-checklist.md`
- contracts, behavior, or acceptance contradict each other

---

## 13. Expected Outcome

A valid SoT ensures:

- one machine-readable definition per concept under `source-of-truth/`
- consistent implementation across agents
- repeatable validation and auditing
- controlled evolution without undocumented semantic change
