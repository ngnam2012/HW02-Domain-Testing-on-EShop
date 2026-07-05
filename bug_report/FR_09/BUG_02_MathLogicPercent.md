# Logic Bug: Severely Incorrect Math for Percent Discount Calculation

## Description
The backend formula for calculating percentage discounts (`percent`) is mathematically incorrect. It results in a massively negative discount amount, which in turn causes the `final_amount` of the order to inflate instead of decreasing.

## Steps to Reproduce
1. Use an active percent coupon (e.g., `SAVE10` which has `discount_value: 10`).
2. Make a request to `POST /api/apply-coupon` with `total_amount: 500000`.
```json
{
  "code": "SAVE10",
  "total_amount": 500000,
  "user_id": 1
}
```
3. Observe the `discount_amount` and `final_amount` in the response.

## Expected Result
- Discount amount should be: `500000 * (10 / 100) = 50000`
- Final amount should be: `500000 - 50000 = 450000`

## Actual Result
The API returns:
- `discount_amount`: `-4500000` (Negative 4.5 million!)
- `final_amount`: `5000000` (5 million!)
This increases the customer's total by 10 times instead of applying a discount.

## Severity
🚨 **CRITICAL**
This is a catastrophic financial calculation bug that completely breaks the payment logic and charges users astronomical amounts.

## Root Cause Analysis
In `server.js` (line 399), the formula is:
`discount_amount = Math.floor(total_amount * (1 - coupon.discount_value));`

If `coupon.discount_value` is `10` (representing 10%), the math evaluates to:
`500,000 * (1 - 10) = 500,000 * -9 = -4,500,000`
Then `final_amount = total_amount - discount_amount` evaluates to:
`500,000 - (-4,500,000) = 5,000,000`.

**Fix:**
It should be:
`discount_amount = Math.floor(total_amount * (coupon.discount_value / 100));`

---
**Test Case**: TC-09 (Toán học - percent)  
**Date Found**: 2026-07-04  
**Environment**: Localhost (Backend API)  
**Method**: API Testing (Domain Testing)  
**Status**: CONFIRMED BUG
