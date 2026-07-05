# FR-10: Trạng thái Đơn hàng (Order State Machine) — Test Execution Report

> **Execution Date**: July 4, 2026
> **Tester**: AI Testing Agent
> **Test Environment**: Local (Backend: http://localhost:3000, Frontend: http://localhost:5173, Admin: http://localhost:5174)
> **Total API Test Cases**: 20
> **Passed**: 18
> **Failed**: 2
> **UI Tests**: 2 Scenarios Executed

---

## Executive Summary

The FR-10 Order State Machine testing revealed **1 CRITICAL security vulnerability** (privilege escalation), **1 HIGH functional issue** (user canceling shipping order), and **1 MEDIUM functional/UI issue** (admin delivering canceled orders).

### Critical Findings:
🚨 **CRITICAL BUG - Privilege Escalation (TC-18)**
- Authenticated regular users can use the Admin API (`PUT /api/admin/orders/:id/status`) to change any order's status.
- Lack of role-based access control (RBAC) on the endpoint.

---

## Detailed Test Results

### 1. Nhóm 1: Chuyển đổi trạng thái Hợp lệ (Admin API) (TC-01 to TC-05)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-01 | pending ➔ confirmed | ✅ 200 OK | ✅ 200 OK | **PASS** |
| TC-02 | pending ➔ canceled | ✅ 200 OK | ✅ 200 OK | **PASS** |
| TC-03 | confirmed ➔ shipping | ✅ 200 OK | ✅ 200 OK | **PASS** |
| TC-04 | confirmed ➔ canceled | ✅ 200 OK | ✅ 200 OK | **PASS** |
| TC-05 | shipping ➔ delivered | ✅ 200 OK | ✅ 200 OK | **PASS** |

**Summary**: Admin can transition valid states correctly.

---

### 2. Nhóm 2: Chuyển đổi trạng thái Không hợp lệ (Admin API) (TC-06 to TC-11)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-06 | pending ➔ shipping | ❌ 400 | ✅ 400 | **PASS** |
| TC-07 | pending ➔ delivered | ❌ 400 | ✅ 400 | **PASS** |
| TC-08 | shipping ➔ canceled | ❌ 400 | ✅ 400 | **PASS** |
| TC-09 | delivered ➔ canceled | ❌ 400 | ✅ 400 | **PASS** |
| TC-10 | canceled ➔ pending | ❌ 400 | ✅ 400 | **PASS** |
| TC-11 | Invalid Enum | ❌ 400 | ✅ 400 | **PASS** |

**Issue Found in UI (Edge Case)**: 
⚠️ **BUG-UI-1: Admin Can Mark Canceled Orders as Delivered**
- The UI allows admins to click "Đánh dấu Đã giao" on orders that are already "Đã hủy".
- The backend API accepts this invalid transition without throwing an error.

---

### 3. Nhóm 3: User Hủy Đơn (TC-12 to TC-16)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-12 | Cancel in pending | ✅ 200 OK | ✅ 200 OK | **PASS** |
| TC-13 | Cancel in confirmed | ✅ 200 OK | ✅ 200 OK | **PASS** |
| TC-14 | Cancel in shipping | ❌ 400 | ✅ 200 OK | **FAIL** 🔴 |
| TC-15 | Cancel in delivered | ❌ 400 | ✅ 400 | **PASS** |
| TC-16 | Cancel multiple times | ❌ 400 | ✅ 400 | **PASS** |

**Issue Found:**
🔴 **BUG-BE-1: User Can Cancel Shipping Orders**
- Users can cancel orders that are already being shipped, violating business logic.

---

### 4. Nhóm 4: Ranh giới Bảo mật (Security / Access Control) (TC-17 to TC-20)

| TC ID | Test Case | Expected | Actual | Result | Severity |
|-------|-----------|----------|--------|--------|----------|
| TC-17 | IDOR: Cancel other's order | ❌ 403/404 | ✅ 404 | **PASS** | - |
| TC-18 | Privilege Escalation | ❌ 403 | ✅ 200 OK | **FAIL** 🚨 | CRITICAL |
| TC-19 | Guest cancels order | ❌ 401 | ✅ 401 | **PASS** | - |
| TC-20 | Guest updates status | ❌ 401 | ✅ 401 | **PASS** | - |

**Issue Found:**
🚨 **BUG-SEC-1: Privilege Escalation on Admin API**
- Regular users can access `PUT /api/admin/orders/:id/status`.

---

## 📊 Test Execution Summary

### By Category:
| Category | Passed | Failed | Total |
|----------|--------|--------|-------|
| Admin Valid | 5 | 0 | 5 |
| Admin Invalid | 6 | 0 | 6 |
| User Cancel | 4 | 1 | 5 |
| Security | 3 | 1 | 4 |
| **TOTAL** | **18** | **2** | **20** |

### Pass Rate: 90% (18/20)

---

## 🐛 Bugs Found

### CRITICAL (Must Fix):
1. **BUG-SEC-1: Privilege Escalation on Admin Order API (TC-18)**
   - Severity: 🚨 CRITICAL
   - Fix: Add proper admin-role authorization middleware to all `/api/admin/*` routes.

### HIGH (Should Fix):
2. **BUG-BE-1: User Can Cancel Shipping Orders (TC-14)**
   - Severity: 🔴 HIGH
   - Fix: The API should reject user cancellation if `order.status !== 'pending' && order.status !== 'confirmed'`. Update UI to hide the cancel button for shipping orders.

### MEDIUM (Nice to Have):
3. **BUG-UI-1: Admin Can Mark Canceled Orders as Delivered**
   - Severity: 🟠 MEDIUM
   - Fix: Hide transition buttons for `canceled` and `delivered` orders in the Admin UI. Add backend validation to prevent modifying final states.

---

## Conclusion

The FR-10 State Machine testing reveals a **critical privilege escalation vulnerability** and a **high-severity functional flaw**. The system correctly handles most valid and invalid state transitions, but lacks strict boundary checks on certain edge cases and security validations.

**Overall Assessment**: ⚠️ **READY TO DEPLOY WITH CRITICAL SECURITY FIX REQUIRED**

---

**Test Execution Date**: 2026-07-04  
**Tester**: AI Testing Agent  
**Status**: Complete ✅
