# Validation Checklist (AI-FIRST)

## Purpose

Step-by-step checklist to validate repository compliance with the AI-FIRST Framework.

This checklist is **operational** and MUST be followed in section order.

---

## 0. Scope Identification

Determine the validation scope before any checks.

- [ ] Target is identified as one of:
  - framework repository
  - project using the framework
  - example under `examples/*`
  - specific artifact or subset

- [ ] Validation mode selected:
  - onboarding
  - full audit
  - example audit
  - targeted audit

If scope cannot be determined, STOP and set `Status` to INVALID.

### 0.1 Applicability of sections 1–11

For validation mode `onboarding`, `full audit`, or `example audit`, sections **1 through 11** are **APPLICABLE**.

For validation mode `targeted audit`, or when the target is **specific artifact or subset**, assign each of sections **1 through 11** exactly one of:

- **APPLICABLE** — evaluate that section’s checklist rows; record BLOCKING and NON-BLOCKING findings from that section.
- **NOT APPLICABLE** — do not evaluate that section’s checklist rows; record no BLOCKING and no NON-BLOCKING from that section; record one justification line per NOT APPLICABLE section (repository path or artifact identifier).

NOT APPLICABLE sections MUST NOT produce failures. Final classification uses only findings from APPLICABLE sections.

Sections **12** and **13** are always evaluated. Section **12** stop conditions use the repository tree as it exists; they are not skipped when sections 1–11 are marked NOT APPLICABLE in section 0.1.

---

## 1. Authoritative Structure Detection

Apply only if section 1 is APPLICABLE per section 0.1.

- [ ] `AGENTS.md` exists OR `framework/` exists OR `skills/` exists
- [ ] `source-of-truth/` exists at repository root
- [ ] `source-of-truth/_meta/source-of-truth.manifest.json` exists OR the five section directories in section 2 each exist on disk

Failure conditions:
- `source-of-truth/` missing at repository root → BLOCKING

---

## 2. Source of Truth Completeness

Apply only if section 2 is APPLICABLE per section 0.1.

- [ ] `source-of-truth/_meta/` exists
- [ ] `source-of-truth/contracts/` exists
- [ ] `source-of-truth/behavior/` exists
- [ ] `source-of-truth/acceptance/` exists
- [ ] `source-of-truth/scenarios/` exists

- [ ] If `source-of-truth/_meta/source-of-truth.manifest.json` exists, every path it lists exists under `source-of-truth/`

Failure conditions:
- any required directory above missing → BLOCKING
- manifest lists a path that does not exist → BLOCKING

---

## 3. Single Source of Truth Integrity

Apply only if section 3 is APPLICABLE per section 0.1.

- [ ] each concept has exactly one authoritative location under `source-of-truth/`
- [ ] no duplicated acceptance definitions
- [ ] no duplicated schema definitions
- [ ] no template and instance both treated as authoritative
- [ ] protocols and skills point only to authoritative artifacts under `source-of-truth/`

Failure conditions:
- duplicated authority detected → BLOCKING

---

## 4. Contracts Validation

Apply only if section 4 is APPLICABLE per section 0.1.

All paths under `source-of-truth/contracts/`.

- [ ] `entities.schema.json` exists
- [ ] `actions.schema.json` exists
- [ ] `system-input.schema.json` exists
- [ ] `system-output.schema.json` exists
- [ ] `error-codes.schema.json` exists

- [ ] all fields are typed and defined
- [ ] no undefined fields exist
- [ ] error codes referenced elsewhere exist in `error-codes.schema.json`
- [ ] input and output schemas align with scenarios and fixtures under `source-of-truth/scenarios/`

Failure conditions:
- any file above missing → BLOCKING
- undefined fields → BLOCKING
- mismatched error codes → BLOCKING

---

## 5. Behavior Validation

Apply only if section 5 is APPLICABLE per section 0.1.

All paths under `source-of-truth/behavior/`.

- [ ] `rules.dsl.json` exists
- [ ] `invariants.json` exists
- [ ] `state-machine.json` exists
- [ ] `deterministic-boundary.md` exists

- [ ] DSL contains only defined operators and functions
- [ ] no undefined identifiers or variables
- [ ] no implicit iteration or hidden semantics
- [ ] invariants do not contradict `source-of-truth/contracts/`
- [ ] state transitions align with `source-of-truth/contracts/actions.schema.json`
- [ ] `deterministic-boundary.md` defines: time handling; string normalization; numeric behavior when `contracts/` or `rules.dsl.json` reference numeric fields

Failure conditions:
- any file above missing → BLOCKING
- undefined DSL semantics → BLOCKING
- behavior delegated to implementation without a written rule in `source-of-truth/behavior/` → BLOCKING
- contradiction with contracts → BLOCKING

---

## 6. Acceptance Validation

Apply only if section 6 is APPLICABLE per section 0.1.

All paths under `source-of-truth/acceptance/`.

- [ ] `acceptance-criteria.template.md` exists and is the single authoritative acceptance template
- [ ] no second file defines normative acceptance criteria
- [ ] `quality-gates.md` exists
- [ ] `done-definition.md` exists

- [ ] acceptance defines observable outcomes
- [ ] language is verifiable (pass or fail per stated condition)
- [ ] acceptance does not redefine behavior

Failure conditions:
- multiple acceptance authorities → BLOCKING
- acceptance criteria not verifiable from text and contracts → BLOCKING

---

## 7. Scenarios and Evaluation

Apply only if section 7 is APPLICABLE per section 0.1.

All paths under `source-of-truth/scenarios/`.

- [ ] `scenario.template.json` exists
- [ ] `eval.template.json` exists
- [ ] `test-matrix.template.json` exists
- [ ] at least one fixture file exists

- [ ] scenarios align with `source-of-truth/contracts/`
- [ ] evaluation semantics name comparison rules (exact, subset, or other named rule) in `eval.template.json` or a referenced file under `source-of-truth/scenarios/`
- [ ] no evaluation references behavior not defined under `source-of-truth/behavior/`
- [ ] fixtures validate against `source-of-truth/contracts/` schemas

Failure conditions:
- evaluation semantics not named in a file under `source-of-truth/scenarios/` → BLOCKING
- scenarios contradict contracts → BLOCKING

---

## 8. Determinism Verification

Apply only if section 8 is APPLICABLE per section 0.1.

- [ ] same input produces same output by written rule in `source-of-truth/behavior/`
- [ ] no reliance on system time unless `deterministic-boundary.md` defines injection or clock source
- [ ] no reliance on hidden state not defined under `source-of-truth/behavior/`
- [ ] no reliance on external uncontrolled dependencies not listed in `source-of-truth/_meta/` or contracts
- [ ] all comparisons used in eval and DSL are defined in `deterministic-boundary.md` or DSL specification files under `source-of-truth/behavior/`

Failure conditions:
- any item above fails → BLOCKING

---

## 9. Dependency Direction

Apply only if section 9 is APPLICABLE per section 0.1.

- [ ] governance does not depend on implementation
- [ ] `source-of-truth/` does not import from application code paths
- [ ] implementation references `source-of-truth/` for rules, not the reverse

Failure conditions:
- reverse dependency detected → BLOCKING

---

## 10. Agent-Facing Integrity

Apply only if section 10 is APPLICABLE per section 0.1.

- [ ] protocols reference correct paths under `source-of-truth/`
- [ ] skills reference correct paths under `source-of-truth/`
- [ ] no references to deprecated or removed files
- [ ] no duplicated normative logic from SoT inside skills or protocols

Failure conditions:
- protocol or skill points to wrong or missing SoT path → BLOCKING

---

## 11. Generated and Derived Artifacts

Apply only if section 11 is APPLICABLE per section 0.1.

- [ ] generated artifacts are marked as such
- [ ] generated artifacts are reproducible from a documented command or script in the repository
- [ ] generated artifacts are not treated as authoritative over `source-of-truth/`

Failure conditions:
- generated artifact treated as source of truth → BLOCKING

---

## 12. Stop Conditions

Evaluate this section **after** sections 1–11 have been evaluated for all APPLICABLE rows per section 0.1.

Set `section_12_stop` to true if **any** of the following holds:

- `source-of-truth/` is not at repository root
- manifest and directory tree under `source-of-truth/` conflict (listed path missing)
- `source-of-truth/contracts/` is missing or contains none of the required schema files listed in section 4
- `source-of-truth/behavior/` is missing or contains none of `rules.dsl.json`, `invariants.json`, `state-machine.json`, `deterministic-boundary.md`

Do not re-evaluate sections 1–11 after this section.

---

## 13. Final Classification

This section defines the **only** authoritative `Status`: READY | PARTIAL | INVALID.

Evaluate **after** section 12. Use **only** BLOCKING and NON-BLOCKING findings from **APPLICABLE** sections (section 0.1). Ignore findings from NOT APPLICABLE sections.

**INVALID** if any of:

- section 0 could not be completed
- `section_12_stop` is true (section 12)
- section 3 is APPLICABLE and multiple authoritative definitions exist for one concept
- section 8 is APPLICABLE and section 8 records any BLOCKING failure

**READY** if:

- not INVALID
- zero BLOCKING findings across all APPLICABLE sections among 1–11

**PARTIAL** if:

- not INVALID
- if section 8 is APPLICABLE: zero BLOCKING findings in section 8
- at least one BLOCKING finding in at least one APPLICABLE section among 1, 2, 3, 4, 5, 6, 7, 9, 10, or 11

NON-BLOCKING findings do not change READY to PARTIAL. List NON-BLOCKING items separately in the output for APPLICABLE sections only.

---

## 14. Output Requirements

Validation MUST produce:

- `Status`: READY | PARTIAL | INVALID (section 13)
- list of BLOCKING findings with file paths (APPLICABLE sections only)
- list of NON-BLOCKING findings with file paths (APPLICABLE sections only)
- determinism assessment (PASS if section 8 is NOT APPLICABLE or section 8 has zero BLOCKING failures; FAIL if section 8 is APPLICABLE and has any BLOCKING failure)
- ordered remediation plan with `RP-*` pattern IDs from `references/remediation-patterns.md`
- `Confidence` per `SKILL.md` output contract
- for targeted or subset scope: the applicability table from section 0.1 with justifications for each NOT APPLICABLE section

---

## 15. Expected Outcome

A repository that completes this checklist with `Status` READY has:

- all required SoT paths present under `source-of-truth/` for APPLICABLE sections
- zero BLOCKING findings in APPLICABLE sections 1–11
- repeatable validation across agents using the same files and applicability rules
