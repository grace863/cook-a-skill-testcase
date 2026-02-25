# Smart Test Case Generator (STG-QC)

A Claude Code skill that reads any product specification and automatically generates a comprehensive, structured set of test cases — including happy path, negative path, edge cases, and security scenarios.

---

## What it does

Given any feature specification (Markdown, plain text, or pasted content), STG-QC will:

1. **Detect** the feature type of each section: `[AUTH]` `[CRUD]` `[FILE]` `[PAY]` `[SEARCH]` `[STATE]` `[REPORT]` `[NOTIFY]`
2. **Extract** all requirements with full traceability IDs
3. **Analyze** using a type-specific edge case checklist — not generic guesswork
4. **Generate** test cases grouped by module and category
5. **Report** requirement coverage with a REQ → TC mapping table

---

## Usage

### Option A — Slash Command (recommended)

```
/stg-qc @your-spec.md
```

### Option B — Direct prompt

Paste your spec content and say:
> "Follow SKILL.md and generate test cases from this spec."

---

## Output per feature module

| Category | ID Format | Count |
|----------|-----------|-------|
| Happy Path | `TC-<MODULE>-<NNN>` | 1–2 |
| Negative Path | `NEG-<MODULE>-<NNN>` | 2–3 |
| Edge Cases | `EDGE-<MODULE>-<NNN>` | 3 |
| Security | `SEC-<MODULE>-<NNN>` | 1–2 |

**Security tests** include: `threat_vector | severity | mitigation`

**Edge/Error tests** include: `error_code | trigger_condition | expected_behavior | recovery_action`

---

## Demo result — E-Commerce spec (5 modules, 56 requirements)

**Input** (`demo-spec.md`): E-Commerce Product & Order Management — 5 modules covering `[CRUD]` `[FILE]` `[STATE]` `[PAY]` `[SEARCH]`.

**Output** (`demo-output.md`): 60 test cases, 100% requirement coverage.

| Module | Happy | Negative | Edge | Security | Total |
|--------|-------|----------|------|----------|-------|
| Product Management | 2 | 3 | 3 | 2 | 10 |
| Image Upload | 1 | 3 | 3 | 2 | 9 |
| Shopping Cart | 2 | 3 | 3 | 2 | 10 |
| Order & Payment | 3 | 3 | 3 | 2 | 11 |
| Search & Filter | 2 | 3 | 3 | 2 | 10 |
| Review & Rating | 2 | 3 | 3 | 2 | 10 |
| **TOTAL** | **12** | **18** | **18** | **12** | **60** |

---

## Files

| File | Description |
|------|-------------|
| [`SKILL-CARD.md`](SKILL-CARD.md) | One-page skill showcase |
| [`SPEC.md`](SPEC.md) | Full skill specification — goals, workflow, field definitions |
| [`SKILL.md`](SKILL.md) | AI instruction prompt — the core logic Claude executes |
| [`.claude/commands/stg-qc.md`](.claude/commands/stg-qc.md) | Registered slash command |
| [`demo-spec.md`](demo-spec.md) | Sample input — E-Commerce (5 modules, 56 requirements) |
| [`demo-output.md`](demo-output.md) | Sample output — 60 test cases with coverage report |
