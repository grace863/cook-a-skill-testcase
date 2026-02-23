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

---

## 5. Requirement Coverage

### 5.1 Requirement Extraction
- Extract requirements from specification headings (H1, H2, H3), numbered lists, bullet points, and explicit "User Stories" or "Acceptance Criteria" sections.
- Assign unique identifier to each extracted requirement for tracking and mapping.

### 5.2 Coverage Calculation
- **Coverage Formula:** Coverage % = (Requirements with ≥1 mapped testcase) / (Total extracted requirements) × 100
- **Breakdown by Category:**
  - Happy Path coverage %: testcases verifying successful flows
  - Negative Path coverage %: testcases for error handling and invalid inputs
  - Edge Case coverage %: testcases for boundary conditions and special scenarios

### 5.3 Coverage Report Structure
- `total_requirements`: Total number of extracted requirements/stories
- `covered_requirements`: Count of requirements with at least one mapped testcase
- `coverage_percent`: Calculated coverage percentage (2 decimal places)
- `by_category`: Breakdown { "happy": %, "negative": %, "edge": % }
- `uncovered_items`: List of requirement IDs and descriptions with no mapped testcases
- `mapping_examples`: Sample mappings showing requirement → testcase ID relationships

### 5.4 Coverage Thresholds & Recommendations
- Configurable coverage threshold (default: 80%). Report marked `needs_review` if coverage falls below threshold.
- Auto-suggest generation of additional edge-case or negative-path testcases if needed to meet target coverage.
- Allow manual assignment of testcases to uncovered requirements.

---

## 6. Language & Domain-Term Handling

### 6.1 Glossary & Domain Dictionary Support
- Accept optional glossary file in JSON, YAML, or CSV format.
- Glossary structure per term: `term`, `canonical_form`, `type` (noun/verb/entity/acronym), `notes`, `synonyms`.
- Tool normalizes term occurrences to `canonical_form` when generating Module and Scenario names.
- Preserve original terminology in Steps and Expected Results for clarity.

### 6.2 Automatic Domain-Term Extraction & Suggestion
- On first parse, extract candidate domain terms: capitalized tokens, frequently repeated nouns, acronyms, specialized phrases.
- Present extracted terms to user for confirmation/refinement (interactive or config-driven approval).
- Approved terms are added to a runtime glossary ensuring consistent usage throughout testcase generation.

### 6.3 Term Disambiguation & Context Management
- Maintain term-context mapping: when a term appears in different modules, record and note its local meaning/interpretation.
- Detect ambiguities: flag when the same term is used with conflicting senses across the specification.
- Allow creation of module-scoped synonyms to clarify usage in specific contexts.

### 6.4 Multilingual Specification & Edge Case Handling
- Auto-detect language of input specification (with configurable `language` override via config).
- Apply appropriate tokenization and NLP rules based on detected language.
- Normalize punctuation variants, unicode forms (NFD/NFC), and common OCR/digitization artifacts.

### 6.5 Confidence Scoring & Interpretation Fallbacks
- Each term mapping and interpretation assigned a confidence score (0.0 to 1.0 scale).
- If confidence score < configurable threshold (default 0.7):
  - **Option A:** Include `"term_confidence_low"` annotation in the generated testcase for manual review.
  - **Option B:** Queue the item for clarification step; request user confirmation before finalizing testcase.

---

## 7. Export & Multi-Platform Integrations

### 7.1 Supported Export Targets
- **Local Formats:** Markdown (table format), CSV (comma-separated values), XLSX (Microsoft Excel).
- **Test Management Platforms (via API):**
  - **Jira** (Cloud and Server versions): Create test issues or integrate with Xray/ZAPI plugins.
  - **Zephyr** (Cloud and Server versions): Native Zephyr API for test case and test cycle management.
  - **TestRail** (Cloud and Server versions): TestRail API v2 for test case suites and test runs.
  - **Plugin Interface:** Extensible adapter pattern for adding custom export targets.

### 7.2 Export Field Mapping & Templating
- Define per-target mapping configuration that translates internal testcase fields to platform-specific fields.
- **Supported mapping keys:** `title`, `steps`, `expected_result`, `priority`, `component`, `labels`, `custom_field_1`, etc.
- **Template variable support:** Use `{{Module}}`, `{{Scenario}}`, `{{ID}}` placeholders in title and step templates.
- **Example template:** Title = `{{ID}} - {{Module}}: {{Scenario}}`

### 7.3 Authentication & API Security
- Support multiple authentication methods: API token/key, HTTP Basic Auth, OAuth 2.0 (platform-dependent).
- Credentials can be stored encrypted in local config (user opt-in) or passed via environment variables/CLI arguments per run.
- Require explicit user consent before sending testcases to third-party platforms.
- Option to mask/redact sensitive data fields before export.

### 7.4 Rate Limiting, Batching & Idempotency
- Batch testcase exports to respect platform API rate limits; configurable batch size and inter-batch delay.
- Use idempotency keys (UUID per export operation) to prevent duplicate testcase creation on retry.
- Provide `dry-run` mode: display API request/response payloads and headers without actually sending data.
- Track and report counts: sent, created, updated, failed for each export operation.

### 7.5 Error Handling & Retry Strategy
- Per-item error logging with detailed failure reasons.
- Implement exponential backoff retry for transient errors (network timeouts, rate limit responses).
- Log persistent errors with actionable remediation steps.
- Generate comprehensive Export Report for user review and audit trail.

### 7.6 Export Report Fields
- `target`: Export target name (e.g., "jira", "testrail", "zephyr", "markdown", "csv")
- `sent`: Number of testcases sent
- `created`: Number of testcases successfully created in target
- `updated`: Number of existing testcases updated in target
- `failed`: Number of testcases that failed; includes detailed error reasons per item
- `dry_run`: Boolean flag indicating if payload was simulated (true) or actually sent (false)
- `timestamp`: ISO 8601 timestamp of export operation
- `duration_seconds`: Total execution time
- `export_id`: Idempotency key (UUID) for tracking

### 7.7 Platform-Specific Export Behaviors

#### Jira Export
- Creates test issues (using "Test" issue type or custom types via Xray/ZAPI).
- Field mappings:
  - `Priority` (STG-QC) → `Priority` (Jira priority field)
  - `Module` → `Component` or Label (configurable)
  - `ID` → Custom field `test_case_id` (or mapped custom field)
  - `Steps` → Issue description or Xray test step format
- Optionally creates parent Test Suite / Test Plan issue if configured.

#### Zephyr Export
- Uses Zephyr REST API to create testcases within specified project.
- Maps testcase priority, module (as Zephyr folder/component), steps, and expected results.
- Supports linking to parent Test Cycle if specified in export config.
- Handles Zephyr-specific field requirements and validations.

#### TestRail Export
- Uses TestRail API v2 to add testcases to specified test suite.
- Maps:
  - `Priority` → TestRail priority (1-5 scale)
  - `Module` → Section (folder) within test suite
  - Steps and Expected Results → TestRail step format
- Optionally creates test runs and assigns testcases to runs.
- Supports linking to test cases via case IDs for traceability.

---

## 8. Security, Privacy & Audit

### 8.1 Data Handling & Consent
- Users should redact Personally Identifiable Information (PII) and secrets from specifications before processing.
- Tool provides optional automated redaction pass using regex patterns or user-defined rules.
- When exporting to third-party APIs, require explicit user consent and allow selective field masking.

### 8.2 Audit Trail & Logging
- Maintain export audit log: timestamp, user identifier, target platform, testcase count, operation result (success/failure).
- Store logs locally in `.stgqc_audit_log` file or configurable path.
- Users may clear audit logs at any time; cleared logs are not recoverable.

---

## 9. Extensibility & Configuration

### 9.1 Plugin & Adapter Points
- **Custom Analyzers:** Users can provide custom rule sets and heuristics for domain-specific analysis.
- **Custom Export Adapters:** Implement adapter interface to add support for additional test management platforms.

### 9.2 Configuration File Support (`stgqcrc.yaml` or `stgqcrc.json`)
- Stores all configuration: glossary file path, export target definitions, API credentials (encrypted references), priority mapping, coverage threshold, language override, export field templates, audit log path.
- Example structure:
  ```yaml
  glossary:
    path: "./glossary.json"
  coverage:
    threshold: 80
    auto_generate: true
  export_targets:
    - name: "jira"
      url: "https://company.atlassian.net"
      project_key: "QA"
      credentials_ref: "${JIRA_API_TOKEN}"
    - name: "testrail"
      url: "https://company.testrail.net"
      credentials_ref: "${TESTRAIL_API_KEY}"
  language: "en"
  ```

---

## 10. Deliverables & Outputs

- **Test Cases Files:**
  - `tests.md` (Markdown table format, default export)
  - `tests.csv` (Comma-separated values)
  - `tests.xlsx` (Microsoft Excel, optional)

- **Reports:**
  - `coverage_report.json` (Machine-readable coverage metrics)
  - `coverage_summary.txt` (Human-readable summary)
  - `export_report_<timestamp>.json` (Per export operation record)

- **Sample Configuration Files:**
  - `glossary_sample.json` (Example domain glossary)
  - `stgqcrc_sample.yaml` (Example configuration)
  - Platform-specific export mapping templates for Jira, Zephyr, TestRail

---

## Notes / Implementation Hints

- The generator should prioritize test cases by likely business impact (Priority: High / Medium / Low).
- For each primary feature the AI should output at minimum:
  - 1–2 Happy Path test cases
  - 2–3 Negative Path test cases
  - 3 Edge Cases (boundary, performance, concurrency, invalid input, etc.)
- The AI should flag ambiguous spec items and optionally suggest clarifying questions.
