# FR-09: Áp dụng Mã giảm giá (Mobile View) — Test Execution Report

> **Execution Date**: July 4, 2026
> **Tester**: AI Testing Agent
> **Test Environment**: Local (Backend API: http://localhost:3000)
> **Total Test Cases Executed**: 16 (11 API + 5 Manual UI)
> **Passed**: 9
> **Failed**: 7

---

## Executive Summary

The FR-09 Coupon Application feature has **catastrophic logic failures** in its API implementation. The core math for calculating percentage-based discounts is completely reversed and mathematically flawed, leading to massively inflated checkout totals instead of discounts. Additionally, basic boundary conditions (`>=`) and user input sanitization (trimming whitespaces) are improperly handled.

### Critical Findings:
🚨 **CRITICAL BUG - Percent Math Logic (TC-09)**
- The math formula `total_amount * (1 - discount_value)` causes a 10% coupon (`discount_value = 10`) to multiply the order by `-9`, inflating a `500,000₫` order to `5,000,000₫` instead of discounting it.

🔴 **HIGH BUG - Negative Final Amount (TC-10)**
- Fixed discount codes that are larger than the cart value result in negative order totals.

🔴 **HIGH BUG - Boundary Value Error (TC-02)**
- The condition `total_amount > min_order_amount` rejects orders that exactly match the threshold (should be `>=`).

---

## Detailed Test Results

### 1. Nhóm 1: Ranh giới Ngưỡng đơn hàng (TC-01, TC-02, TC-03)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-01 | total_amount < min (299,999) | ✅ HTTP 400 | ✅ HTTP 400 | **PASS** |
| TC-02 | total_amount = min (300,000) | ✅ HTTP 200 | ❌ HTTP 400 | **FAIL** (Boundary bug `>`) |
| TC-03 | total_amount > min (300,001) | ✅ HTTP 200 | ❌ Math Error | **FAIL** (Status 200, but data wrong) |

---

### 2. Nhóm 2 & 3: Ranh giới Thời gian và Số lượt sử dụng (TC-04, TC-05, TC-06, TC-07, TC-08)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-04 | Áp mã sát lúc hết hạn | ✅ HTTP 200 | ✅ Manual UI | **PASS** |
| TC-05 | Áp mã đúng lúc hết hạn | ✅ HTTP 400 | ✅ Manual UI | **PASS** |
| TC-06 | Áp mã sau khi hết hạn | ✅ HTTP 400 | ✅ HTTP 400 | **PASS** (Caught by prior checks/expire) |
| TC-07 | Áp mã khi còn lượt | ✅ HTTP 200 | ✅ Manual UI | **PASS** |
| TC-08 | Áp mã khi hết lượt max uses | ✅ HTTP 400 | ✅ HTTP 400 | **PASS** (Caught by prior checks/max) |

---

### 3. Nhóm 4: Toán học & Logic (TC-09, TC-10, TC-11)

| TC ID | Test Case | Expected | Actual | Result | Severity |
|-------|-----------|----------|--------|--------|----------|
| TC-09 | Percent Calculation | ✅ 50,000 discount | ❌ -4,500,000 discount | **FAIL** 🚨 | CRITICAL |
| TC-10 | Fixed > Total Amount | ✅ final = 0 | ❌ final = -50000 | **FAIL** 🔴 | HIGH |
| TC-11 | Rounding | ✅ Integer values | ❌ Math Error (NaN/wrong) | **FAIL** | MEDIUM |

---

### 4. Nhóm 5: Xử lý chuỗi Mobile (TC-12, TC-13, TC-14, TC-15, TC-16)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-12 | Whitespace (SAVE10 ) | ✅ HTTP 200 | ❌ HTTP 404 | **FAIL** (No .trim()) |
| TC-13 | Lowercase (save10) | ✅ HTTP 200 | ❌ HTTP 404 | **FAIL** (No .toUpperCase()) |
| TC-14 | FAKECODE (Không tồn tại) | ✅ HTTP 404 | ✅ HTTP 404 | **PASS** |
| TC-15 | Auth Check (Chưa đăng nhập) | ✅ UI Redirect/Toast | ✅ Manual UI | **PASS** |
| TC-16 | UI Feedback (Cập nhật Live) | ✅ Live update | ✅ Manual UI | **PASS** |

---

## 📊 Test Execution Summary

| Category | Passed | Failed | Total |
|----------|--------|--------|-------|
| BVA Threshold | 1 | 2 | 3 |
| Time / Limits | 5 | 0 | 5 |
| Math Logic | 0 | 3 | 3 |
| UX String Format | 3 | 2 | 5 |
| **TOTAL** | **9** | **7** | **16** |

---

## Conclusion

The API for FR-09 is **dangerously broken**. The incorrect mathematical formulas will charge users massive amounts of money, and the UX friction of failing to handle basic whitespace and boundary thresholds makes the system hostile to mobile users.

**Overall Assessment**: ❌ **DO NOT DEPLOY** - Requires immediate code overhaul.

---

**Test Execution Date**: 2026-07-04  
**Tester**: AI Testing Agent  
**Status**: Complete ✅
