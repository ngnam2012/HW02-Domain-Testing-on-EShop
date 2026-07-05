# FR-10 State Machine Testing - Bug Summary

## Overview
Testing of FR-10 Order State Machine identified **3 bugs** through UI and API testing.

---

## Bug List

### 🔴 [BUG #1: User Can Cancel Shipping Orders](BUG_01_CancelShippingOrder.md)
**Severity**: HIGH  
**Type**: Functional / Business Logic  
**Impact**: Business Operations  

Users can cancel orders that have already entered the "shipping" state via both UI and API.

---

### 🚨 [BUG #2: Privilege Escalation on Admin Order API (CRITICAL)](BUG_02_PrivilegeEscalation_AdminAPI.md)
**Severity**: CRITICAL  
**Type**: Security  
**Impact**: System Security Compromise  

Regular users can change the status of any order by calling the Admin API endpoint.

---

### 🟠 [BUG #3: Admin Can Mark Canceled Orders as Delivered](BUG_03_AdminCanceledToDelivered.md)
**Severity**: MEDIUM  
**Type**: Functional / UI Logic  
**Impact**: Data Integrity  

The Admin UI displays a "Đánh dấu Đã giao" button for canceled orders, and the backend allows this invalid transition.

---

## Test Evidence

### Test Environment
- Backend: http://localhost:3000
- Frontend Web: http://localhost:5173
- Frontend Admin: http://localhost:5174
- Test Date: 2026-07-04

### Testing Methods
- ✅ UI Testing (Browser Subagent)
- ✅ API Testing (NodeJS Script)
- ✅ State Transition Testing
- ✅ Security Testing

### Test Cases Executed
- Total API Cases: 20
- Passed: 18 (90%)
- Failed: 2 (10%)

---

## Pass Rate Analysis

| Category | Passed | Failed | Notes |
|----------|--------|--------|-------|
| Admin Valid Transitions | 5/5 | 0 | All passed |
| Admin Invalid Transitions | 6/6 | 0 | API passed, UI has edge case issue |
| User Cancel Valid | 2/2 | 0 | All passed |
| User Cancel Invalid | 2/3 | 1 | Failed on Shipping state |
| Security & Auth | 3/4 | 1 | **CRITICAL FAILURE** (Privilege Escalation) |
| **TOTAL** | **18/20** | **2** | **90% Pass Rate** |

---

**Overall Status**: ⚠️ NOT READY FOR PRODUCTION (Fix Critical Security Issue First)

---

**Generated**: 2026-07-04  
**Tester**: AI Testing Agent  
**Method**: State Transition & API Testing
