# Smart Test Case Generator (STG-QC)

A Claude Code skill that reads a product specification (Markdown) and automatically generates a comprehensive, structured set of test cases — including happy path, negative path, edge cases, and security scenarios.

---

## What it does

Given any feature specification in Markdown format, STG-QC will:

1. **Extract** all requirements from headings, lists, user stories, and acceptance criteria
2. **Analyze** for ambiguities, missing security requirements, and unstated edge cases
3. **Generate** test cases grouped by module and category with full traceability
4. **Report** requirement coverage with a REQ → TC mapping table

### Output per feature module

| Category | Count | ID Format |
|----------|-------|-----------|
| Happy Path | 1–2 | `TC-<MODULE>-<NNN>` |
| Negative Path | 2–3 | `NEG-<MODULE>-<NNN>` |
| Edge Cases | 3 | `EDGE-<MODULE>-<NNN>` |
| Security | 1–2 | `SEC-<MODULE>-<NNN>` |

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

## Output format

Standard test case table:

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |

Security test cases add: `threat_vector | severity | mitigation`

Edge/Error test cases add: `error_code | trigger_condition | expected_behavior | recovery_action`

---

## Files

| File | Description |
|------|-------------|
| [`SPEC.md`](SPEC.md) | Full skill specification — goals, workflow, field definitions, export targets |
| [`SKILL.md`](SKILL.md) | AI instruction prompt — what the skill does and how |
| [`.claude/commands/stg-qc.md`](.claude/commands/stg-qc.md) | Registered slash command |
| [`demo-spec.md`](demo-spec.md) | Sample product specification (User Registration & Login) |
| [`demo-output.md`](demo-output.md) | Sample generated test cases from `demo-spec.md` |

---

## Example

**Input** (`demo-spec.md`): User Registration & Login specification with 4 modules, 27 requirements.

**Output** (`demo-output.md`): 42 test cases across 4 modules, 100% requirement coverage, full traceability matrix.

```
Module              | Happy | Negative | Edge | Security | Total
--------------------|-------|----------|------|----------|------
User Registration   |   2   |    4     |  4   |    2     |  12
Email Verification  |   1   |    3     |  3   |    2     |   9
User Login          |   2   |    3     |  3   |    3     |  11
Password Reset      |   2   |    3     |  3   |    2     |  10
TOTAL               |   7   |   13     | 13   |    9     |  42
```
