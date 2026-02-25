# SKILL SPECIFICATION: SMART TEST CASE GENERATOR (STG-QC)

## 1. Goal

To create an AI assistant capable of rapidly comprehending product specifications and automating the creation of comprehensive test cases with high coverage, including edge cases and potential logic gaps.

## 2. Input & Output

- **Input:** Product specification file in `.md` (Markdown) format.
- **Output:** A complete set of Test Cases in a structured table, categorized by:
  - **Happy Path:** Successful functional flows.
  - **Negative Path:** Error handling and invalid input scenarios.
  - **Edge Cases:** Boundary conditions (e.g., max characters, negative numbers, duplicates, etc.).

- **Table Format (Extended):** `ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority`

> Example table header (Markdown):

```
| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| TC-001 | Auth | Login with valid credentials | User not logged in; Valid user account exists | Username: testuser, Password: secure123 | 1. Open app 2. Enter username/password 3. Tap Login | User logged in, dashboard displayed | User sees dashboard within 2s; Session token valid | High |
```

- **Table Field Descriptions:**
  - `ID`: Unique test case identifier (e.g., TC-001, STG-AUTH-001)
  - `Module`: Feature or functional area being tested
  - `Scenario`: Brief description of test scenario
  - `Preconditions`: Required system state or setup before test execution
  - `Test Data`: Specific data values used in the test (inputs, parameters)
  - `Steps`: Numbered sequence of actions to perform
  - `Expected Result`: Anticipated outcome of the test
  - `Acceptance Criteria`: Specific conditions/metrics that must be met for test to pass
  - `Priority`: Test case business importance (High / Medium / Low)

## 3. Workflow

- **Step 0 — Pre-flight & Spec Type Detection:** Read the entire specification and classify each section by feature type: `[AUTH]` `[CRUD]` `[FILE]` `[PAY]` `[SEARCH]` `[STATE]` `[REPORT]` `[NOTIFY]`. Accept any readable input — do not reject informal or imperfect Markdown. If the spec is incomplete, proceed with best-effort analysis and annotate all assumptions with `⚠️ ASSUMED:`.

- **Step 1 — Extract:** Scan all headings (H1–H3), numbered lists, bullet points, User Stories, and Acceptance Criteria. Assign a unique ID (REQ-001, REQ-002, …) to each requirement and tag it with its feature type(s). Output a table: `REQ ID | Feature Type | Section | Requirement Description`.

- **Step 2 — Smart Analysis:** For each feature, use the type-specific edge case checklist (see Section 3a) to identify at least 3 edge cases. Flag ambiguities (`⚠️ AMBIGUITY:`), detect security threats, and note missing security requirements (`⚠️ MISSING SECURITY REQ:`). Never skip a feature due to incomplete information — apply defaults and annotate.

- **Step 3 — Generate:** Write test cases using the structured table format. Per primary feature: 1–2 Happy Path, 2–3 Negative Path, 3 Edge Cases (driven by type-specific checklist), 1–2 Security tests.

- **Step 4 — Review & Format:** Self-check consistency. Ensure all REQ IDs map to at least one test case. Output Coverage Report.

---

## 3a. Supported Feature Types & Required Test Patterns

| Label | Feature Type | Required Edge Cases | Required Security Cases |
|---|---|---|---|
| `[AUTH]` | Authentication / Session | Lockout at exact threshold, token expiry at boundary second, concurrent sessions, non-active account login | Brute force, credential stuffing, session hijacking, JWT exposure |
| `[CRUD]` | Create / Read / Update / Delete | Empty list, max-length field, duplicate key, soft-delete visibility, cascade delete | Unauthorized access to other user's data, privilege escalation |
| `[FILE]` | File / Image Upload | File at exact size limit (±1 byte), unsupported MIME type, duplicate hash, corrupted/empty file, concurrent uploads | Malicious file content (XSS in SVG, script in filename), path traversal |
| `[PAY]` | Payment / Transaction | Gateway timeout at boundary, double-submit, gateway failure mid-redirect, zero-amount | Payment tampering, replay attack, sensitive data in logs |
| `[SEARCH]` | Search & Filter | Zero results (not error), max page size, special characters, combined filters with no match, page beyond last | SQL/NoSQL injection via search params, filter bypass |
| `[STATE]` | State Machine / Workflow | All invalid transitions rejected, concurrent race condition, access in intermediate state | Forced transition via parameter tampering |
| `[REPORT]` | Reporting / Export | Zero records, 1 record, date range start = end, max count + 1 | Unauthorized export of other users' data |
| `[NOTIFY]` | Notifications / Email | Delivery to deactivated account, duplicate trigger, retry exhausted | Email header injection, PII exposure in content |

## 4. Key Strengths

- **Speed:** Converts lengthy specifications into test cases in seconds.
- **Proactive Coverage:** Does not just follow the spec blindly; it suggests additional testing scenarios based on professional QC best practices.

---

## 5. Scope & Assumptions

### 5.1 Project Scope
- **In Scope:**
  - Automated test case generation from Markdown specifications
  - Support for happy path, negative, and edge case scenarios
  - Multi-platform export (Jira, Zephyr, TestRail, Markdown, CSV, XLSX)
  - Requirement coverage tracking and reporting
  - Domain-specific terminology normalization
  - Security & error handling test case generation

- **Out of Scope:**
  - Manual test execution/debugging
  - Load testing or performance testing of target platforms
  - Real-time test execution monitoring
  - Integration with CI/CD pipelines (future enhancement)
  - Mobile app UI testing (desktop/web focus only)

### 5.2 Assumptions
- Input specifications are preferably Markdown, but the tool accepts imperfect, informal, or plain-text input — auto-normalize and proceed; only surface a warning if input is completely unreadable
- Users have valid API credentials for target export platforms (Jira, Zephyr, TestRail)
- Network connectivity is available for API exports
- Target platforms support OAuth 2.0 or API token authentication
- Glossary files (if provided) follow JSON, YAML, or CSV format
- Test data does not contain highly sensitive information; users redact PII before processing

---

## 6. Requirement Coverage

### 6.1 Requirement Extraction
- Extract requirements from specification headings (H1, H2, H3), numbered lists, bullet points, and explicit "User Stories" or "Acceptance Criteria" sections.
- Assign unique identifier to each extracted requirement for tracking and mapping.

### 6.2 Coverage Calculation
- **Coverage Formula:** Coverage % = (Requirements with ≥1 mapped testcase) / (Total extracted requirements) × 100
- **Breakdown by Category:**
  - Happy Path coverage %: testcases verifying successful flows
  - Negative Path coverage %: testcases for error handling and invalid inputs
  - Edge Case coverage %: testcases for boundary conditions and special scenarios

### 6.3 Coverage Report Structure
- `total_requirements`: Total number of extracted requirements/stories
- `covered_requirements`: Count of requirements with at least one mapped testcase
- `coverage_percent`: Calculated coverage percentage (2 decimal places)
- `by_category`: Breakdown { "happy": %, "negative": %, "edge": % }
- `uncovered_items`: List of requirement IDs and descriptions with no mapped testcases
- `mapping_examples`: Sample mappings showing requirement → testcase ID relationships

### 6.4 Coverage Thresholds & Recommendations
- Configurable coverage threshold (default: 80%). Report marked `needs_review` if coverage falls below threshold.
- Auto-suggest generation of additional edge-case or negative-path testcases if needed to meet target coverage.
- Allow manual assignment of testcases to uncovered requirements.

---

## 7. Language & Domain-Term Handling

### 7.1 Glossary & Domain Dictionary Support
- Accept optional glossary file in JSON, YAML, or CSV format.
- Glossary structure per term: `term`, `canonical_form`, `type` (noun/verb/entity/acronym), `notes`, `synonyms`.
- Tool normalizes term occurrences to `canonical_form` when generating Module and Scenario names.
- Preserve original terminology in Steps and Expected Results for clarity.

### 7.2 Automatic Domain-Term Extraction & Suggestion
- On first parse, extract candidate domain terms: capitalized tokens, frequently repeated nouns, acronyms, specialized phrases.
- Present extracted terms to user for confirmation/refinement (interactive or config-driven approval).
- Approved terms are added to a runtime glossary ensuring consistent usage throughout testcase generation.

### 7.3 Term Disambiguation & Context Management
- Maintain term-context mapping: when a term appears in different modules, record and note its local meaning/interpretation.
- Detect ambiguities: flag when the same term is used with conflicting senses across the specification.
- Allow creation of module-scoped synonyms to clarify usage in specific contexts.

### 7.4 Multilingual Specification & Edge Case Handling
- Auto-detect language of input specification (with configurable `language` override via config).
- Apply appropriate tokenization and NLP rules based on detected language.
- Normalize punctuation variants, unicode forms (NFD/NFC), and common OCR/digitization artifacts.

### 7.4a Data Validation
- **Input Validation:**
  - Validate all input fields from specification and test data for required presence, correct type, allowed values, and format (e.g., email, phone, date).
  - Reject hoặc flag invalid/ambiguous data trước khi sinh testcase.
- **Field Normalization:**
  - Chuẩn hóa whitespace, punctuation, unicode variants cho tất cả các trường.
  - Auto-correct các lỗi phổ biến do OCR/digitization.
- **Validation Testcases:**
  - Sinh negative-path testcase cho input sai, thiếu trường bắt buộc, giá trị biên.
  - Ví dụ:
    | ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
    |----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
    | TC-NEG-001 | Profile | Submit invalid email | User logged in | Email: abc@ | 1. Go to profile 2. Enter invalid email 3. Save | Error message shown, email not updated | Error shown for invalid email format | Medium |

### 7.5 Confidence Scoring & Interpretation Fallbacks
- Each term mapping and interpretation assigned a confidence score (0.0 to 1.0 scale).
- If confidence score < configurable threshold (default 0.7):
  - **Option A:** Include `"term_confidence_low"` annotation in the generated testcase for manual review.
  - **Option B:** Queue the item for clarification step; request user confirmation before finalizing testcase.

---

## 8. Export & Multi-Platform Integrations

### 8.1 Supported Export Targets
- **Local Formats:** Markdown (table format), CSV (comma-separated values), XLSX (Microsoft Excel).
- **Test Management Platforms (via API):**
  - **Jira** (Cloud and Server versions): Create test issues or integrate with Xray/ZAPI plugins.
  - **Zephyr** (Cloud and Server versions): Native Zephyr API for test case and test cycle management.
  - **TestRail** (Cloud and Server versions): TestRail API v2 for test case suites and test runs.
  - **Plugin Interface:** Extensible adapter pattern for adding custom export targets.

### 8.2 Export Field Mapping & Templating
- Define per-target mapping configuration that translates internal testcase fields to platform-specific fields.
- **Supported mapping keys:** `title`, `steps`, `expected_result`, `priority`, `component`, `labels`, `custom_field_1`, etc.
- **Template variable support:** Use `{{Module}}`, `{{Scenario}}`, `{{ID}}` placeholders in title and step templates.
- **Example template:** Title = `{{ID}} - {{Module}}: {{Scenario}}`

### 8.3 Authentication & API Security
- Support multiple authentication methods: API token/key, HTTP Basic Auth, OAuth 2.0 (platform-dependent).
- Credentials can be stored encrypted in local config (user opt-in) or passed via environment variables/CLI arguments per run.
- Require explicit user consent before sending testcases to third-party platforms.
- Option to mask/redact sensitive data fields before export.

### 8.4 Rate Limiting, Batching & Idempotency
- Batch testcase exports to respect platform API rate limits; configurable batch size and inter-batch delay.
- Use idempotency keys (UUID per export operation) to prevent duplicate testcase creation on retry.
- Provide `dry-run` mode: display API request/response payloads and headers without actually sending data.
- Track and report counts: sent, created, updated, failed for each export operation.

### 8.5 Error Handling & Retry Strategy
- **Per-item error logging:** Detailed failure reasons for each failed testcase (e.g., "Invalid field 'priority'", "Network timeout", "API rate limit exceeded").
- **Exponential backoff retry:** Transient errors trigger automatic retry with increasing delay (1s, 2s, 4s, 8s, max 5 retries).
- **Error categories:**
  - `validation_error`: Invalid testcase data (missing field, invalid format)
  - `auth_error`: Authentication failure (invalid token, expired credentials)
  - `rate_limit_error`: API rate limit exceeded (auto-retry with backoff)
  - `network_error`: Connection timeout, DNS failure (auto-retry with backoff)
  - `server_error`: Target platform server error (auto-retry with backoff)
  - `persistent_error`: Unrecoverable error, requires manual intervention
- **Error messages:** Actionable remediation steps (e.g., "Renew API token", "Check field mapping", "Verify network connectivity").
- **Comprehensive Export Report:** Includes error summary, failed item details, and recovery recommendations.
- **Recovery actions:** User can retry failed items, adjust mapping, or export to different target after fixing errors.

### 8.6 Export Report Fields
- `target`: Export target name (e.g., "jira", "testrail", "zephyr", "markdown", "csv")
- `sent`: Number of testcases sent
- `created`: Number of testcases successfully created in target
- `updated`: Number of existing testcases updated in target
- `failed`: Number of testcases that failed; includes detailed error reasons per item
- `dry_run`: Boolean flag indicating if payload was simulated (true) or actually sent (false)
- `timestamp`: ISO 8601 timestamp of export operation
- `duration_seconds`: Total execution time
- `export_id`: Idempotency key (UUID) for tracking

### 8.7 Platform-Specific Export Behaviors

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

## 9. Security, Privacy & Audit

### 9.1 Data Handling & Consent
- Users should redact Personally Identifiable Information (PII) and secrets from specifications before processing.
- Tool provides optional automated redaction pass using regex patterns or user-defined rules.
- When exporting to third-party APIs, require explicit user consent and allow selective field masking.

### 9.2 Audit Trail & Logging
- Maintain export audit log: timestamp, user identifier, target platform, testcase count, operation result (success/failure).
- Store logs locally in `.stgqc_audit_log` file or configurable path.
- Users may clear audit logs at any time; cleared logs are not recoverable.

---

## 10. Extensibility & Configuration

### 10.1 Plugin & Adapter Points
- **Custom Analyzers:** Users can provide custom rule sets and heuristics for domain-specific analysis.
- **Custom Export Adapters:** Implement adapter interface to add support for additional test management platforms.

### 10.2 Configuration File Support (`stgqcrc.yaml` or `stgqcrc.json`)
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

### 10.3 Dependencies & Tech Stack
- **Runtime:** Python 3.8+ or Node.js 14+
- **Core Libraries:**
  - Markdown parser (markdown-it or python-markdown)
  - YAML/JSON configuration parser (PyYAML, json module)
  - HTTP client (requests library, axios, or built-in fetch)
  - Excel export (openpyxl, xlsx library)
  - NLP/Tokenization (NLTK, spaCy for advanced language handling)
- **External APIs:** Jira REST API, Zephyr API, TestRail API v2
- **Optional:** Docker for containerized deployment, Git for version control integration
- **Development:** Unit testing framework (pytest, Jest), linting (ESLint, pylint), documentation (Sphinx, JSDoc)

---

## 11. Performance Requirements

### 11.1 Response Time & Throughput
- **Specification parsing:** < 2 seconds for specifications up to 50 pages (Markdown format)
- **Test case generation:** < 5 seconds for typical 50-requirement specification
- **Export to platform:** < 30 seconds for batch of up to 500 test cases (including API latency)
- **Coverage report generation:** < 3 seconds

### 11.2 Scalability & Limits
- **Maximum testcases per export:** 5,000 (configurable)
- **Maximum specification size:** 100 MB (Markdown file)
- **Concurrent export operations:** Support at least 2 simultaneous exports
- **API rate limiting:** Respect platform rate limits (Jira: 100 req/min, Zephyr: 500 req/min, TestRail: 80 req/min)

### 11.3 Resource Requirements
- **Memory:** Base ~ 50 MB; + 1 MB per 1,000 testcases generated
- **Disk:** Configuration + glossary files ~ 10 MB; audit logs ~ 1 MB per 10,000 exports
- **Network:** Minimal (API calls only); no telemetry or background uploads

### 11.4 Availability & Reliability
- **Uptime target:** 99.5% (for SaaS/cloud deployment)
- **Error recovery:** Automatic retry with exponential backoff for transient failures
- **Idempotency:** Export operations are idempotent (safe to retry)

---

## 12. Deliverables & Outputs

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
  - **1–2 Security test cases** (authentication, authorization, input validation, data protection)
- The AI should flag ambiguous spec items and optionally suggest clarifying questions.

---

## 13. Security Testing

- Generate security testcases for: authentication, authorization, input validation, data protection, session management.
- Cover common threats: SQL injection, XSS, unauthorized access, weak passwords, expired sessions.
- Each testcase includes: `threat_vector` (threat type), `severity` (Critical/High/Medium/Low), `mitigation` (expected control).
- Minimum **1–2 security testcases per module** with authentication/authorization/data handling.
- Flag specifications missing security requirements.

---

## 14. Error Handling & Edge Case Testing

### 14.1 Error Scenario Coverage
- Generate testcases for error conditions: invalid input, missing data, timeout, network failures, resource exhaustion.
- Cover HTTP error codes: 400 (Bad Request), 401 (Unauthorized), 403 (Forbidden), 404 (Not Found), 500 (Server Error), 503 (Service Unavailable).
- Test error messages: clarity, no information disclosure, appropriate severity level.

### 14.1.1 Error Handling Overview
- **Centralized error logging:** All errors (validation, export, runtime) are logged with timestamp, operation, and detailed reason.
- **Error categorization:**
  - `validation_error`: Input/specification data invalid
  - `data_error`: Test data missing, malformed, or out-of-range
  - `export_error`: API/network/platform failures
  - `system_error`: Internal failures, resource exhaustion
- **User notification:** Actionable error messages and remediation steps are provided for each error.
- **Recovery:** User can retry, fix data, or adjust mapping as needed.

### 14.1.2 Error Handling Testcase Example
| error_code | trigger_condition | expected_behavior | recovery_action |
|------------|------------------|-------------------|-----------------|
| VALID-001 | Missing required field (e.g., email) | Error message shown, testcase not generated | User adds missing field, retry |
| DATA-002 | Test data out of allowed range | Error message shown, testcase flagged | User corrects data, retry |
| EXPORT-003 | API rate limit exceeded | Retry with backoff, error logged | Wait, retry later |
| SYSTEM-004 | Memory limit exceeded | Operation aborted, error logged | Increase memory, retry |

### 14.2 Recovery & Resilience Testing
- Testcases for system recovery after failure: retry logic, fallback mechanisms, data consistency after error.
- Validate error logging and monitoring: errors properly logged, alerting triggered for critical issues.
- Test graceful degradation: system continues functioning with reduced capacity when non-critical components fail.

### 14.3 Edge Case Scenarios
- Boundary conditions: max/min values, empty inputs, special characters, extremely large payloads.
- Concurrent operations: race conditions, deadlock prevention, resource contention.
- State transitions: invalid state changes, incomplete workflows, session expiration during operations.
- Timeout & rate limiting: slow responses, request throttling, backoff behavior.

### 14.4 Error Testcase Format
- `error_code`: System or HTTP error code (e.g., "ERR-001", "400", "TIMEOUT")
- `trigger_condition`: What action or condition causes the error
- `expected_behavior`: How system should respond (error message, state change, retry)
- `recovery_action`: User action or system action to recover from error
