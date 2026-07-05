# FR-15: Quản lý Sản phẩm (Product CRUD) — Test Execution Report

> **Execution Date**: July 4, 2026
> **Tester**: AI Testing Agent
> **Test Environment**: Local (Backend: http://localhost:3000, Frontend: http://localhost:5174)
> **Total API Test Cases**: 20
> **Passed**: 2
> **Failed**: 18
> **UI Tests Executed**: Add Product Form Validation

---

## Executive Summary

The FR-15 Product Management feature testing revealed a **systematic failure** in both security and data validation. The system lacks fundamental Access Control (allowing unauthenticated users to modify products) and performs absolutely no data validation on the backend.

### Critical Findings:
🚨 **CRITICAL BUG - Unauthenticated API (TC-18, TC-19, TC-20)**
- The endpoints `POST /api/products`, `PUT /api/products/:id`, and `DELETE /api/products/:id` lack the `authenticateToken` middleware.
- Anyone can create, edit, or delete products.

🔴 **HIGH BUG - Missing Backend Validation (TC-03 to TC-13)**
- The backend accepts empty strings for names, negative numbers for prices, and invalid category IDs.

---

## Detailed Test Results

### 1. Nhóm 1: BVA cho trường `name` (TC-01 to TC-05)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-01 | name 1 char | ✅ 201 | ❌ 200 | **FAIL** |
| TC-02 | name 255 char | ✅ 201 | ❌ 200 | **FAIL** |
| TC-03 | empty name "" | ❌ 400 | ❌ 200 | **FAIL** |
| TC-04 | name 256 char | ❌ 400 | ❌ 200 | **FAIL** |
| TC-05 | no name | ❌ 400 | ❌ 200 | **FAIL** |

**Summary**: The backend does not validate the `name` field. It returns HTTP 200 instead of HTTP 201 for successful creations.

---

### 2. Nhóm 2: BVA cho trường `price` (TC-06 to TC-10)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-06 | price 1 | ✅ 201 | ❌ 200 | **FAIL** |
| TC-07 | price 0 | ❌ 400 | ❌ 200 | **FAIL** |
| TC-08 | price -100 | ❌ 400 | ❌ 200 | **FAIL** |
| TC-09 | price string "100"| ❌ 400 | ❌ 200 | **FAIL** |
| TC-10 | no price | ❌ 400 | ❌ 200 | **FAIL** |

**Issue Found in UI**: 
⚠️ **BUG-UI-1: Admin UI Allows Negative Prices**
- The Admin UI allows submission of negative and zero prices. There is no HTML5 `min="1"` attribute on the price input.

---

### 3. Nhóm 3 & 4: Khóa ngoại và Toàn vẹn Dữ liệu (TC-11 to TC-17)

| TC ID | Test Case | Expected | Actual | Result |
|-------|-----------|----------|--------|--------|
| TC-11 | valid category | ✅ 201 | ❌ 200 | **FAIL** |
| TC-12 | category_id 9999 | ❌ 400 | ❌ 200 | **FAIL** |
| TC-13 | no category_id | ❌ 400 | ❌ 200 | **FAIL** |
| TC-14 | delete valid product | ✅ 200 OK | ✅ 200 OK | **PASS** |
| TC-15 | delete invalid product | ❌ 404 | ❌ 200 OK | **FAIL** |
| TC-16 | update invalid product | ❌ 404 | ❌ 200 OK | **FAIL** |
| TC-17 | update independent | ✅ 200 OK | ✅ 200 OK | **PASS** |

**Summary**: The API doesn't check if the `category_id` exists before inserting.

---

### 4. Nhóm 5: Phân quyền Bảo mật (TC-18 to TC-20)

| TC ID | Test Case | Expected | Actual | Result | Severity |
|-------|-----------|----------|--------|--------|----------|
| TC-18 | User adds product | ❌ 403 | ❌ 200 OK | **FAIL** 🚨 | CRITICAL |
| TC-19 | User deletes product | ❌ 403 | ❌ 200 OK | **FAIL** 🚨 | CRITICAL |
| TC-20 | Guest adds product | ❌ 401 | ❌ 200 OK | **FAIL** 🚨 | CRITICAL |

---

## 📊 Test Execution Summary

### By Category:
| Category | Passed | Failed | Total |
|----------|--------|--------|-------|
| Name Validation | 0 | 5 | 5 |
| Price Validation | 0 | 5 | 5 |
| Foreign Key & DB | 2 | 5 | 7 |
| Security | 0 | 3 | 3 |
| **TOTAL** | **2** | **18** | **20** |

### Pass Rate: 10% (2/20)

---

## 🐛 Bugs Found

### CRITICAL (Must Fix):
1. **BUG-SEC-1: Unauthenticated & Unauthorized Product API (TC-18, TC-19, TC-20)**
   - Severity: 🚨 CRITICAL
   - Fix: Add `authenticateToken` middleware to all Product mutation endpoints in `server.js` and verify user has `role === 'admin'`.

### HIGH (Should Fix):
2. **BUG-BE-1: Missing Backend Validation (TC-03 to TC-13)**
   - Severity: 🔴 HIGH
   - Fix: Implement robust validation for `name`, `price`, and `category_id` before processing DB inserts/updates. 

### MEDIUM (Nice to Have):
3. **BUG-UI-1: Lack of Client-side Price Validation**
   - Severity: 🟠 MEDIUM
   - Fix: Add HTML5 `min="1"` and `required` attributes to the Price input in the Admin UI.

---

## Conclusion

The FR-15 Product CRUD module is fundamentally insecure and lacks basic data validation. The system accepts any data from any user, posing a severe risk to application integrity.

**Overall Assessment**: ❌ **DO NOT DEPLOY - CRITICAL REWRITE REQUIRED**

---

**Test Execution Date**: 2026-07-04  
**Tester**: AI Testing Agent  
**Status**: Complete ✅
