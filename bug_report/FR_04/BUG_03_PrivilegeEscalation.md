# Critical Security Issue: Privilege Escalation

## Description
A regular user can change their account role to "admin" through the Profile update form.

## Steps to Reproduce
1. Login as regular user (e.g., test@eshop.com)
2. Navigate to Profile page
3. Open browser Developer Tools (F12)
4. Open Console tab
5. Make API call to change profile with admin role:
```javascript
fetch('/api/users/me', {
  method: 'PUT',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer ' + localStorage.getItem('token')
  },
  body: JSON.stringify({
    name: 'Test User',
    phone: '1912345678',
    shipping_address: 'Test Address',
    role: 'admin'
  })
})
.then(r => r.json())
.then(console.log)
```
6. After success response, verify role changed by fetching user data again

## Expected Result
System should reject the role change request or ignore the role field.

## Actual Result
✅ Request succeeds  
✅ Response shows: `{"message":"Profile updated"}`  
✅ User role successfully changes from "user" to "admin"  
❌ Regular user now has admin privileges

## Verification
Make GET request to `/api/users/me`:
- **Before**: `"role": "user"`
- **After Attack**: `"role": "admin"`

## Security Impact
🚨 **CRITICAL**

Any user can:
- ✅ Become an admin
- ✅ Access admin features  
- ✅ Potentially compromise the entire application
- ✅ Do this repeatedly with unlimited attempts

## Business Risk
- System is not secure for production
- All user data and admin operations at risk
- Could lead to data breach or unauthorized modifications

## Root Cause (Observable)
The system accepts "role" field in profile updates without proper authorization checks.

## Severity
🔴 **CRITICAL - MUST FIX BEFORE DEPLOYMENT**

---

**Test Case**: TC-22, TC-23  
**Date Found**: 2026-07-02  
**Environment**: http://localhost:3000 (Backend API)  
**Method**: API Testing (Blackbox)  
**Status**: CONFIRMED VULNERABLE
