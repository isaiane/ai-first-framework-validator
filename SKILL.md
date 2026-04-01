---
name: ai-first-framework-validator
description: Validate whether a connected repository adheres to the AI-FIRST Framework and guide deterministic remediation. Use when a user wants to onboard an existing project into the framework, audit structural compliance, detect drift between Source of Truth and agent-facing artifacts, identify blocking and non-blocking gaps, or receive an ordered remediation plan to align the repository with the framework. Trigger for requests such as validate this repo against the AI-FIRST Framework, audit framework adherence, check whether this project is compliant, prepare this repository to use the framework, identify what is missing from source-of-truth, or review whether AGENTS, protocols, skills, examples, and source-of-truth are consistent.
---

# AI-FIRST Framework Validator

## Authoritative references

Read and follow as binding, in this order:

1. [references/validation-checklist.md](references/validation-checklist.md) — execution backbone; perform sections 0–15 in order; evaluate sections 1–11 per section 0.1 applicability, then section 12 stop conditions, then section 13 classification; section 15 is informational
2. [references/structure-rules.md](references/structure-rules.md) — blocking criteria for structure
3. [references/sot-rules.md](references/sot-rules.md) — blocking criteria for SoT content
4. [references/remediation-patterns.md](references/remediation-patterns.md) — every remediation action MUST cite one or more pattern IDs (`RP-*`)

If this file disagrees with a reference, the reference wins.

## Canonical Source of Truth location

The Source of Truth root MUST be `source-of-truth/`.

Required directories:

- `source-of-truth/_meta/`
- `source-of-truth/contracts/`
- `source-of-truth/behavior/`
- `source-of-truth/acceptance/`
- `source-of-truth/scenarios/`

Manifest when used: `source-of-truth/_meta/source-of-truth.manifest.json`.

## Orchestration

1. Map the user request to a validation mode per [references/validation-checklist.md](references/validation-checklist.md) section 0.
2. Execute [references/validation-checklist.md](references/validation-checklist.md) sections 0 through 15 in order. Set applicability per section 0.1; then run APPLICABLE checks in sections 1–11; then evaluate section 12; then assign `Status` in section 13 only.
3. For each BLOCKING finding, verify it against [references/structure-rules.md](references/structure-rules.md) and [references/sot-rules.md](references/sot-rules.md) where applicable.
4. Order remediation steps by dependency: duplicate authority and manifest mismatch first; then contracts; then behavior; then acceptance; then scenarios and evaluation; then agent-facing drift; then generated artifacts; last, editorial non-blocking items. Tie-break by checklist section number (lower first).
5. For every remediation step, list pattern IDs from [references/remediation-patterns.md](references/remediation-patterns.md).

## Non-Goals

Do not: redesign the product; invent product requirements; rewrite the repository unless the user asks; assume a runtime, database, or product architecture; mark compliance without file-level evidence; treat examples or prose as normative over contracts.

## Output contract

Produce exactly:

1. **Overall assessment** — `Status:` READY | PARTIAL | INVALID (only as defined in [references/validation-checklist.md](references/validation-checklist.md) section 13); `Confidence:` HIGH | MEDIUM | LOW (HIGH = every APPLICABLE checklist item in this pass has a cited repository path for evidence; MEDIUM = at least one APPLICABLE item is marked pass or fail using only directory existence without reading a file inside that directory; LOW = section 12 stop or section 0 incomplete before completing all APPLICABLE sections numbered lower than the stop trigger); `Scope:` framework | project | example | targeted artifact set; `Summary:` 3–6 factual lines.
2. **Blocking issues** — each: `ID`, `Description`, `Affected Files`, `Why it breaks adherence`, `Remediation pattern IDs`, `Suggested Fix` (must map to those IDs).
3. **Non-blocking issues** — each: `ID`, `Description`, `Affected Files`, `Impact`, `Remediation pattern IDs` if any.
4. **Determinism assessment** — PASS if section 8 is NOT APPLICABLE or section 8 has zero BLOCKING failures; FAIL if section 8 is APPLICABLE and has any BLOCKING failure; one sentence citing files or checklist rows.
5. **Remediation order** — ordered list; each step includes pattern IDs.
6. **Agent usability** — `Status` is the sole verdict; definitions are only in [references/validation-checklist.md](references/validation-checklist.md) section 13.

## Style

Prefer repository paths over abstract names. No filler. Do not label READY if any BLOCKING finding exists.

## After user-requested edits

Apply minimal edits; keep derived artifacts non-authoritative; re-run validation on the same scope using this checklist.
