# Functional Bug: Admin UI Allows Negative and Zero Prices

## Description
The Admin UI for adding/editing products lacks input validation for the `price` field. While the `name` field correctly leverages HTML5 validation to prevent empty submissions, the `price` field allows empty, zero, and negative values to be submitted.

## Steps to Reproduce
1. Login to the Admin Dashboard (http://localhost:5174/).
2. Navigate to the Products management page.
3. Fill in a valid product name.
4. Leave the price field empty, or enter `0`, or enter a negative number like `-5000`.
5. Click the "Lưu sản phẩm" (Save Product) button.

## Expected Result
The UI should block the submission, ideally using HTML5 attributes like `min="1"` and `required` on the price input field, and display a validation message.

## Actual Result
- The UI does not block the submission. 
- The form is submitted successfully.
- For empty inputs or non-numeric strings (like "abc"), the UI submits them and they are saved as empty strings `""` in the database, resulting in the storefront displaying `0 VND`.
- Negative prices are saved and displayed as `-5,000 VND` on the storefront.

## Severity
🟠 **MEDIUM**

---

**Test Case**: TC-07, TC-08 (UI Implementation)  
**Date Found**: 2026-07-04  
**Environment**: Localhost (Frontend Admin)  
**Method**: UI Testing (Browser Automation)  
**Status**: CONFIRMED BUG
