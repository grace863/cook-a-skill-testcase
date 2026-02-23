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

## Notes / Implementation Hints

- The generator should prioritize test cases by likely business impact (Priority: High / Medium / Low).
- For each primary feature the AI should output at minimum:
  - 1–2 Happy Path test cases
  - 2–3 Negative Path test cases
  - 3 Edge Cases (boundary, performance, concurrency, invalid input, etc.)
- The AI should flag ambiguous spec items and optionally suggest clarifying questions.
