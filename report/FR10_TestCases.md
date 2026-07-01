# Báo cáo Test Cases - FR-10: Trạng thái Đơn hàng (Order State Machine)

**Mô tả:** Bộ test cases này được sinh ra từ quá trình phân tích Domain Testing và Boundary Value Analysis (BVA) cho chức năng Quản lý trạng thái đơn hàng (FR-10).
**Phương pháp:** State Transition Testing + BVA + Phân quyền.

---

## 🟢 Nhóm 1: Chuyển đổi trạng thái Hợp lệ (Admin API 6.2)

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data (Input) | Expected Result |
|---|---|---|---|---|---|
| **TC-01** | Admin chuyển từ `pending` sang `confirmed` | Có tài khoản Admin (Token hợp lệ). Đơn hàng ID=1 đang ở `pending` | 1. Gọi PUT `/api/admin/orders/1/status`<br>2. Gửi body | `status: "confirmed"` | HTTP 200 OK. Trạng thái trong DB chuyển thành `confirmed`. |
| **TC-02** | Admin chuyển từ `pending` sang `canceled` | Như trên, đơn ở `pending` | 1. Gọi PUT `/api/admin/orders/1/status`<br>2. Gửi body | `status: "canceled"` | HTTP 200 OK. Trạng thái chuyển thành `canceled`. |
| **TC-03** | Admin chuyển từ `confirmed` sang `shipping` | Đơn hàng đang ở `confirmed` | 1. Gọi PUT cập nhật trạng thái | `status: "shipping"` | HTTP 200 OK. Trạng thái chuyển thành `shipping`. |
| **TC-04** | Admin chuyển từ `confirmed` sang `canceled` | Đơn hàng đang ở `confirmed` | 1. Gọi PUT cập nhật trạng thái | `status: "canceled"` | HTTP 200 OK. Trạng thái chuyển thành `canceled`. |
| **TC-05** | Admin chuyển từ `shipping` sang `delivered` | Đơn hàng đang ở `shipping` | 1. Gọi PUT cập nhật trạng thái | `status: "delivered"` | HTTP 200 OK. Trạng thái chuyển thành `delivered`. |

---

## 🔴 Nhóm 2: Chuyển đổi trạng thái Không hợp lệ (Admin API 6.2)
*(Kiểm tra các ranh giới vi phạm State Machine)*

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data (Input) | Expected Result |
|---|---|---|---|---|---|
| **TC-06** | Admin bỏ qua bước (`pending` ➔ `shipping`) | Đơn hàng ở `pending` | 1. Gọi PUT cập nhật trạng thái | `status: "shipping"` | HTTP 400 (Bad Request). Lỗi: Chuyển đổi không hợp lệ. |
| **TC-07** | Admin nhảy tới kết thúc (`pending` ➔ `delivered`) | Đơn hàng ở `pending` | 1. Gọi PUT cập nhật trạng thái | `status: "delivered"` | HTTP 400. Lỗi: Chuyển đổi không hợp lệ. |
| **TC-08** | Admin hủy đơn khi đang giao (`shipping` ➔ `canceled`) | Đơn hàng ở `shipping` | 1. Gọi PUT cập nhật trạng thái | `status: "canceled"` | HTTP 400. Lỗi: Không thể hủy đơn đang giao. |
| **TC-09** | Admin hủy đơn đã hoàn thành (`delivered` ➔ `canceled`) | Đơn hàng ở `delivered` (Final State) | 1. Gọi PUT cập nhật trạng thái | `status: "canceled"` | HTTP 400. Lỗi: Đơn hàng đã kết thúc. |
| **TC-10** | Admin tái kích hoạt đơn (`canceled` ➔ `pending`) | Đơn hàng ở `canceled` (Final State) | 1. Gọi PUT cập nhật trạng thái | `status: "pending"` | HTTP 400. Lỗi: Đơn hàng đã kết thúc. |
| **TC-11** | Admin gửi trạng thái ngoài Enum (BVA format) | Đơn hàng ở `pending` | 1. Gọi PUT cập nhật trạng thái | `status: "PENDING"` (in hoa) hoặc `status: ""` | HTTP 400 (Validation Error). |

---

## 🔵 Nhóm 3: User Hủy Đơn (API 4.6) - Ranh giới hợp lệ & không hợp lệ

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data | Expected Result |
|---|---|---|---|---|---|
| **TC-12** | User hủy đơn ở `pending` | User có token. Order ID=2 thuộc sở hữu User, đang ở `pending` | 1. Gọi PUT `/api/orders/2/cancel` | N/A | HTTP 200 OK. Trạng thái chuyển thành `canceled`. |
| **TC-13** | User hủy đơn ở `confirmed` (Ranh giới sát nút) | Đơn hàng của User ở `confirmed` | 1. Gọi PUT `/api/orders/2/cancel` | N/A | HTTP 200 OK. Trạng thái chuyển thành `canceled`. |
| **TC-14** | User hủy đơn ở `shipping` (Vượt ranh giới) | Đơn hàng của User đã sang `shipping` | 1. Gọi PUT `/api/orders/2/cancel` | N/A | HTTP 400. Lỗi: Đơn hàng đang được giao, không thể tự hủy. |
| **TC-15** | User hủy đơn ở `delivered` | Đơn hàng của User đã `delivered` | 1. Gọi PUT `/api/orders/2/cancel` | N/A | HTTP 400. Lỗi: Đơn hàng đã hoàn tất. |
| **TC-16** | User hủy đơn nhiều lần (`canceled` ➔ cancel) | Đơn hàng của User đã `canceled` | 1. Gọi PUT `/api/orders/2/cancel` | N/A | HTTP 400. Lỗi: Đơn hàng đã bị hủy trước đó. |

---

## 🔐 Nhóm 4: Ranh giới Bảo mật (Security / Access Control)

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data | Expected Result |
|---|---|---|---|---|---|
| **TC-17** | **IDOR:** User A hủy đơn của User B | User A đăng nhập. Order ID=3 thuộc sở hữu của User B. | 1. Gọi PUT `/api/orders/3/cancel` với token của User A | N/A | HTTP 403 (Forbidden) hoặc 404 (Not Found). Không được hủy đơn người khác. |
| **TC-18** | **Privilege Escalation:** User dùng API Admin | User đăng nhập tài khoản thường (role=user). | 1. Gọi PUT `/api/admin/orders/1/status` | `status: "confirmed"` | HTTP 403. Lỗi: Không có quyền Admin (kiểm tra Role token). |
| **TC-19** | **Unauthorized:** Guest hủy đơn | Không có JWT Token. | 1. Gọi PUT `/api/orders/2/cancel` | N/A | HTTP 401 (Unauthorized). |
| **TC-20** | **Unauthorized:** Guest cập nhật status | Không có JWT Token. | 1. Gọi PUT `/api/admin/orders/1/status` | `status: "confirmed"` | HTTP 401 (Unauthorized). |

---

**Tổng kết:** 20 Test Cases bao trùm hoàn toàn các đường dẫn (Transition Paths) hợp lệ/không hợp lệ và bảo vệ phân quyền cho State Machine.
