# SKILL SPECIFICATION: SMART TEST CASE GENERATOR (STG-QC)

## 1. Goal

To create an AI assistant capable of rapidly comprehending product specifications and automating the creation of comprehensive test cases with high coverage, including edge cases and potential logic gaps.

## 2. Input & Output

- **Input:** Product specification file in `.md` (Markdown) format.
- **Output:** A complete set of Test Cases in a structured table, categorized by:
  - **Happy Path:** Successful functional flows.
  - **Negative Path:** Error handling and invalid input scenarios.
  - **Edge Cases:** Boundary conditions (e.g., max characters, negative numbers, duplicates, etc.).

- **Table Format:** `ID | Module | Scenario | Steps | Expected Result | Priority`

> Example table header (Markdown):

```
| ID | Module | Scenario | Steps | Expected Result | Priority |
|----|--------|----------|-------|-----------------|----------|
| TC-001 | Auth | Login with valid credentials | 1. Open app 2. Enter valid username/password 3. Tap Login | User is logged in and taken to dashboard | High |
```

## 3. Workflow

- **Step 1: Extract:** The AI scans the specification to identify all key features and constraints.
- **Step 2: Smart Analysis:** The AI proactively identifies potential ambiguities and generates at least 3 edge cases for each primary feature that may not be explicitly mentioned in the spec.
- **Step 3: Generate:** Writes detailed test steps and expected results based on the analyzed logic.
- **Step 4: Review & Format:** Self-checks for consistency and ensures the output matches the required structured format.

## 4. Key Strengths

- **Speed:** Converts lengthy specifications into test cases in seconds.
- **Proactive Coverage:** Does not just follow the spec blindly; it suggests additional testing scenarios based on professional QC best practices.
 
## 5. Requirement Coverage

- **Goal:** Provide a measurable coverage report that shows what percentage of the requirements/stories in the input specification are covered by generated test cases.

- **What is counted as a Requirement/Story:**
  - Top-level features and sub-features extracted from headings (H1/H2/H3), numbered lists, and explicit "Stories" sections.
  - Inline constraints or acceptance criteria written as bullet points under a feature are considered sub-requirements.

- **Coverage calculation:**
  - Map each requirement/story to one or more generated test cases.
  - Coverage % = (Number of unique requirements with >=1 mapped test case) / (Total number of extracted requirements) * 100.
  - Provide breakdown by category: Happy Path coverage, Negative Path coverage, Edge Case coverage.

- **Coverage Report fields:**
  - `total_requirements`: integer
  - `covered_requirements`: integer
  - `coverage_percent`: float (2 decimal places)
  - `by_category`: { "happy": %, "negative": %, "edge": % }
  - `uncovered_items`: list of requirement identifiers and text that have no mapped test case
  - `mapping_examples`: sample mapping of requirement -> testcase IDs

- **Thresholds & Alerts:**
  - Configurable threshold (default 80%). If `coverage_percent` < threshold, mark report as `needs_review` and list uncovered items.
  - Option to auto-generate additional edge/negative cases if coverage below threshold.

## 6. Language & Domain-Term Handling

- **Goal:** Ensure domain-specific terms and terminology are correctly interpreted and used when generating test cases (avoid false assumptions).

- **Glossary / Domain Dictionary:**
  - Support an optional glossary file (JSON/CSV/YAML) supplied by the user. Each entry: `term`, `canonical_form`, `type` (noun/verb/entity), `notes`, `synonyms`.
  - The tool will normalize occurrences in spec to `canonical_form` when creating Module/Scenario names and will reference original text in steps/expected results.

- **Automatic Term Extraction & Suggestion:**
  - On first pass, the analyzer will extract candidate domain terms (capitalized tokens, repeated nouns, acronym patterns) and suggest them for confirmation.
  - Provide an interactive or config-driven approval step to accept/reject/modify suggested domain terms.

- **Disambiguation & Context:**
  - Maintain term-context mapping: when a term appears in different modules, record its local meaning.
  - When ambiguity/conflict is detected (same term used with different senses), flag for manual review and optionally create module-scoped synonyms.

- **Language Edge Cases:**
  - Handle multilingual specs by detecting language (auto-detect) and applying appropriate tokenization rules. Provide a `language` config option.
  - Normalize punctuation, unicode variants, and common OCR/noise errors.

- **Confidence & Fallbacks:**
  - Each term mapping has a confidence score. If confidence < configurable threshold, generation will either: (a) include note `"term_confidence_low"` in the testcase, or (b) queue the item for clarification.

## 7. Export & Integrations (Multi-platform)

- **Supported export targets:**
  - Local formats: `markdown` (table), `csv`, `xlsx`.
  - Test management platforms via API: `Jira` (as issues or Xray Zephyr tests), `Zephyr` (Cloud/Server APIs), `TestRail` (API v2).

- **Export mapping and templates:**
  - Provide per-target mapping configuration that maps internal testcase fields to target fields. Example mapping keys: `title`, `steps`, `expected_result`, `priority`, `component`, `labels`.
  - Allow templating for `title` and `steps` (e.g., `{{Module}} - {{Scenario}}`).

- **Authentication & Security:**
  - Support API token-based auth, Basic Auth, and OAuth where supported by the target.
  - Allow storing credentials encrypted in a local config (user opt-in) or accept ephemeral tokens at runtime.

- **Rate limiting, batching & idempotency:**
  - Implement batching of testcases to respect API rate limits; configurable batch size and delay.
  - Use idempotency keys (where supported) so repeated exports don't create duplicates. Provide a `dry-run` mode showing the API payloads without sending them.

- **Error handling & retry:**
  - Provide per-item error logs and retry policies for transient errors (exponential backoff). Persistent errors are reported in export report with actionable messages.

- **Export Report fields:**
  - `target`: string (e.g., "jira", "testrail")
  - `sent`: integer
  - `created`: integer
  - `updated`: integer
  - `failed`: integer with list of failure reasons
  - `dry_run`: boolean

- **Example: Jira export behavior**
  - Create test issue type or link to Xray/ZAPI depending on mapping.
  - Map `Priority` to Jira priority, `Module` to Component or Label, `ID` to a custom field.
  - Support creating parent Test Suites / Test Cycles if requested.

---
---

## Notes / Implementation Hints

- The generator should prioritize test cases by likely business impact (Priority: High / Medium / Low).
- For each primary feature the AI should output at minimum:
  - 1–2 Happy Path test cases
  - 2–3 Negative Path test cases
  - 3 Edge Cases (boundary, performance, concurrency, invalid input, etc.)
- The AI should flag ambiguous spec items and optionally suggest clarifying questions.
