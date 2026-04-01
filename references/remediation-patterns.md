# Remediation Patterns (AI-FIRST)

## Purpose

Remediation patterns resolve violations detected during validation of an AI-FIRST repository.

Each pattern:

- addresses one failure class
- defines the correction strategy
- preserves Source of Truth integrity under `source-of-truth/`
- prevents regression

Patterns MUST be applied exactly as specified.

---

## Pattern Structure

Each remediation pattern follows:

- ID
- Name
- Problem
- Detection Signal
- Root Cause
- Remediation Steps
- Expected Outcome
- Failure if Not Applied

---

## RP-SOT-01 — Missing Source of Truth

### Problem
Source of Truth cannot be located at `source-of-truth/`.

### Detection Signal
- `source-of-truth/` does not exist at repository root
- or `source-of-truth/` exists but one or more of `_meta/`, `contracts/`, `behavior/`, `acceptance/`, `scenarios/` is missing

### Root Cause
Project created without AI-FIRST SoT layout.

### Remediation Steps
1. Create `source-of-truth/` at repository root
2. Add:
   - `source-of-truth/_meta/`
   - `source-of-truth/contracts/`
   - `source-of-truth/behavior/`
   - `source-of-truth/acceptance/`
   - `source-of-truth/scenarios/`
3. Add `source-of-truth/_meta/source-of-truth.manifest.json` when using a manifest
4. Populate minimal valid files so checklist sections 4–7 can pass

### Expected Outcome
SoT exists at `source-of-truth/` with all required subdirectories.

### Failure if Not Applied
Validation cannot proceed past checklist sections 1–2.

---

## RP-SOT-02 — Multiple Sources of Truth

### Problem
Same concept defined in more than one authoritative place under `source-of-truth/` or across layers.

### Detection Signal
- duplicated schemas
- duplicated acceptance criteria
- duplicated behavior definitions

### Root Cause
Authority rules not enforced.

### Remediation Steps
1. List conflicting definitions with paths
2. Select one authoritative file under `source-of-truth/`
3. Remove or deprecate the others per template rules in `references/structure-rules.md` section 2.3
4. Update references in protocols and skills to that path

### Expected Outcome
One authoritative file per concept under `source-of-truth/`.

### Failure if Not Applied
Agents apply divergent rules.

---

## RP-CTR-01 — Missing Contract

### Problem
Required contract file missing under `source-of-truth/contracts/`.

### Detection Signal
- missing `entities.schema.json`, `actions.schema.json`, `system-input.schema.json`, `system-output.schema.json`, or `error-codes.schema.json`

### Root Cause
Incomplete SoT initialization.

### Remediation Steps
1. Create the missing file under `source-of-truth/contracts/`
2. Define all required fields per `references/validation-checklist.md` section 4
3. Validate JSON Schema syntax
4. Align with `source-of-truth/scenarios/` and `source-of-truth/behavior/`

### Expected Outcome
Contract layer complete per section 4.

### Failure if Not Applied
Downstream validation cannot run.

---

## RP-CTR-02 — Undefined Field or Type

### Problem
Field used outside schema definition.

### Detection Signal
- scenario field absent from `source-of-truth/contracts/`
- DSL references a field not declared in contracts

### Root Cause
Drift between layers.

### Remediation Steps
1. List undefined symbols with file paths
2. Add fields to the correct schema under `source-of-truth/contracts/` OR remove references from scenarios or DSL
3. Re-run checklist sections 4–7

### Expected Outcome
Contracts match all references.

### Failure if Not Applied
Validation and execution disagree on data shape.

---

## RP-BEH-01 — Undefined DSL Semantics

### Problem
DSL contains undefined operators, variables, or functions.

### Detection Signal
- variables not declared in inputs
- implicit iteration
- undefined functions

### Root Cause
DSL not fully specified.

### Remediation Steps
1. List invalid expressions in `source-of-truth/behavior/rules.dsl.json`
2. Rewrite using only constructs defined in `source-of-truth/behavior/` or referenced grammar docs
3. Declare all free variables
4. Re-run checklist section 5

### Expected Outcome
DSL matches defined semantics.

### Failure if Not Applied
Behavior is not machine-checkable.

---

## RP-BEH-02 — Behavior Delegated to Implementation

### Problem
Normative behavior appears only outside `source-of-truth/behavior/`.

### Detection Signal
- implementation-only text controls a rule that should be in SoT
- `deterministic-boundary.md` omits a comparison or time rule referenced by DSL or eval

### Root Cause
Incomplete deterministic boundary.

### Remediation Steps
1. List behaviors present only in implementation
2. Add explicit rules to `source-of-truth/behavior/` and `deterministic-boundary.md`
3. Align `source-of-truth/contracts/` and `source-of-truth/scenarios/` with those rules

### Expected Outcome
All normative behavior exists under `source-of-truth/behavior/`.

### Failure if Not Applied
Agents and code can diverge.

---

## RP-BEH-03 — Contract/Behavior Misalignment

### Problem
Behavior contradicts or exceeds contract definitions.

### Detection Signal
- DSL references fields not in `source-of-truth/contracts/`
- rules enforce constraints not expressible in current schemas

### Root Cause
Independent edits to layers.

### Remediation Steps
1. Diff `source-of-truth/behavior/` against `source-of-truth/contracts/`
2. Extend schemas OR narrow rules so every DSL field exists in contracts
3. Re-run checklist sections 4–7

### Expected Outcome
Contracts and behavior align.

### Failure if Not Applied
Execution rejects valid contracts or accepts invalid ones.

---

## RP-ACC-01 — Ambiguous Acceptance Criteria

### Problem
Acceptance criteria are not objectively checkable.

### Detection Signal
- criteria use subjective adjectives without measurable predicates
- no link from criterion to contract field or observable output

### Root Cause
Non-operational acceptance text.

### Remediation Steps
1. Rewrite `source-of-truth/acceptance/acceptance-criteria.template.md` using observable predicates
2. Tie each criterion to fields in `source-of-truth/contracts/` or outputs in `system-output.schema.json`
3. Re-run checklist section 6

### Expected Outcome
Each criterion is pass or fail from artifacts and files.

### Failure if Not Applied
Reviewers cannot agree on pass or fail.

---

## RP-ACC-02 — Multiple Acceptance Authorities

### Problem
More than one normative acceptance source.

### Detection Signal
- two files both define acceptance criteria as normative
- template and instance both marked authoritative

### Root Cause
Authority not consolidated.

### Remediation Steps
1. Keep `acceptance-criteria.template.md` as sole normative acceptance template unless manifest states otherwise
2. Remove duplicate normative acceptance files
3. Update protocols and skills to one path

### Expected Outcome
One acceptance authority under `source-of-truth/acceptance/`.

### Failure if Not Applied
Conflicting pass or fail outcomes.

---

## RP-SCN-01 — Scenario/Contract Mismatch

### Problem
Scenarios do not conform to schemas.

### Detection Signal
- required fields missing in fixtures
- fields not in `source-of-truth/contracts/`

### Root Cause
Scenarios authored without schema checks.

### Remediation Steps
1. Validate each fixture against the relevant schema in `source-of-truth/contracts/`
2. Fix structure or schema
3. Re-run checklist section 7

### Expected Outcome
Fixtures validate against contracts.

### Failure if Not Applied
Evaluations use invalid inputs.

---

## RP-EVAL-01 — Undefined Evaluation Semantics

### Problem
Evaluation rules lack a named comparison rule.

### Detection Signal
- `eval.template.json` or linked file does not name comparison rules (exact, subset, or other named rule)

### Root Cause
Incomplete eval specification.

### Remediation Steps
1. Add named comparison rules to `source-of-truth/scenarios/` files referenced by `eval.template.json`
2. Reference those names from `eval.template.json`
3. Re-run checklist section 7

### Expected Outcome
Eval semantics are named and stable.

### Failure if Not Applied
Tests yield unstable results.

---

## RP-DET-01 — Non-Deterministic Behavior

### Problem
Behavior depends on factors not fixed in SoT.

### Detection Signal
- system time used without rule in `deterministic-boundary.md`
- hidden state not declared under `source-of-truth/behavior/`

### Root Cause
Missing deterministic constraints.

### Remediation Steps
1. List each uncontrolled input
2. Add rules to `deterministic-boundary.md` or remove the dependency
3. Re-run checklist section 8

### Expected Outcome
Section 8 has zero BLOCKING findings.

### Failure if Not Applied
`Status` remains INVALID per checklist section 13 while section 8 is APPLICABLE and section 8 records any BLOCKING finding.

---

## RP-DEP-01 — Invalid Dependency Direction

### Problem
`source-of-truth/` depends on implementation.

### Detection Signal
- imports or path references from `source-of-truth/` to application packages
- business rules only in code

### Root Cause
Inverted layering.

### Remediation Steps
1. Move normative rules into `source-of-truth/behavior/` and contracts
2. Remove references from SoT to implementation paths
3. Re-run checklist section 9

### Expected Outcome
Dependencies flow governance → `source-of-truth/` → implementation.

### Failure if Not Applied
SoT is not portable.

---

## RP-AGT-01 — Invalid Agent References

### Problem
Skills or protocols reference wrong SoT paths.

### Detection Signal
- broken paths in `skills/` or `framework/protocols/`
- references to removed files

### Root Cause
Refactor without updating agents.

### Remediation Steps
1. List each broken reference with file path
2. Replace with paths under `source-of-truth/`
3. Remove duplicated normative text from skills
4. Re-run checklist section 10

### Expected Outcome
All references resolve under `source-of-truth/`.

### Failure if Not Applied
Agents read wrong files.

---

## RP-GEN-01 — Generated Artifact as Authority

### Problem
Generated file treated as normative over `source-of-truth/`.

### Detection Signal
- edits to generated output without regeneration
- skills point to generated file instead of schema

### Root Cause
Authority misplaced.

### Remediation Steps
1. Mark generated outputs explicitly
2. Point skills and protocols to `source-of-truth/contracts/` or source templates
3. Add or document regeneration command

### Expected Outcome
Authority stays under `source-of-truth/`.

### Failure if Not Applied
Drift between generated output and schemas.

---

## Final Note

Apply patterns together with `references/validation-checklist.md`.

Do not add new ambiguous rules, duplicate authority, or non-reproducible generated outputs.
