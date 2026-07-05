# Functional Bug: Order in 'Shipping' Status Can Be Cancelled via User UI & API

## Description
A regular user can cancel their orders even when the order status has already progressed to "Đang giao" (shipping). This violates the domain rule that orders in transit cannot be cancelled by the user.

## Steps to Reproduce (UI)
1. Login as regular user (e.g., test@eshop.com / Test1234!)
2. Navigate to Profile page and view the Order history.
3. Observe an order with the status "Đang giao" (Shipping).
4. Note that the "Hủy đơn" (Cancel order) button is visible.
5. Click "Hủy đơn".

## Steps to Reproduce (API - TC-14)
1. Authenticate as a regular user and obtain JWT token.
2. Ensure there is an order belonging to the user with `status="shipping"`.
3. Make an API call to cancel the order:
```javascript
fetch('/api/orders/2/cancel', {
  method: 'PUT',
  headers: {
    'Authorization': 'Bearer ' + token
  }
})
```
4. Observe the response.

## Expected Result
- UI: The "Hủy đơn" button should NOT be displayed for orders in "Đang giao" or "Đã giao" states.
- API: The backend should return `HTTP 400 Bad Request` preventing the transition.

## Actual Result
- UI: The "Hủy đơn" button is visible, and clicking it successfully transitions the order to "Đã hủy" (Canceled).
- API: Returns `HTTP 200 OK` and updates the database state to `canceled`.

## Severity
🔴 **HIGH**

---

**Test Case**: TC-14  
**Date Found**: 2026-07-04  
**Environment**: Localhost (Frontend Web & Backend API)  
**Method**: UI Testing & API Testing  
**Status**: CONFIRMED BUG
