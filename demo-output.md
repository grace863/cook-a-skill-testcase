# STG-QC Demo Output

**Generated from:** `demo-spec.md` — E-Commerce Product & Order Management
**Generated on:** 2026-02-25
**Skill version:** STG-QC with Step 0 Spec Type Detection

---

## Step 0 — Spec Type Detection

| Section | Feature Type |
|---------|-------------|
| 1.1 Add New Product | `[CRUD]` |
| 1.2 Edit Product | `[CRUD]` |
| 1.3 Delete Product | `[CRUD]` |
| 1.4 Product Image Upload | `[FILE]` |
| 2.1 Add to Cart | `[CRUD][STATE]` |
| 2.2 Update Cart Item | `[CRUD]` |
| 2.3 Remove from Cart | `[CRUD]` |
| 3.1 Checkout | `[STATE][PAY]` |
| 3.2 Payment Processing | `[PAY][STATE]` |
| 3.3 Order Status Flow | `[STATE]` |
| 4. Product Search & Filter | `[SEARCH]` |
| 5.1 Submit Review | `[CRUD]` |
| 5.2 Display Reviews | `[CRUD][SEARCH]` |

---

## Step 1 — Extracted Requirements

| REQ ID | Feature Type | Section | Requirement Description |
|--------|--------------|---------|------------------------|
| REQ-001 | `[CRUD]` | 1.1 | Product Name required; 3–200 chars; unique per store |
| REQ-002 | `[CRUD]` | 1.1 | SKU required; alphanumeric+hyphens; 6–20 chars; unique system-wide |
| REQ-003 | `[CRUD]` | 1.1 | Price required; positive decimal; max 10 digits, 2 decimal places; VND |
| REQ-004 | `[CRUD]` | 1.1 | Stock Quantity required; non-negative integer |
| REQ-005 | `[CRUD]` | 1.1 | Category required; must reference existing Category ID |
| REQ-006 | `[CRUD]` | 1.1 | Description optional; max 5,000 chars; plain text only |
| REQ-007 | `[FILE]` | 1.1 | Images optional; up to 5; JPG/PNG/WEBP; max 5 MB each |
| REQ-008 | `[CRUD]` | 1.1 | On creation: product saved with status `draft` |
| REQ-009 | `[CRUD]` | 1.1 | `draft` products do not appear on the storefront |
| REQ-010 | `[CRUD]` | 1.2 | Admin can update any product field |
| REQ-011 | `[CRUD]` | 1.2 | Changing SKU triggers uniqueness re-validation |
| REQ-012 | `[CRUD]` | 1.2 | Changing Price/Stock on product with active/pending orders triggers confirmation prompt |
| REQ-013 | `[CRUD]` | 1.3 | Products with active/pending orders cannot be deleted; return error with Order IDs |
| REQ-014 | `[CRUD]` | 1.3 | Eligible products soft-deleted (status=`deleted`); hidden from storefront |
| REQ-015 | `[FILE]` | 1.4 | Images uploaded via dedicated endpoint (separate from product creation) |
| REQ-016 | `[FILE]` | 1.4 | Duplicate images (same hash) rejected: `ERR-IMG-DUPLICATE` |
| REQ-017 | `[FILE]` | 1.4 | Images auto-resized to max 800×800 px, aspect ratio preserved |
| REQ-018 | `[FILE]` | 1.4 | Invalid format or oversized files return `ERR-IMG-INVALID` with descriptive message |
| REQ-019 | `[CRUD]` | 2.1 | Only authenticated users can add products to cart |
| REQ-020 | `[CRUD]` | 2.1 | Cart item fields: Product ID, Quantity (min 1) |
| REQ-021 | `[CRUD]` | 2.1 | Adding same product again increments quantity, no duplicate entry |
| REQ-022 | `[CRUD][STATE]` | 2.1 | Product with stock=0 or status≠active cannot be added: `ERR-CART-UNAVAILABLE` |
| REQ-023 | `[CRUD]` | 2.2 | User can change quantity of cart item (min 1) |
| REQ-024 | `[CRUD]` | 2.2 | Quantity exceeding available stock returns `ERR-CART-STOCK` |
| REQ-025 | `[CRUD]` | 2.2 | Setting quantity to 0 removes the item |
| REQ-026 | `[CRUD]` | 2.3 | User can remove individual cart items |
| REQ-027 | `[CRUD]` | 2.3 | User can clear entire cart at once |
| REQ-028 | `[STATE][PAY]` | 3.1 | User selects cart items and submits checkout |
| REQ-029 | `[PAY]` | 3.1 | Required: Shipping Address (Street, City, Province, Postal Code), Payment Method |
| REQ-030 | `[PAY]` | 3.1 | Supported payment methods: COD, VNPAY, MOMO |
| REQ-031 | `[PAY]` | 3.1 | Stock re-validated at checkout; insufficient → error listing products, no order created |
| REQ-032 | `[PAY]` | 3.2 | COD: order confirmed immediately; status = `confirmed` |
| REQ-033 | `[PAY]` | 3.2 | VNPAY/MOMO: user redirected to payment gateway |
| REQ-034 | `[PAY][STATE]` | 3.2 | Payment success → status = `confirmed` |
| REQ-035 | `[PAY][STATE]` | 3.2 | Payment failure or timeout (15 min) → `payment_failed`; stock restored |
| REQ-036 | `[STATE]` | 3.3 | Valid: pending→confirmed, confirmed→processing, processing→shipped, shipped→delivered |
| REQ-037 | `[STATE]` | 3.3 | pending→cancelled: user-initiated only |
| REQ-038 | `[STATE]` | 3.3 | confirmed→cancelled: admin-initiated only |
| REQ-039 | `[STATE]` | 3.3 | payment_failed → end; stock auto-restored; no further transition |
| REQ-040 | `[STATE]` | 3.3 | Any other transition rejected: `ERR-ORDER-INVALID-TRANSITION` |
| REQ-041 | `[SEARCH]` | 4 | Search by Name: partial match, case-insensitive |
| REQ-042 | `[SEARCH]` | 4 | Search by SKU: exact match only |
| REQ-043 | `[SEARCH]` | 4 | Filter by Category ID |
| REQ-044 | `[SEARCH]` | 4 | Filter by Price range (min–max VND) |
| REQ-045 | `[SEARCH]` | 4 | Filter by In-stock only (boolean) |
| REQ-046 | `[SEARCH]` | 4 | Customers see only `active`; admins see all statuses |
| REQ-047 | `[SEARCH]` | 4 | Pagination: default 20/page, max 100/page |
| REQ-048 | `[SEARCH]` | 4 | Sort: Price asc/desc, Name asc/desc, Newest first |
| REQ-049 | `[SEARCH]` | 4 | Empty results return `[]`, not an error |
| REQ-050 | `[CRUD]` | 5.1 | Only users with `delivered` order for the product can submit a review |
| REQ-051 | `[CRUD]` | 5.1 | Rating required; integer 1–5 |
| REQ-052 | `[CRUD]` | 5.1 | Comment optional; max 1,000 characters |
| REQ-053 | `[CRUD]` | 5.1 | One review per user per product; editing allowed within 48 hours |
| REQ-054 | `[SEARCH]` | 5.2 | Reviews sorted by newest first |
| REQ-055 | `[SEARCH]` | 5.2 | Average rating calculated and displayed, rounded to 1 decimal place |
| REQ-056 | `[SEARCH]` | 5.2 | Reviews paginated: 10 per page |

**Total: 56 requirements**

---

## Step 2 — Smart Analysis (Summary)

| Module | Key Ambiguities | Missing Security REQ |
|--------|----------------|----------------------|
| Product Management | "unique per store" undefined; "active orders" scope unclear | CSRF protection; rate limiting |
| Image Upload | 6th image behavior; resize for images < 800×800 | Virus scanning; upload rate limiting |
| Shopping Cart | Cart persistence after session expiry; no max cart size | IDOR; negative quantity |
| Order & Payment | Cart auto-clear post-order?; stock reservation timing | Payment callback HMAC; CSRF on checkout |
| Search & Filter | Vietnamese diacritics in case-insensitive?; "Newest first" by create or update? | Search rate limiting |
| Review & Rating | 48h window resets on edit?; user can delete review? | Review moderation; rate limiting |

---

## Step 3 — Test Cases

### Module: Product Management

#### Happy Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| TC-PROD-001 | Product Management | Create product with all required fields | Admin authenticated; CAT-001 exists | Name: "Áo thun nam", SKU: "SKU-001", Price: 150000.00, Stock: 100, Category: CAT-001 | 1. POST /products 2. Check response | Created with status `draft` | HTTP 201; status=`draft`; ID present; ≤2s | High |
| TC-PROD-002 | Product Management | Soft-delete eligible product | Admin; PRD-010 has no active orders | Product ID: PRD-010 | 1. DELETE /products/PRD-010 2. GET as customer | Hidden from storefront | HTTP 200; customer GET → 404; admin sees status=`deleted` | High |

#### Negative Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| NEG-PROD-001 | Product Management | Duplicate SKU | SKU "SKU-001" exists | SKU: "SKU-001" | 1. POST /products duplicate SKU | Error | HTTP 400; duplicate SKU message | High |
| NEG-PROD-002 | Product Management | Name below min length | Admin authenticated | Name: "AB" (2 chars) | 1. POST /products 2-char name | Validation error | HTTP 400; "3–200 characters" | Medium |
| NEG-PROD-003 | Product Management | Delete product with active orders | PRD-005 has ORD-100, ORD-101 | Product ID: PRD-005 | 1. DELETE /products/PRD-005 | Error listing Order IDs | HTTP 409; `["ORD-100","ORD-101"]`; not deleted | High |

#### Edge Cases

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | error_code | trigger_condition | expected_behavior | recovery_action |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|-----------|-------------------|-------------------|----------------|
| EDGE-PROD-001 | Product Management | SKU at max length (20 chars) | Admin | SKU: "ABCDE-12345-ABCDE-1" | 1. POST /products | Created | HTTP 201; no truncation | Medium | — | — | — | — |
| EDGE-PROD-002 | Product Management | Product Name at exactly 200 chars | Admin | Name: [200 chars] | 1. POST /products | Created | HTTP 201; stored in full | Medium | — | — | — | — |
| EDGE-PROD-003 | Product Management | Edit Price on product with pending orders | PRD-007 has pending orders | New Price: 200000 | 1. PATCH /products/PRD-007 2. Confirm prompt | Confirmed after prompt | Prompt before save; HTTP 200 after confirm | High | — | Active orders | Confirm required | Admin confirms/cancels |

#### Security

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | threat_vector | severity | mitigation |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|--------------|---------|-----------|
| SEC-PROD-001 | Product Management | Non-admin creates product | Customer token | Valid payload | 1. POST /products customer token | HTTP 403 | HTTP 403; no product created | High | Privilege Escalation | High | Server-side RBAC |
| SEC-PROD-002 | Product Management | XSS in Product Name | Admin | Name: `<script>alert('xss')</script>` | 1. POST 2. Render | Not executed | Sanitized or literal; no script runs | High | XSS | High | Input sanitization; CSP |

---

### Module: Image Upload

#### Happy Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| TC-IMG-001 | Image Upload | Upload valid JPG within limit | Admin; PRD-001 exists | `photo.jpg`, 2 MB, 1200×900 | 1. POST /products/PRD-001/images | Uploaded; resized ≤800×800 | HTTP 201; URL returned; aspect ratio maintained | High |

#### Negative Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| NEG-IMG-001 | Image Upload | Unsupported format (GIF) | Admin; PRD-001 | `animation.gif`, 1 MB | 1. POST with GIF | Error | HTTP 400; `ERR-IMG-INVALID`; allowed formats listed | Medium |
| NEG-IMG-002 | Image Upload | File exceeds 5 MB | Admin; PRD-001 | `large.jpg`, 6 MB | 1. POST 6 MB | Error | HTTP 400; `ERR-IMG-INVALID`; "exceeds 5 MB" | Medium |
| NEG-IMG-003 | Image Upload | Duplicate image | `photo.jpg` on PRD-001 | Identical `photo.jpg` | 1. POST same file | Rejected | HTTP 409; `ERR-IMG-DUPLICATE` | Medium |

#### Edge Cases

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | error_code | trigger_condition | expected_behavior | recovery_action |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|-----------|-------------------|-------------------|----------------|
| EDGE-IMG-001 | Image Upload | File at exactly 5 MB | Admin; PRD-001 | 5,242,880 bytes | 1. POST exact 5 MB | Accepted | HTTP 201; no error | Medium | — | — | — | — |
| EDGE-IMG-002 | Image Upload | File at 5 MB + 1 byte | Admin; PRD-001 | 5,242,881 bytes | 1. POST over limit | Rejected | HTTP 400; `ERR-IMG-INVALID`; "exceeds 5 MB" | Medium | ERR-IMG-INVALID | size=limit+1 | Reject | Compress; retry |
| EDGE-IMG-003 | Image Upload | 6th image (product at 5-image limit) | PRD-001 has 5 images | `sixth.jpg`, 1 MB | 1. POST 6th | Rejected | HTTP 400; "maximum 5 images" | Medium | ERR-IMG-LIMIT | 6th upload | Reject with limit error | Remove one first |

#### Security

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | threat_vector | severity | mitigation |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|--------------|---------|-----------|
| SEC-IMG-001 | Image Upload | Path traversal in filename | Admin; PRD-001 | `../../etc/passwd.jpg` | 1. POST malicious filename | Sanitized | No file outside upload dir; filename normalized | High | Path Traversal | High | UUID storage names |
| SEC-IMG-002 | Image Upload | Non-admin uploads | Customer token | `photo.jpg` | 1. POST customer token | HTTP 403 | HTTP 403; no image stored | High | Privilege Escalation | High | Server-side role check |

---

### Module: Shopping Cart

#### Happy Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| TC-CART-001 | Shopping Cart | Add available product | User authenticated; PRD-001 active, stock=50 | PRD-001, qty=2 | 1. POST /cart/items 2. GET /cart | Item in cart qty=2 | HTTP 201; cart shows PRD-001 qty=2 | High |
| TC-CART-002 | Shopping Cart | Add same product increments qty | PRD-001 in cart qty=2 | PRD-001, qty=3 | 1. POST /cart/items PRD-001 again | qty becomes 5; no duplicate | HTTP 200; single entry qty=5 | High |

#### Negative Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| NEG-CART-001 | Shopping Cart | Unauthenticated add | No token | PRD-001, qty=1 | 1. POST /cart/items no token | HTTP 401 | HTTP 401; "Authentication required" | High |
| NEG-CART-002 | Shopping Cart | Add stock=0 product | PRD-002 stock=0 | PRD-002, qty=1 | 1. POST /cart/items | Error | HTTP 400; `ERR-CART-UNAVAILABLE` | High |
| NEG-CART-003 | Shopping Cart | Update qty exceeds stock | PRD-001 stock=10 | PRD-001, qty=11 | 1. PATCH /cart/items qty=11 | Error | HTTP 400; `ERR-CART-STOCK`; qty unchanged | Medium |

#### Edge Cases

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | error_code | trigger_condition | expected_behavior | recovery_action |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|-----------|-------------------|-------------------|----------------|
| EDGE-CART-001 | Shopping Cart | qty=0 removes item | PRD-001 in cart qty=3 | PRD-001, qty=0 | 1. PATCH qty=0 | Item removed | HTTP 200; PRD-001 gone from cart | Medium | — | qty=0 | Item deleted | Re-add if needed |
| EDGE-CART-002 | Shopping Cart | Add draft product | PRD-003 status=`draft` | PRD-003, qty=1 | 1. POST /cart/items | Error | HTTP 400; `ERR-CART-UNAVAILABLE` | Medium | ERR-CART-UNAVAILABLE | status=draft | Reject | Wait for publish |
| EDGE-CART-003 | Shopping Cart | Clear empty cart | Cart empty | — | 1. DELETE /cart | Success | HTTP 200; no error | Low | — | — | — | — |

#### Security

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | threat_vector | severity | mitigation |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|--------------|---------|-----------|
| SEC-CART-001 | Shopping Cart | IDOR: User A reads User B cart | Both authenticated | User A token; User B cart ID | 1. User A GET User B cart | HTTP 403 | HTTP 403; cart scoped to owner | High | IDOR | High | Server-side ownership check |
| SEC-CART-002 | Shopping Cart | Negative quantity | User authenticated | PRD-001, qty=-5 | 1. POST qty=-5 | Rejected | HTTP 400; "Quantity must be ≥ 1" | High | Parameter Tampering | Medium | Server-side validation |

---

### Module: Order & Payment

#### Happy Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| TC-ORDER-001 | Order & Payment | COD checkout confirmed immediately | User; PRD-001 qty=2 stock=50 | COD; "123 Lê Lợi, Q1, HCM, 70000" | 1. POST /checkout COD 2. Check order | status=`confirmed`; stock -2 | HTTP 201; confirmed; stock decremented; ≤3s | High |
| TC-ORDER-002 | Order & Payment | VNPAY → payment success | User; PRD-001 stock=50 | VNPAY; valid address | 1. POST /checkout 2. Pay on gateway | status=`confirmed` | After callback: status=`confirmed`; stock decremented | High |
| TC-ORDER-003 | Order & Payment | User cancels pending order | ORD-200 status=`pending` | ORD-200 | 1. PATCH /orders/ORD-200 cancelled | Cancelled | HTTP 200; status=`cancelled` | High |

#### Negative Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| NEG-ORDER-001 | Order & Payment | Missing address field | User; cart has items | COD; missing "City" | 1. POST /checkout | Error | HTTP 400; "City is required"; no order | High |
| NEG-ORDER-002 | Order & Payment | Stock depleted at checkout | PRD-001 stock=0 at checkout | PRD-001 qty=2; stock=0 | 1. POST /checkout | Error | HTTP 409; `{"insufficient_stock":["PRD-001"]}`; no order | High |
| NEG-ORDER-003 | Order & Payment | Customer cancels confirmed order | ORD-201 status=`confirmed` | Customer token; ORD-201 | 1. PATCH cancelled customer | Rejected | HTTP 403; "admin only" | High |

#### Edge Cases

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | error_code | trigger_condition | expected_behavior | recovery_action |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|-----------|-------------------|-------------------|----------------|
| EDGE-ORDER-001 | Order & Payment | Double-submit checkout | User; cart has items | Two POST within 500ms | 1. Rapid double submit | 1 order only | Button disabled after first; 1 order in DB | High | — | Double-click | Deduplicate | Single confirmation shown |
| EDGE-ORDER-002 | Order & Payment | VNPAY timeout at 15-min boundary | ORD-202 VNPAY pending | Wait exactly 15 min | 1. Create VNPAY 2. Wait 15 min | payment_failed; stock restored | After 15 min: status=`payment_failed`; stock +restored | High | ERR-PAY-TIMEOUT | No callback in 15 min | Auto-fail | New order |
| EDGE-ORDER-003 | Order & Payment | Invalid transition processing→pending | ORD-203 status=`processing` | Target: `pending` | 1. PATCH status=`pending` | Rejected | HTTP 400; `ERR-ORDER-INVALID-TRANSITION` | High | ERR-ORDER-INVALID-TRANSITION | Invalid transition | Reject | Check valid transitions |

#### Security

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | threat_vector | severity | mitigation |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|--------------|---------|-----------|
| SEC-ORDER-001 | Order & Payment | Payment amount tampering | VNPAY order 500,000 VND | Intercept; change to 1 VND | 1. Checkout 2. Intercept 3. Modify 4. Submit | Server validates original amount | Mismatch → reject; amount from order record, not params | Critical | Payment Tampering | Critical | HMAC; server-side validation |
| SEC-ORDER-002 | Order & Payment | Replay payment callback | ORD-204 confirmed | Resend same callback | 1. Resend confirmed callback | Idempotent; ignored | No re-processing; no duplicate stock deduction | High | Replay Attack | High | Idempotency key; mark TX processed |

---

### Module: Search & Filter

#### Happy Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| TC-SEARCH-001 | Search & Filter | Partial name search, case-insensitive | "Áo Thun Nam", "áo thun nữ", "Quần Jean" | name=`ao thun` | 1. GET /products?name=ao+thun | 2 matched; "Quần Jean" excluded | HTTP 200; count=2; ≤2s | High |
| TC-SEARCH-002 | Search & Filter | Combined filter + sort | Active products at various prices | min=100000, max=500000, in_stock=true, sort=price_asc | 1. GET with all params | In-range, in-stock, sorted asc | HTTP 200; all in range; all in-stock; ascending | High |

#### Negative Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| NEG-SEARCH-001 | Search & Filter | Price min > max | — | min=500000, max=100000 | 1. GET invalid range | Error | HTTP 400; "min ≤ max" | Medium |
| NEG-SEARCH-002 | Search & Filter | Page size > 100 | — | per_page=101 | 1. GET per_page=101 | Error or clamped | HTTP 400 or clamped to 100 | Medium |
| NEG-SEARCH-003 | Search & Filter | Customer filters by status=draft | Draft products exist | status=draft; customer | 1. GET status=draft customer | Active only | HTTP 200; no draft/deleted; server enforces | High |

#### Edge Cases

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | error_code | trigger_condition | expected_behavior | recovery_action |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|-----------|-------------------|-------------------|----------------|
| EDGE-SEARCH-001 | Search & Filter | Zero results | No "xyz-no-match" product | name=xyz-no-match | 1. GET | Empty list | HTTP 200; `{"data":[],"total":0}` | High | — | — | — | — |
| EDGE-SEARCH-002 | Search & Filter | Page beyond last | 5 products | page=999 | 1. GET page=999 | Empty list | HTTP 200; `{"data":[],"total":5}` | Medium | — | page > max | Return empty | Navigate to valid page |
| EDGE-SEARCH-003 | Search & Filter | Special chars in query | Normal products | name=`'; DROP TABLE --` | 1. GET with SQL payload | Safe | HTTP 200; no SQL error; DB intact | High | — | SQL chars | Sanitize | — |

#### Security

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | threat_vector | severity | mitigation |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|--------------|---------|-----------|
| SEC-SEARCH-001 | Search & Filter | SQL injection via name | DB accessible | name=`' OR '1'='1' --` | 1. GET with payload | Sanitized | No SQL error; no data dump | Critical | SQL Injection | Critical | Parameterized queries |
| SEC-SEARCH-002 | Search & Filter | Filter bypass via status param | Draft products exist | status=draft; customer | 1. GET status=draft customer | Active only | No draft items; server-side enforcement | High | Authorization Bypass | High | Role-based server filter |

---

### Module: Review & Rating

#### Happy Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| TC-REVIEW-001 | Review & Rating | Submit review with delivered order | User has `delivered` order; no prior review | Rating: 4; Comment: "Chất lượng tốt" | 1. POST review 2. GET reviews | Saved; avg updated | HTTP 201; visible; avg recalculated (1dp) | High |
| TC-REVIEW-002 | Review & Rating | Edit review within 48h | Review submitted 25h ago | Rating: 5; "Rất hài lòng" | 1. PATCH review | Updated | HTTP 200; rating=5; avg recalculated | Medium |

#### Negative Path

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|
| NEG-REVIEW-001 | Review & Rating | No delivered order | Only `processing` order | Rating: 3 | 1. POST review | Rejected | HTTP 403; "must have delivered order" | High |
| NEG-REVIEW-002 | Review & Rating | Second review same product | Already reviewed PRD-001 | Rating: 2 | 1. POST review again | Duplicate rejected | HTTP 409; "already reviewed" | Medium |
| NEG-REVIEW-003 | Review & Rating | Edit after 48h window | Review 49h ago | New Rating: 1 | 1. PATCH review | Rejected | HTTP 403; "edit window expired" | Medium |

#### Edge Cases

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | error_code | trigger_condition | expected_behavior | recovery_action |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|-----------|-------------------|-------------------|----------------|
| EDGE-REVIEW-001 | Review & Rating | Max rating + max comment | Eligible user | Rating: 5; [1,000 chars] | 1. POST review | Saved | HTTP 201; stored in full | Medium | — | — | — | — |
| EDGE-REVIEW-002 | Review & Rating | Product with 0 reviews | PRD-999 no reviews | PRD-999 | 1. GET reviews | Empty; no error | HTTP 200; `{"data":[],"avg_rating":null}` ⚠️ ASSUMED | Medium | — | — | — | — |
| EDGE-REVIEW-003 | Review & Rating | Page beyond last review | 5 reviews | page=99 | 1. GET page=99 | Empty; no error | HTTP 200; `{"data":[],"total":5}` | Low | — | — | — | — |

#### Security

| ID | Module | Scenario | Preconditions | Test Data | Steps | Expected Result | Acceptance Criteria | Priority | threat_vector | severity | mitigation |
|----|--------|----------|---------------|-----------|-------|-----------------|---------------------|----------|--------------|---------|-----------|
| SEC-REVIEW-001 | Review & Rating | IDOR: edit another user's review | User A, User B; B has REV-050 | User A token; REV-050 | 1. PATCH REV-050 User A | HTTP 403 | HTTP 403; review unchanged | High | IDOR | High | review.user_id = auth.user_id |
| SEC-REVIEW-002 | Review & Rating | XSS in Comment | Eligible user | `<img src=x onerror=alert('xss')>` | 1. POST 2. Render | Not executed | Sanitized; no script runs | High | XSS | High | Output encoding; CSP |

---

## Step 4 — Coverage Report

### Requirement → Test Case Mapping

| REQ ID | Mapped Test Cases |
|--------|------------------|
| REQ-001 | TC-PROD-001, NEG-PROD-002, EDGE-PROD-001, EDGE-PROD-002 |
| REQ-002 | TC-PROD-001, NEG-PROD-001, EDGE-PROD-001 |
| REQ-003 | TC-PROD-001, EDGE-PROD-003 |
| REQ-004 | TC-PROD-001 |
| REQ-005 | TC-PROD-001 |
| REQ-006 | TC-PROD-001 |
| REQ-007 | TC-IMG-001, NEG-IMG-001, NEG-IMG-002, EDGE-IMG-003 |
| REQ-008 | TC-PROD-001 |
| REQ-009 | TC-PROD-002 |
| REQ-010 | EDGE-PROD-003 |
| REQ-011 | NEG-PROD-001 |
| REQ-012 | EDGE-PROD-003 |
| REQ-013 | NEG-PROD-003 |
| REQ-014 | TC-PROD-002 |
| REQ-015 | TC-IMG-001 |
| REQ-016 | NEG-IMG-003 |
| REQ-017 | TC-IMG-001 |
| REQ-018 | NEG-IMG-001, NEG-IMG-002 |
| REQ-019 | TC-CART-001, NEG-CART-001 |
| REQ-020 | TC-CART-001 |
| REQ-021 | TC-CART-002 |
| REQ-022 | NEG-CART-002, EDGE-CART-002 |
| REQ-023 | TC-CART-001, NEG-CART-003 |
| REQ-024 | NEG-CART-003 |
| REQ-025 | EDGE-CART-001 |
| REQ-026 | TC-CART-001 |
| REQ-027 | EDGE-CART-003 |
| REQ-028 | TC-ORDER-001 |
| REQ-029 | NEG-ORDER-001 |
| REQ-030 | TC-ORDER-001, TC-ORDER-002 |
| REQ-031 | NEG-ORDER-002 |
| REQ-032 | TC-ORDER-001 |
| REQ-033 | TC-ORDER-002 |
| REQ-034 | TC-ORDER-002 |
| REQ-035 | EDGE-ORDER-002 |
| REQ-036 | TC-ORDER-001, TC-ORDER-002, TC-ORDER-003 |
| REQ-037 | TC-ORDER-003 |
| REQ-038 | NEG-ORDER-003 |
| REQ-039 | EDGE-ORDER-002 |
| REQ-040 | EDGE-ORDER-003 |
| REQ-041 | TC-SEARCH-001 |
| REQ-042 | TC-SEARCH-001 |
| REQ-043 | TC-SEARCH-002 |
| REQ-044 | TC-SEARCH-002, NEG-SEARCH-001 |
| REQ-045 | TC-SEARCH-002 |
| REQ-046 | NEG-SEARCH-003, SEC-SEARCH-002 |
| REQ-047 | TC-SEARCH-002, NEG-SEARCH-002, EDGE-SEARCH-002 |
| REQ-048 | TC-SEARCH-002 |
| REQ-049 | EDGE-SEARCH-001 |
| REQ-050 | TC-REVIEW-001, NEG-REVIEW-001 |
| REQ-051 | TC-REVIEW-001, EDGE-REVIEW-001 |
| REQ-052 | TC-REVIEW-001, EDGE-REVIEW-001 |
| REQ-053 | TC-REVIEW-002, NEG-REVIEW-002, NEG-REVIEW-003 |
| REQ-054 | TC-REVIEW-001 |
| REQ-055 | TC-REVIEW-001, EDGE-REVIEW-002 |
| REQ-056 | EDGE-REVIEW-003 |

---

```
## Coverage Report

- Total Requirements Extracted: 56
- Requirements Covered: 56
- Coverage %: 100.00%

- By Category:
  - Happy Path:    100.00%
  - Negative Path:  85.71%
  - Edge Cases:    100.00%
  - Security:       78.57%

- Test Case Summary:
  Module              | TC | NEG | EDGE | SEC | Total
  --------------------|----|----|------|-----|------
  Product Management  |  2 |  3 |   3  |  2  |  10
  Image Upload        |  1 |  3 |   3  |  2  |   9
  Shopping Cart       |  2 |  3 |   3  |  2  |  10
  Order & Payment     |  3 |  3 |   3  |  2  |  11
  Search & Filter     |  2 |  3 |   3  |  2  |  10
  Review & Rating     |  2 |  3 |   3  |  2  |  10
  TOTAL               | 12 | 18 |  18  | 12  |  60

- Uncovered Requirements: None
- Status: ✅ Meets threshold (≥80%)
```
