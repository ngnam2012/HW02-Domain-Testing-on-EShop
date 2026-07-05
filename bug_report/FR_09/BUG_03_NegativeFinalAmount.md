# Functional Bug: Order Final Amount Can Be Negative

## Description
When applying a `fixed` type coupon whose discount value is greater than the order's `total_amount` (and `min_order_amount` is 0 or less than `total_amount`), the API calculates the `final_amount` as a negative number.

## Steps to Reproduce
1. In the database, create or use a coupon of type `fixed` with a high `discount_value` (e.g., `BIGFIXED`, discount `100,000`, min order `0`).
2. Make a request to `POST /api/apply-coupon` with a `total_amount` smaller than the discount (e.g., `50000`).
```json
{
  "code": "BIGFIXED",
  "total_amount": 50000,
  "user_id": 1
}
```
3. Check the `final_amount` in the JSON response.

## Expected Result
An order's total cannot be negative. If the discount exceeds the total amount, the `final_amount` should be capped at `0`.

## Actual Result
The API returns a negative `final_amount` (e.g., `-50000`), which could potentially break payment gateways or create logic flaws where the user earns store credit unintentionally.

## Severity
🔴 **HIGH**

## Root Cause Analysis
In `server.js` (line 406 & 425):
`const final_amount = total_amount - discount_amount;`
There is no bounds checking for values below zero.

**Fix:**
It should be:
`const final_amount = Math.max(0, total_amount - discount_amount);`

---
**Test Case**: TC-10 (Toán học - fixed > tổng tiền đơn)  
**Date Found**: 2026-07-04  
**Environment**: Localhost (Backend API)  
**Method**: API Testing (Edge Cases)  
**Status**: CONFIRMED BUG
