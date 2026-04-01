

# AI-FIRST Framework Validator

## Overview

The AI-FIRST Framework Validator is a Codex-compatible skill designed to audit repositories against the AI-FIRST framework.

It ensures that systems are:

- deterministic
- structurally consistent
- aligned with Source of Truth principles
- safe for multi-agent execution

The validator detects violations, classifies system readiness, and provides deterministic remediation guidance.

---

## What This Skill Does

The validator performs:

- onboarding validation (is the repo AI-FIRST compliant?)
- structural audits (folders, manifest, dependencies)
- semantic audits (contracts, behavior, acceptance)
- determinism checks
- drift detection between layers
- remediation planning using predefined patterns

---

## Repository Structure

```
framework-validator/
├── SKILL.md
├── agents/
│   └── openai.yaml
└── references/
    ├── structure-rules.md
    ├── sot-rules.md
    ├── validation-checklist.md
    └── remediation-patterns.md
```

---

## Core Concepts

### Source of Truth (SoT)

All system behavior must be defined in a single, authoritative structure:

- contracts
- behavior
- acceptance
- scenarios

No logic should exist outside the SoT.

---

### Determinism

The system must guarantee:

- same input → same output
- no hidden state
- no implicit behavior

---

### Single Authority

Each concept must have exactly one source of definition.

No duplication is allowed across:

- schemas
- rules
- acceptance criteria

---

## How It Works

The validator executes a strict checklist:

1. Identify scope
2. Locate Source of Truth
3. Validate structure
4. Validate contracts
5. Validate behavior
6. Validate acceptance
7. Validate scenarios
8. Verify determinism
9. Check dependencies
10. Validate agent references
11. Classify system
12. Generate remediation plan

---

## Output

The validator produces:

- system status (READY | PARTIAL | INVALID)
- blocking issues
- non-blocking issues
- determinism assessment
- remediation plan
- production readiness verdict

---

## When to Use

Use this skill when:

- onboarding a new repository into AI-FIRST
- auditing an existing system
- detecting drift between layers
- preparing a system for multi-agent execution

---

## When NOT to Use

Do not use this skill for:

- generating code
- designing features
- writing documentation unrelated to validation

---

## Installation

Install using the Codex skill installer:

```
scripts/install-skill-from-github.py --repo <your-repo> --path framework-validator
```

After installation:

Restart Codex to pick up new skills.

---

## Usage Examples

```
Validate this repository against the AI-FIRST framework.
```

```
Audit this project for Source of Truth violations.
```

```
Identify determinism issues in this system.
```

---

## Design Principles

This validator is built on:

- explicit over implicit
- single source of truth
- deterministic execution
- zero ambiguity
- machine-verifiable rules

---

## Expected Outcome

A repository that passes validation:

- is safe for AI agents
- is deterministic
- is structurally consistent
- can evolve without ambiguity

---

## License

Define according to your repository.