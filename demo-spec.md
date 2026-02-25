# Product Specification: E-Commerce Product & Order Management

---

## 1. Product Management

### 1.1 Add New Product

Admins can add products by providing:

- **Product Name** (required): 3–200 characters. Must be unique per store.
- **SKU** (required): Alphanumeric and hyphens only, 6–20 characters. Must be unique system-wide.
- **Price** (required): Positive decimal, max 10 digits with 2 decimal places. Currency: VND.
- **Stock Quantity** (required): Non-negative integer.
- **Category** (required): Must reference an existing Category ID.
- **Description** (optional): Max 5,000 characters, plain text only.
- **Images** (optional): Up to 5 images per product. Accepted formats: JPG, PNG, WEBP. Max 5 MB per image.

On successful creation:
- Product saved with status `draft`.
- Admin must publish separately; `draft` products do not appear on the storefront.

### 1.2 Edit Product

- Admins can update any product field.
- Changing SKU requires re-validation of uniqueness.
- If the product has active or pending orders, changing Price or Stock Quantity triggers a confirmation prompt before saving.

### 1.3 Delete Product

- Products with active or pending orders **cannot** be deleted. Return error listing affected Order IDs.
- Eligible products are soft-deleted (status = `deleted`) and hidden from the storefront.

### 1.4 Product Image Upload

- Images are uploaded via a dedicated endpoint, separate from product creation.
- Duplicate images (identical file hash) are rejected: error `ERR-IMG-DUPLICATE`.
- Uploaded images are auto-resized to max 800×800 px while preserving aspect ratio.
- Invalid format or oversized files return `ERR-IMG-INVALID` with a descriptive message.

---

## 2. Shopping Cart

### 2.1 Add to Cart

- Only authenticated users can add products to their cart.
- Cart item fields: Product ID, Quantity (minimum 1).
- Adding the same product again increments quantity instead of creating a duplicate entry.
- Products with `stock = 0` or status ≠ `active` cannot be added: return `ERR-CART-UNAVAILABLE`.

### 2.2 Update Cart Item

- User can change the quantity of any cart item (minimum 1).
- If requested quantity exceeds available stock, return `ERR-CART-STOCK`.
- Setting quantity to 0 removes the item from the cart.

### 2.3 Remove from Cart

- User can remove individual items.
- User can clear the entire cart at once.

---

## 3. Order Placement & Payment

### 3.1 Checkout

- User selects items from the cart and submits checkout.
- Required fields: Shipping Address (Street, City, Province, Postal Code), Payment Method.
- Supported payment methods: `COD`, `VNPAY`, `MOMO`.
- Stock is re-validated at checkout time. If any item is insufficient, return error listing affected products and do not create the order.

### 3.2 Payment Processing

- **COD**: Order is confirmed immediately → status = `confirmed`.
- **VNPAY / MOMO**: User is redirected to the payment gateway.
  - Payment success → status = `confirmed`.
  - Payment failure or timeout (15-minute window) → status = `payment_failed`; reserved stock is restored.

### 3.3 Order Status Flow

Valid transitions:

```
pending     → confirmed    (payment success or COD)
confirmed   → processing   (admin action)
processing  → shipped      (admin action)
shipped     → delivered    (admin action)
pending     → cancelled    (user-initiated only)
confirmed   → cancelled    (admin-initiated only)
payment_failed → [end]     (stock auto-restored, no further transition)
```

Any other transition is rejected with `ERR-ORDER-INVALID-TRANSITION`.

---

## 4. Product Search & Filter

- Users can search products by:
  - **Name**: partial match, case-insensitive.
  - **SKU**: exact match only.
  - **Category**: filter by Category ID.
- Additional filters: Price range (min–max VND), In-stock only (boolean toggle).
- Customers see only `active` products. Admins can see all statuses.
- Pagination: default 20 items per page, max 100 items per page.
- Sort options: Price asc/desc, Name asc/desc, Newest first.
- Empty search results return an empty list `[]`, not an error.

---

## 5. Review & Rating

### 5.1 Submit Review

- Only users with a `delivered` order containing the product may submit a review.
- Review fields:
  - **Rating** (required): Integer 1–5.
  - **Comment** (optional): Max 1,000 characters.
- One review per user per product. Editing allowed within 48 hours of the original submission.

### 5.2 Display Reviews

- Reviews displayed sorted by newest first.
- Average rating calculated and displayed, rounded to 1 decimal place.
- Paginated: 10 reviews per page.
