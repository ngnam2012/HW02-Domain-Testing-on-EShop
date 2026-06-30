# FR-04: Quản lý Hồ sơ Cá nhân — Test Cases Report

> **Feature**: FR-04 Profile Management
> **API Under Test**: `PUT /api/users/me`, `GET /api/users/me`
> **Ngày tạo**: 2026-06-30
> **Phương pháp**: Domain Testing + Boundary Value Analysis (BVA)

---

## Pre-conditions chung

- Backend server đang chạy tại `http://localhost:3000`
- Tài khoản test: `test@eshop.com` / `Test1234!` (role: user, id: 2)
- Tài khoản admin: `admin@eshop.com` / `Admin123!` (role: admin, id: 1)
- Token được lấy qua `POST /api/login` và dùng trong header `Authorization: Bearer <token>`

---

## Nhóm 1: Validation `phone` (Số điện thoại) — BVA 3-value

### TC-01: Phone hợp lệ — 10 chữ số, bắt đầu bằng 0

| Mục                 | Nội dung                                                                                                     |
| ------------------- | ------------------------------------------------------------------------------------------------------------ |
| **ID**              | TC-01                                                                                                        |
| **Mô tả**           | Cập nhật SĐT hợp lệ 10 chữ số bắt đầu bằng 0 (ON boundary min)                                               |
| **Pre-conditions**  | Đã đăng nhập với tài khoản test                                                                              |
| **Test Steps**      | 1. Gửi `PUT /api/users/me` với body `{"name":"Test User","phone":"0912345678","shipping_address":"123 ABC"}` |
| **Test Data**       | `phone = "0912345678"` (10 chữ số, bắt đầu `0`)                                                              |
| **Expected Result** | 200 OK, `{"message":"Profile updated"}`. Verify qua `GET /api/users/me`: phone = "0912345678"                |

### TC-02: Phone hợp lệ — 11 chữ số, bắt đầu bằng 0

| Mục                 | Nội dung                                                                                                 |
| ------------------- | -------------------------------------------------------------------------------------------------------- |
| **ID**              | TC-02                                                                                                    |
| **Mô tả**           | Cập nhật SĐT hợp lệ 11 chữ số bắt đầu bằng 0 (ON boundary max)                                           |
| **Pre-conditions**  | Đã đăng nhập với tài khoản test                                                                          |
| **Test Steps**      | 1. Gửi `PUT /api/users/me` với `{"name":"Test User","phone":"09123456789","shipping_address":"123 ABC"}` |
| **Test Data**       | `phone = "09123456789"` (11 chữ số)                                                                      |
| **Expected Result** | 200 OK, phone được cập nhật thành công                                                                   |

### TC-03: Phone không hợp lệ — 9 chữ số (min - 1)

| Mục                  | Nội dung                                                                                               |
| -------------------- | ------------------------------------------------------------------------------------------------------ |
| **ID**               | TC-03                                                                                                  |
| **Mô tả**            | SĐT chỉ 9 chữ số — dưới boundary tối thiểu                                                             |
| **Pre-conditions**   | Đã đăng nhập                                                                                           |
| **Test Steps**       | 1. Gửi `PUT /api/users/me` với `{"name":"Test User","phone":"091234567","shipping_address":"123 ABC"}` |
| **Test Data**        | `phone = "091234567"` (9 chữ số)                                                                       |
| **Expected Result**  | Phải từ chối — SĐT không đủ 10 chữ số                                                                  |
| **Actual (Dự đoán)** | Backend KHÔNG validate → 200 OK (BUG-3)                                                                |

### TC-04: Phone không hợp lệ — 12 chữ số (max + 1)

| Mục                  | Nội dung                                                                                                  |
| -------------------- | --------------------------------------------------------------------------------------------------------- |
| **ID**               | TC-04                                                                                                     |
| **Mô tả**            | SĐT 12 chữ số — trên boundary tối đa                                                                      |
| **Pre-conditions**   | Đã đăng nhập                                                                                              |
| **Test Steps**       | 1. Gửi `PUT /api/users/me` với `{"name":"Test User","phone":"091234567890","shipping_address":"123 ABC"}` |
| **Test Data**        | `phone = "091234567890"` (12 chữ số)                                                                      |
| **Expected Result**  | Phải từ chối                                                                                              |
| **Actual (Dự đoán)** | Backend KHÔNG validate → 200 OK (BUG-3)                                                                   |

### TC-05: Phone không hợp lệ — không bắt đầu bằng 0

| Mục                  | Nội dung                                                                                                |
| -------------------- | ------------------------------------------------------------------------------------------------------- |
| **ID**               | TC-05                                                                                                   |
| **Mô tả**            | SĐT bắt đầu bằng `1` thay vì `0` (OFF boundary ký tự đầu)                                               |
| **Pre-conditions**   | Đã đăng nhập                                                                                            |
| **Test Steps**       | 1. Gửi `PUT /api/users/me` với `{"name":"Test User","phone":"1234567890","shipping_address":"123 ABC"}` |
| **Test Data**        | `phone = "1234567890"` (10 chữ số, bắt đầu `1`)                                                         |
| **Expected Result**  | Phải từ chối — không bắt đầu bằng `0`                                                                   |
| **Actual (Dự đoán)** | Backend chấp nhận (BUG-3). Frontend regex cũng chấp nhận vì `/^[1-9][0-9]{8,9}$/` cho phép (BUG-2)      |

### TC-06: Phone không hợp lệ — chứa chữ cái

| Mục                  | Nội dung                                                                                                |
| -------------------- | ------------------------------------------------------------------------------------------------------- |
| **ID**               | TC-06                                                                                                   |
| **Mô tả**            | SĐT chứa ký tự chữ cái                                                                                  |
| **Pre-conditions**   | Đã đăng nhập                                                                                            |
| **Test Steps**       | 1. Gửi `PUT /api/users/me` với `{"name":"Test User","phone":"09123abcde","shipping_address":"123 ABC"}` |
| **Test Data**        | `phone = "09123abcde"`                                                                                  |
| **Expected Result**  | Phải từ chối                                                                                            |
| **Actual (Dự đoán)** | Backend chấp nhận (BUG-3)                                                                               |

### TC-07: Phone không hợp lệ — 8 chữ số (min - 2)

| Mục                 | Nội dung                                                                                              |
| ------------------- | ----------------------------------------------------------------------------------------------------- |
| **ID**              | TC-07                                                                                                 |
| **Mô tả**           | SĐT chỉ 8 chữ số                                                                                      |
| **Pre-conditions**  | Đã đăng nhập                                                                                          |
| **Test Steps**      | 1. Gửi `PUT /api/users/me` với `{"name":"Test User","phone":"01234567","shipping_address":"123 ABC"}` |
| **Test Data**       | `phone = "01234567"` (8 chữ số)                                                                       |
| **Expected Result** | Phải từ chối                                                                                          |

### TC-08: Phone rỗng

| Mục                 | Nội dung                                                                                      |
| ------------------- | --------------------------------------------------------------------------------------------- |
| **ID**              | TC-08                                                                                         |
| **Mô tả**           | Gửi SĐT rỗng                                                                                  |
| **Pre-conditions**  | Đã đăng nhập                                                                                  |
| **Test Steps**      | 1. Gửi `PUT /api/users/me` với `{"name":"Test User","phone":"","shipping_address":"123 ABC"}` |
| **Test Data**       | `phone = ""`                                                                                  |
| **Expected Result** | Tùy đặc tả — nếu không bắt buộc thì chấp nhận, nếu bắt buộc thì từ chối                       |

### TC-09: Phone có dấu gạch ngang

| Mục                 | Nội dung                                                                                                  |
| ------------------- | --------------------------------------------------------------------------------------------------------- |
| **ID**              | TC-09                                                                                                     |
| **Mô tả**           | SĐT chứa dấu gạch ngang (format phổ biến nhưng không đúng spec)                                           |
| **Pre-conditions**  | Đã đăng nhập                                                                                              |
| **Test Steps**      | 1. Gửi `PUT /api/users/me` với `{"name":"Test User","phone":"091-234-5678","shipping_address":"123 ABC"}` |
| **Test Data**       | `phone = "091-234-5678"`                                                                                  |
| **Expected Result** | Phải từ chối — chỉ chấp nhận chữ số                                                                       |

---

## Nhóm 2: Validation `name` (Họ Tên)

### TC-10: Name hợp lệ — giá trị bình thường

| Mục                 | Nội dung                 |
| ------------------- | ------------------------ |
| **ID**              | TC-10                    |
| **Mô tả**           | Cập nhật tên bình thường |
| **Test Data**       | `name = "Nguyen Van A"`  |
| **Expected Result** | 200 OK                   |

### TC-11: Name hợp lệ — 1 ký tự (ON min boundary)

| Mục                 | Nội dung                             |
| ------------------- | ------------------------------------ |
| **ID**              | TC-11                                |
| **Mô tả**           | Tên chỉ 1 ký tự — boundary tối thiểu |
| **Test Data**       | `name = "A"`                         |
| **Expected Result** | 200 OK                               |

### TC-12: Name không hợp lệ — chuỗi rỗng (OFF min boundary)

| Mục                  | Nội dung                             |
| -------------------- | ------------------------------------ |
| **ID**               | TC-12                                |
| **Mô tả**            | Tên rỗng — dưới boundary tối thiểu   |
| **Test Data**        | `name = ""`                          |
| **Expected Result**  | Phải từ chối                         |
| **Actual (Dự đoán)** | Backend không validate name → 200 OK |

### TC-13: Name — chỉ khoảng trắng

| Mục                 | Nội dung                         |
| ------------------- | -------------------------------- |
| **ID**              | TC-13                            |
| **Mô tả**           | Tên chỉ gồm khoảng trắng         |
| **Test Data**       | `name = "   "`                   |
| **Expected Result** | Nên từ chối (tên không có nghĩa) |

### TC-14: Name — Unicode tiếng Việt

| Mục                 | Nội dung                     |
| ------------------- | ---------------------------- |
| **ID**              | TC-14                        |
| **Mô tả**           | Tên có dấu tiếng Việt        |
| **Test Data**       | `name = "Nguyễn Văn Ả"`      |
| **Expected Result** | 200 OK, lưu và hiển thị đúng |

### TC-15: Name — XSS payload

| Mục                 | Nội dung                                                            |
| ------------------- | ------------------------------------------------------------------- |
| **ID**              | TC-15                                                               |
| **Mô tả**           | Tên chứa script HTML (kiểm tra SEC-04)                              |
| **Test Data**       | `name = "<script>alert('xss')</script>"`                            |
| **Expected Result** | Lưu được nhưng khi hiển thị phải escape HTML, không thực thi script |

### TC-16: Name — null

| Mục                  | Nội dung                |
| -------------------- | ----------------------- |
| **ID**               | TC-16                   |
| **Mô tả**            | Gửi name = null         |
| **Test Data**        | `name = null`           |
| **Expected Result**  | Phải từ chối            |
| **Actual (Dự đoán)** | Backend lưu NULL vào DB |

---

## Nhóm 3: Validation `shipping_address` (Địa chỉ giao hàng)

### TC-17: Address hợp lệ — giá trị bình thường

| Mục                 | Nội dung                                       |
| ------------------- | ---------------------------------------------- |
| **ID**              | TC-17                                          |
| **Mô tả**           | Cập nhật địa chỉ bình thường                   |
| **Test Data**       | `shipping_address = "123 Lê Lợi, Q.1, TP.HCM"` |
| **Expected Result** | 200 OK                                         |

### TC-18: Address rỗng — bỏ trống

| Mục                 | Nội dung                |
| ------------------- | ----------------------- |
| **ID**              | TC-18                   |
| **Mô tả**           | Không nhập địa chỉ      |
| **Test Data**       | `shipping_address = ""` |
| **Expected Result** | 200 OK (không bắt buộc) |

### TC-19: Address — chuỗi rất dài (stress test)

| Mục                 | Nội dung                                        |
| ------------------- | ----------------------------------------------- |
| **ID**              | TC-19                                           |
| **Mô tả**           | Địa chỉ cực dài 10,000 ký tự                    |
| **Test Data**       | `shipping_address = "A" × 10000`                |
| **Expected Result** | Hệ thống không crash, nên giới hạn hoặc cắt bớt |

### TC-20: Address — XSS payload

| Mục                 | Nội dung                                                |
| ------------------- | ------------------------------------------------------- |
| **ID**              | TC-20                                                   |
| **Mô tả**           | Địa chỉ chứa HTML injection (kiểm tra SEC-04)           |
| **Test Data**       | `shipping_address = "<img src=x onerror=alert('xss')>"` |
| **Expected Result** | Lưu được nhưng hiển thị an toàn (escape HTML)           |

---

## Nhóm 4: Security — `role` Escalation

### TC-21: Cập nhật bình thường KHÔNG gửi role

| Mục                 | Nội dung                                                                                                                         |
| ------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| **ID**              | TC-21                                                                                                                            |
| **Mô tả**           | Request bình thường không có field role                                                                                          |
| **Test Steps**      | 1. Đăng nhập user test → lấy token. 2. `PUT /api/users/me` body: `{"name":"Test","phone":"0912345678","shipping_address":"ABC"}` |
| **Expected Result** | 200 OK, role giữ nguyên "user"                                                                                                   |

### TC-22: Gửi `role: "admin"` để leo thang quyền ⭐

| Mục                  | Nội dung                                                                                                                                                                                |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ID**               | TC-22                                                                                                                                                                                   |
| **Mô tả**            | User thường gửi role=admin qua API để thử nâng quyền (SEC-06)                                                                                                                           |
| **Test Steps**       | 1. Đăng nhập user test → lấy token. 2. `PUT /api/users/me` body: `{"name":"Test","phone":"0912345678","shipping_address":"ABC","role":"admin"}`. 3. `GET /api/users/me` → kiểm tra role |
| **Test Data**        | `role = "admin"`                                                                                                                                                                        |
| **Expected Result**  | Role phải giữ nguyên "user" — backend phải bỏ qua field role                                                                                                                            |
| **Actual (Dự đoán)** | ⚠️ **FAIL** — Backend cập nhật role thành "admin" → **Privilege Escalation (BUG-1)**                                                                                                    |

### TC-23: Gửi `role: "superadmin"` — giá trị tùy ý

| Mục                  | Nội dung                                          |
| -------------------- | ------------------------------------------------- |
| **ID**               | TC-23                                             |
| **Mô tả**            | Gửi role với giá trị không tồn tại trong hệ thống |
| **Test Data**        | `role = "superadmin"`                             |
| **Expected Result**  | Phải bỏ qua hoặc từ chối                          |
| **Actual (Dự đoán)** | Backend lưu "superadmin" vào DB                   |

### TC-24: Gửi `role: ""` — chuỗi rỗng

| Mục                  | Nội dung                                        |
| -------------------- | ----------------------------------------------- |
| **ID**               | TC-24                                           |
| **Mô tả**            | Gửi role rỗng                                   |
| **Test Data**        | `role = ""`                                     |
| **Expected Result**  | Role giữ nguyên                                 |
| **Actual (Dự đoán)** | PASS — `if(role)` đánh giá `""` là falsy → skip |

---

## Nhóm 5: Authentication — Authorization Token

### TC-25: Request không có token

| Mục                 | Nội dung                                             |
| ------------------- | ---------------------------------------------------- |
| **ID**              | TC-25                                                |
| **Mô tả**           | Gửi PUT mà không có Authorization header             |
| **Test Steps**      | 1. `PUT /api/users/me` không có header Authorization |
| **Expected Result** | 401 Unauthorized                                     |

### TC-26: Token sai format

| Mục                 | Nội dung                                                        |
| ------------------- | --------------------------------------------------------------- |
| **ID**              | TC-26                                                           |
| **Mô tả**           | Gửi token với format Basic thay vì Bearer                       |
| **Test Steps**      | 1. `PUT /api/users/me` với header `Authorization: Basic abc123` |
| **Expected Result** | 403 Forbidden                                                   |

### TC-27: Token bị tamper

| Mục                 | Nội dung                                                                     |
| ------------------- | ---------------------------------------------------------------------------- |
| **ID**              | TC-27                                                                        |
| **Mô tả**           | Gửi token đã bị sửa đổi chữ ký                                               |
| **Test Steps**      | 1. `PUT /api/users/me` với header `Authorization: Bearer invalid.token.here` |
| **Expected Result** | 403 Forbidden                                                                |

### TC-28: Token hợp lệ với user ID không tồn tại

| Mục                 | Nội dung                                                            |
| ------------------- | ------------------------------------------------------------------- |
| **ID**              | TC-28                                                               |
| **Mô tả**           | JWT ký với id=99999 (user không tồn tại)                            |
| **Test Steps**      | 1. Tạo JWT sign `{id: 99999, role: "user"}`. 2. `PUT /api/users/me` |
| **Expected Result** | Hệ thống xử lý phù hợp (không crash, trả lỗi hoặc update 0 rows)    |

---

## Nhóm 6: GUI/UX Compliance (Frontend Web)

### TC-29: Email field bị disabled

| Mục                 | Nội dung                                                     |
| ------------------- | ------------------------------------------------------------ |
| **ID**              | TC-29                                                        |
| **Mô tả**           | Kiểm tra field Email không cho phép chỉnh sửa trên giao diện |
| **Test Steps**      | 1. Đăng nhập. 2. Vào trang Profile. 3. Kiểm tra field Email  |
| **Expected Result** | Field Email hiển thị giá trị nhưng disabled, không cho nhập  |

### TC-30: Tiêu đề trang dùng `<h1>`

| Mục                  | Nội dung                                                |
| -------------------- | ------------------------------------------------------- |
| **ID**               | TC-30                                                   |
| **Mô tả**            | Trang Profile phải có đúng 1 thẻ `<h1>` (FR-21)         |
| **Test Steps**       | 1. Mở trang Profile. 2. Inspect DOM → đếm số thẻ `<h1>` |
| **Expected Result**  | Có đúng 1 thẻ `<h1>`                                    |
| **Actual (Dự đoán)** | **FAIL** — Profile.jsx dùng `<h2>` (BUG-5)              |

### TC-31: Label trường bắt buộc có dấu `*`

| Mục                  | Nội dung                                                      |
| -------------------- | ------------------------------------------------------------- |
| **ID**               | TC-31                                                         |
| **Mô tả**            | Các trường bắt buộc phải có ký hiệu `*` bên cạnh nhãn (FR-22) |
| **Test Steps**       | 1. Mở trang Profile. 2. Kiểm tra label "Họ Tên"               |
| **Expected Result**  | Label hiển thị "Họ Tên \*"                                    |
| **Actual (Dự đoán)** | **FAIL** — Label chỉ hiển thị "Họ Tên" không có `*` (BUG-4)   |

---

## 📊 Tổng hợp Test Cases

| Nhóm             | Test Case IDs | Số lượng | Expected Bugs    |
| ---------------- | ------------- | -------- | ---------------- |
| Phone (BVA)      | TC-01 → TC-09 | 9        | BUG-2, BUG-3     |
| Name             | TC-10 → TC-16 | 7        | —                |
| Shipping Address | TC-17 → TC-20 | 4        | —                |
| Role Security    | TC-21 → TC-24 | 4        | BUG-1 (Critical) |
| Auth Token       | TC-25 → TC-28 | 4        | —                |
| GUI/UX           | TC-29 → TC-31 | 3        | BUG-4, BUG-5     |
| **TOTAL**        |               | **31**   | **5 bugs**       |

---

## 🐛 Bug Summary

| Bug ID | Severity | Mô tả | Vi phạm |
| ------ | -------- | ----- | ------- |
| BUG-1  |          |       | FR-04   |
| BUG-2  |          |       | FR-04   |
| BUG-3  |          |       | FR-04   |
