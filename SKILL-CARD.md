# STG-QC — Smart Test Case Generator

> Claude Code skill that reads any product specification and generates structured, traceable test cases in one command.

---

## The Problem

Writing test cases manually is slow, inconsistent, and easy to miss edge cases.
Most QC engineers default to happy-path testing and forget security and boundary checks.

## The Solution

**STG-QC** detects what kind of feature you are testing, applies a type-specific checklist, and generates a complete test suite with full requirement traceability — automatically.

---

## How It Works

```
/stg-qc @your-spec.md
```

| Step | What Happens |
|------|--------------|
| **Step 0** | Reads the spec and labels each section: `[AUTH]` `[CRUD]` `[FILE]` `[PAY]` `[SEARCH]` `[STATE]` `[REPORT]` `[NOTIFY]` |
| **Step 1** | Extracts every requirement and assigns a unique REQ-ID with full traceability |
| **Step 2** | Runs a mandatory edge-case checklist tuned to each feature type; flags ambiguities and missing security requirements |
| **Step 3** | Generates Happy Path, Negative Path, Edge Case, and Security test cases with standard fields |
| **Step 4** | Produces a Coverage Report — REQ → TC mapping, coverage %, uncovered gaps |

---

## Output Per Module

| Category | ID Format | Typical Count |
|----------|-----------|---------------|
| Happy Path | `TC-<MODULE>-<NNN>` | 1–2 |
| Negative Path | `NEG-<MODULE>-<NNN>` | 2–3 |
| Edge Cases | `EDGE-<MODULE>-<NNN>` | 3 |
| Security | `SEC-<MODULE>-<NNN>` | 1–2 |

**Security tests** include: `threat_vector | severity | mitigation`
**Edge/Error tests** include: `error_code | trigger_condition | expected_behavior | recovery_action`

---

## Type-Specific Edge Case Checklists

The skill never relies on generic guesswork. Each feature type has a mandatory checklist:

| Feature Type | Checklist Highlights |
|---|---|
| `[AUTH]` | Lockout at exact threshold, token expiry boundary, concurrent sessions |
| `[CRUD]` | Max-length field, duplicate key, soft-delete visibility, cascade effects |
| `[FILE]` | File at size limit ±1 byte, duplicate hash, corrupted/empty file |
| `[PAY]` | Gateway timeout, double-submit, failure mid-redirect |
| `[SEARCH]` | Zero results (not error), special chars, combined filters, page beyond last |
| `[STATE]` | All invalid transitions rejected, race condition, access in intermediate state |

---

## Handles Incomplete Specs Gracefully

| Situation | Behavior |
|---|---|
| Validation rules missing | Applies common-sense defaults, annotates `⚠️ ASSUMED:` |
| Expected error not specified | Defaults to HTTP error + message, annotates `⚠️ ASSUMED:` |
| Vague section heading | Generates 1 Happy + 1 Negative, flags `⚠️ AMBIGUITY:` |
| Plain text / non-Markdown input | Auto-normalizes, proceeds normally |

---

## Demo Result — E-Commerce Spec

**Input** ([`demo-spec.md`](demo-spec.md)): E-Commerce Product & Order Management — 5 modules, 56 requirements
**Output** ([`demo-output.md`](demo-output.md)): 60 test cases, 100% requirement coverage

| Module | Feature Type | Happy | Neg | Edge | Sec | Total |
|--------|-------------|-------|-----|------|-----|-------|
| Product Management | `[CRUD]` | 2 | 3 | 3 | 2 | 10 |
| Image Upload | `[FILE]` | 1 | 3 | 3 | 2 | 9 |
| Shopping Cart | `[CRUD][STATE]` | 2 | 3 | 3 | 2 | 10 |
| Order & Payment | `[STATE][PAY]` | 3 | 3 | 3 | 2 | 11 |
| Search & Filter | `[SEARCH]` | 2 | 3 | 3 | 2 | 10 |
| Review & Rating | `[CRUD]` | 2 | 3 | 3 | 2 | 10 |
| **TOTAL** | | **12** | **18** | **18** | **12** | **60** |

---

## Files

| File | Role |
|------|------|
| [`SKILL.md`](SKILL.md) | AI instruction prompt — the core logic Claude executes |
| [`SPEC.md`](SPEC.md) | Full skill specification — goals, workflow, field definitions |
| [`.claude/commands/stg-qc.md`](.claude/commands/stg-qc.md) | Registered slash command |
| [`demo-spec.md`](demo-spec.md) | Sample input — E-Commerce (5 modules, 56 requirements) |
| [`demo-output.md`](demo-output.md) | Sample output — 60 test cases with coverage report |

---

*Works on any product spec: just point it at a file or paste content directly.*
