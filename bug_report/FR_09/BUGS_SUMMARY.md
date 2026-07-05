# FR-09 Áp dụng Mã giảm giá - Bug Summary

## Overview
Testing of FR-09 (Áp dụng Mã giảm giá / Coupon Application API) identified **4 critical and high severity bugs**. The math logic for percentage calculation is completely broken, and there are multiple boundary and UX issues that severely impact the mobile checkout experience.

---

## Bug List

### 🚨 [BUG #1: Incorrect Boundary Logic for Minimum Order Amount](BUG_01_TotalAmountBoundary.md)
**Severity**: HIGH  
**Type**: Boundary Logic Error  
**Impact**: Business Logic Flaw  

The backend uses `>` instead of `>=` for the minimum order amount check. When a user's cart perfectly matches the minimum threshold (e.g., exactly `300,000₫`), the coupon is incorrectly rejected.

---

### 🔴 [BUG #2: Severely Incorrect Math for Percent Discount Calculation](BUG_02_MathLogicPercent.md)
**Severity**: CRITICAL  
**Type**: Mathematical Error  
**Impact**: Severe Financial Calculation Failure  

The API incorrectly calculates percentage discounts using `total_amount * (1 - discount_value)` instead of `total_amount * (discount_value / 100)`. A 10% discount turns into a negative 900% multiplier, resulting in massive negative discounts and charging the customer millions of extra VND.

---

### 🔴 [BUG #3: Order Final Amount Can Be Negative](BUG_03_NegativeFinalAmount.md)
**Severity**: HIGH  
**Type**: Bounds Checking  
**Impact**: Payment Gateway Failure  

When a fixed discount exceeds the total cart value, the final checkout amount becomes a negative number, which can cause severe errors with payment processing APIs.

---

### 🟠 [BUG #4: Code Format Mishandling (Case & Whitespace)](BUG_04_CodeFormatHandling.md)
**Severity**: MEDIUM  
**Type**: Input Handling / Mobile UX  
**Impact**: User Frustration  

The API does not sanitize inputs (`trim` or `toUpperCase`). Mobile users who auto-space or use lowercase letters will have valid coupons falsely rejected as "Not Found".

---

## Test Evidence

### Test Environment
- Backend: http://localhost:3000
- Test Date: 2026-07-04

### Testing Methods
- ✅ API Testing (NodeJS Script)
- ✅ Boundary Value Analysis (BVA)
- ✅ Equivalence Partitioning / Math Logic

### Test Cases Executed (API)
- Total Cases: 11
- Passed: 3
- Failed: 8

---

## Conclusion
**Overall Status**: ❌ DO NOT DEPLOY

The mathematical calculation bugs render the discount feature entirely unusable and dangerous for production. All reported bugs must be fixed immediately.

---
**Generated**: 2026-07-04  
**Tester**: AI Testing Agent
