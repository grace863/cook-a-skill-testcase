# AI Showcase — STG-QC: Smart Test Case Generator

> Tuyển chọn các conversation ấn tượng nhất để demo cho BGK.
> Mỗi showcase tập trung vào **một điểm mạnh khác nhau** của skill.

---

## Showcase 1 — One Command, 60 Test Cases, 100% Coverage

**Điểm muốn highlight:** Chỉ cần 1 lệnh slash command → AI tự động phân tích toàn bộ spec 56 requirement và xuất ra bộ test case hoàn chỉnh.

---

### Prompt

```
/stg-qc @demo-spec.md
```

---

### AI Response (trích đoạn)

**Step 0 — Spec Type Detection**

| Section | Feature Type |
|---------|-------------|
| 1.1 Add New Product | `[CRUD]` |
| 1.4 Product Image Upload | `[FILE]` |
| 2.1 Add to Cart | `[CRUD][STATE]` |
| 3.2 Payment Processing | `[PAY][STATE]` |
| 3.3 Order Status Flow | `[STATE]` |
| 4. Product Search & Filter | `[SEARCH]` |

> AI tự phân loại đúng từng section theo feature type — không cần user chỉ định.

---

**Step 1 — 56 Requirements được extract tự động**

| REQ ID | Feature Type | Section | Requirement Description |
|--------|--------------|---------|------------------------|
| REQ-001 | `[CRUD]` | 1.1 | Product Name required; 3–200 chars; unique per store |
| REQ-002 | `[CRUD]` | 1.1 | SKU required; alphanumeric+hyphens; 6–20 chars; unique system-wide |
| REQ-035 | `[PAY][STATE]` | 3.2 | Payment failure or timeout (15 min) → `payment_failed`; stock restored |
| REQ-040 | `[STATE]` | 3.3 | Any other transition rejected: `ERR-ORDER-INVALID-TRANSITION` |
| ... | ... | ... | *(56 requirements total)* |

---

**Step 3 — Test Cases (trích đoạn)**

**Module: Order & Payment — Edge Cases**

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | error_code | trigger_condition | expected_behavior | recovery_action |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|-----------|-------------------|-------------------|----------------|
| EDGE-ORDER-001 | Order & Payment | Double-submit checkout | User; cart has items | Two POST within 500ms | 1. Rapid double submit | 1 order only | Button disabled after first; 1 order in DB | High | — | Double-click | Deduplicate | Single confirmation shown |
| EDGE-ORDER-002 | Order & Payment | VNPAY timeout at 15-min boundary | ORD-202 VNPAY pending | Wait exactly 15 min | 1. Create VNPAY 2. Wait 15 min | payment_failed; stock restored | After 15 min: status=`payment_failed`; stock +restored | High | ERR-PAY-TIMEOUT | No callback in 15 min | Auto-fail | New order |

---

**Step 4 — Coverage Report**

```
Total Requirements Extracted: 56
Requirements Covered:         56
Coverage %:                   100.00%

By Category:
  Happy Path:    100.00%
  Negative Path:  85.71%
  Edge Cases:    100.00%
  Security:       78.57%

Module              | TC | NEG | EDGE | SEC | Total
--------------------|----|----|------|-----|------
Product Management  |  2 |  3 |   3  |  2  |  10
Image Upload        |  1 |  3 |   3  |  2  |   9
Shopping Cart       |  2 |  3 |   3  |  2  |  10
Order & Payment     |  3 |  3 |   3  |  2  |  11
Search & Filter     |  2 |  3 |   3  |  2  |  10
Review & Rating     |  2 |  3 |   3  |  2  |  10
TOTAL               | 12 | 18 |  18  | 12  |  60

Uncovered Requirements: None
Status: ✅ Meets threshold (≥80%)
```

> **1 lệnh → 56 requirements → 60 test cases → 100% coverage.**

---

---

## Showcase 2 — Security Test Generation Tự Động

**Điểm muốn highlight:** AI không chỉ sinh happy/negative path — nó tự phát hiện các security threat theo từng module và sinh test case có đủ `threat_vector`, `severity`, `mitigation`.

---

### Prompt

```
/stg-qc @demo-spec.md

Tôi đặc biệt muốn xem phần Security test cases cho Order & Payment.
```

---

### AI Response

> AI tự phát hiện 2 security threat nghiêm trọng nhất trong luồng thanh toán mà spec **không đề cập**.

**Module: Order & Payment — Security**

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | threat_vector | severity | mitigation |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|--------------|---------|-----------|
| SEC-ORDER-001 | Order & Payment | Payment amount tampering | VNPAY order 500,000 VND | Intercept; change to 1 VND | 1. Checkout 2. Intercept callback 3. Modify amount 4. Submit | Server validates original amount | Mismatch → reject; amount from order record, not params | Critical | Payment Tampering | **Critical** | HMAC; server-side validation |
| SEC-ORDER-002 | Order & Payment | Replay payment callback | ORD-204 confirmed | Resend same callback | 1. Resend confirmed callback | Idempotent; ignored | No re-processing; no duplicate stock deduction | High | **Replay Attack** | High | Idempotency key; mark TX processed |

**Và với Image Upload:**

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | threat_vector | severity | mitigation |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|--------------|---------|-----------|
| SEC-IMG-001 | Image Upload | Path traversal in filename | Admin; PRD-001 | `../../etc/passwd.jpg` | 1. POST malicious filename | Sanitized | No file outside upload dir; filename normalized | High | **Path Traversal** | High | UUID storage names |

> AI tự nhận ra:
> - `[PAY]` → kiểm tra **payment tampering** và **replay attack**
> - `[FILE]` → kiểm tra **path traversal**
> - Không cần user hướng dẫn — dựa hoàn toàn vào type-specific checklist

---

---

## Showcase 3 — Xử Lý Spec Thiếu Sót / Mơ Hồ

**Điểm muốn highlight:** AI không từ chối hay bỏ qua khi spec không đầy đủ — nó tự apply defaults, annotate `⚠️ ASSUMED:`, và vẫn sinh test case.

---

### Prompt

```
Đây là spec sơ bộ cho tính năng Notification. Hãy sinh test case:

## Notifications
- Hệ thống gửi email khi đơn hàng được xác nhận.
- Gửi thông báo push khi hàng được ship.
```

*(Spec rất sơ sài: không có error handling, không có retry policy, không có trường dữ liệu cụ thể)*

---

### AI Response

**Step 0 — Spec Type Detection**

| Section | Feature Type |
|---------|-------------|
| Notifications | `[NOTIFY]` |

---

**Step 1 — Extracted Requirements**

| REQ ID | Feature Type | Requirement Description |
|--------|--------------|------------------------|
| REQ-001 | `[NOTIFY]` | Send email when order is confirmed |
| REQ-002 | `[NOTIFY]` | Send push notification when order is shipped |

> ⚠️ **AMBIGUITY:** Email recipient not specified — assumed to be the order owner's registered email.
> ⚠️ **ASSUMED:** Retry policy not specified — defaulting to 3 retries with exponential backoff.
> ⚠️ **MISSING SECURITY REQ:** No mention of email header injection protection or PII handling in notifications.

---

**Step 3 — Test Cases**

**Happy Path**

| ID | Scenario | Test Data | Expected Result | Acceptance Criteria | Priority |
|----|----------|-----------|-----------------|---------------------|----------|
| TC-NOTIFY-001 | Email sent on order confirmed | ORD-001 → `confirmed` | Email received by order owner | Email delivered ≤ 30s; correct subject/body | High |
| TC-NOTIFY-002 | Push notification on shipped | ORD-002 → `shipped` | Push received on user's device | Notification delivered ≤ 10s | High |

**Edge Cases** *(từ `[NOTIFY]` checklist — spec không đề cập nhưng AI tự sinh)*

| ID | Scenario | Test Data | Expected Result | Priority | trigger_condition | recovery_action |
|----|----------|-----------|-----------------|----------|-------------------|----------------|
| EDGE-NOTIFY-001 | Email to deactivated account | User deactivated; order confirmed | ⚠️ ASSUMED: skip or queue | Medium | Deactivated account | Log; do not send |
| EDGE-NOTIFY-002 | Duplicate trigger (same event twice) | ORD-001 confirmed event fires 2x | Only 1 email sent | High | Race condition | Idempotency key |
| EDGE-NOTIFY-003 | Retry exhausted after 3 failures | Email server down; 3 retries fail | Error logged; alert raised | Medium | Delivery failure × 3 | Manual resend |

**Security** *(AI flag luôn dù spec không nhắc)*

| ID | Scenario | threat_vector | severity | mitigation |
|----|----------|--------------|---------|-----------|
| SEC-NOTIFY-001 | Email header injection | `email=user@x.com%0ATo:attacker@evil.com` | **Email Header Injection** | High | Sanitize recipient field |
| SEC-NOTIFY-002 | PII exposure in notification content | Push body shows full card number | **PII Exposure** | Critical | Mask sensitive data |

---

> **Kết quả:** Spec chỉ có 2 dòng → AI sinh được 9 test cases có chiều sâu, bao gồm cả security threats mà spec không đề cập.

---

---

## Showcase 4 — Interactive Follow-up: Deepdive Một Module

**Điểm muốn highlight:** Sau khi đã chạy `/stg-qc`, user có thể tiếp tục hỏi sâu hơn về một module cụ thể. AI nhớ context và có thể sinh thêm test case theo yêu cầu.

---

### Prompt (sau khi đã chạy Showcase 1)

```
Nhìn vào Order Status Flow (Section 3.3), tôi thấy spec có 7 valid transitions.
Hãy sinh test case riêng cho tất cả các INVALID transitions — tức là mọi cặp
(from_state, to_state) không có trong danh sách valid.
```

---

### AI Response

> AI nhận ra đây là `[STATE]` — áp dụng ngay checklist "all invalid transitions rejected".
> Tự tính ra các cặp invalid từ state machine diagram trong spec.

**Invalid Transitions — Order Status**

Valid states: `pending`, `confirmed`, `processing`, `shipped`, `delivered`, `payment_failed`, `cancelled`

Valid transitions theo spec:
```
pending → confirmed
confirmed → processing
processing → shipped
shipped → delivered
pending → cancelled (user)
confirmed → cancelled (admin)
payment_failed → [end]
```

**Test Cases cho Invalid Transitions:**

| ID | Scenario | From State | To State | Expected | error_code |
|----|----------|-----------|---------|---------|-----------|
| EDGE-STATE-001 | confirmed → pending (rollback) | `confirmed` | `pending` | HTTP 400; `ERR-ORDER-INVALID-TRANSITION` | ERR-ORDER-INVALID-TRANSITION |
| EDGE-STATE-002 | processing → confirmed | `processing` | `confirmed` | HTTP 400; `ERR-ORDER-INVALID-TRANSITION` | ERR-ORDER-INVALID-TRANSITION |
| EDGE-STATE-003 | shipped → processing (rollback) | `shipped` | `processing` | HTTP 400 | ERR-ORDER-INVALID-TRANSITION |
| EDGE-STATE-004 | delivered → any state | `delivered` | `confirmed` | HTTP 400; terminal state | ERR-ORDER-INVALID-TRANSITION |
| EDGE-STATE-005 | cancelled → any state | `cancelled` | `pending` | HTTP 400; terminal state | ERR-ORDER-INVALID-TRANSITION |
| EDGE-STATE-006 | payment_failed → confirmed (bypass) | `payment_failed` | `confirmed` | HTTP 400; no bypass | ERR-ORDER-INVALID-TRANSITION |
| EDGE-STATE-007 | Customer cancels confirmed (role check) | `confirmed` | `cancelled` | HTTP 403; "admin only" | ERR-AUTH-FORBIDDEN |
| EDGE-STATE-008 | Concurrent race: 2 admins ship simultaneously | `processing` | `shipped` × 2 | Only 1 succeeds; no duplicate | Race condition handled |

> **8 test cases riêng cho invalid transitions** — sinh trong vài giây, không bỏ sót cặp nào.

---

---

## Tổng Kết

| Showcase | Điểm Mạnh Được Demo |
|----------|---------------------|
| **#1** One command → 60 TC | Tốc độ & độ phủ — 56 REQ, 100% coverage, 1 lệnh |
| **#2** Security tự động | Phát hiện threat mà spec không đề cập (Payment Tampering, Path Traversal) |
| **#3** Spec thiếu sót | Không từ chối — tự apply defaults, annotate ⚠️ ASSUMED, sinh đủ test |
| **#4** Follow-up interactive | Deepdive state machine: 8 invalid transitions không bỏ sót |

> **Điểm khác biệt cốt lõi:** STG-QC không sinh test case generic.
> Nó **biết bạn đang test cái gì** (`[PAY]`, `[STATE]`, `[FILE]`...)
> và áp dụng đúng checklist cho từng loại — giống như một senior QC engineer thực thụ.
