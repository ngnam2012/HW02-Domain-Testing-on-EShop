# Báo cáo Test Cases - FR-15: Quản lý Sản phẩm (Product CRUD)

**Mô tả:** Bộ test cases này được sinh ra từ quá trình phân tích BVA 3-value cho chức năng Quản lý Sản phẩm.
**Phương pháp:** BVA (Boundary Value Analysis), Ràng buộc Khóa ngoại, Phân quyền Bảo mật.

---

## 🏷️ Nhóm 1: BVA cho trường `name` (Độ dài: 1 - 255 ký tự)
*Áp dụng cho API POST (Thêm mới) và PUT (Cập nhật)*

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data (Input) | Expected Result |
|---|---|---|---|---|---|
| **TC-01** | `name` đạt giá trị min (1 ký tự) | Quyền Admin | Gọi API `POST /api/products` | `name: "A"` (cùng các trường hợp lệ khác) | HTTP 201 Created. Sản phẩm được tạo thành công. |
| **TC-02** | `name` đạt giá trị max (255 ký tự) | Quyền Admin | Gọi API `POST /api/products` | `name: "A...A"` (255 ký tự) | HTTP 201 Created. Sản phẩm được tạo thành công. |
| **TC-03** | `name` dưới min (Chuỗi rỗng) | Quyền Admin | Gọi API `POST /api/products` | `name: ""` | HTTP 400 Bad Request. Lỗi validation. |
| **TC-04** | `name` vượt max (256 ký tự) | Quyền Admin | Gọi API `POST /api/products` | `name: "A...A"` (256 ký tự) | HTTP 400 Bad Request. Lỗi validation. |
| **TC-05** | Bỏ qua trường `name` | Quyền Admin | Gọi API `POST /api/products` | (Không có key `name` trong body) | HTTP 400 Bad Request. Thiếu trường bắt buộc. |

---

## 💰 Nhóm 2: BVA cho trường `price` (Giá trị: > 0)
*Áp dụng cho API POST (Thêm mới) và PUT (Cập nhật)*

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data (Input) | Expected Result |
|---|---|---|---|---|---|
| **TC-06** | `price` là số dương (min + 1) | Quyền Admin | Gọi API `POST /api/products` | `price: 1` | HTTP 201 Created. |
| **TC-07** | `price` là 0 (boundary limit) | Quyền Admin | Gọi API `POST /api/products` | `price: 0` | HTTP 400. Lỗi validation (Giá phải lớn hơn 0). |
| **TC-08** | `price` là số âm (min - 1) | Quyền Admin | Gọi API `POST /api/products` | `price: -100` | HTTP 400. Lỗi validation. |
| **TC-09** | `price` sai định dạng kiểu dữ liệu | Quyền Admin | Gọi API `POST /api/products` | `price: "100"` (String) hoặc `null` | HTTP 400. Lỗi validation (Type mismatch). |
| **TC-10** | Bỏ qua trường `price` | Quyền Admin | Gọi API `POST /api/products` | (Không có key `price` trong body) | HTTP 400. Thiếu trường bắt buộc. |

---

## 📂 Nhóm 3: Ràng buộc Khóa ngoại (Category ID)

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data (Input) | Expected Result |
|---|---|---|---|---|---|
| **TC-11** | `category_id` tồn tại | Category ID=1 có trong DB | Gọi API `POST /api/products` | `category_id: 1` | HTTP 201 Created. |
| **TC-12** | `category_id` không tồn tại | Category ID=9999 không có | Gọi API `POST /api/products` | `category_id: 9999` | HTTP 400. Lỗi validation (Danh mục không tồn tại). |
| **TC-13** | Bỏ qua trường `category_id` | Quyền Admin | Gọi API `POST /api/products` | (Không có key `category_id`) | HTTP 400. Thiếu trường bắt buộc. |

---

## 🔗 Nhóm 4: Toàn vẹn dữ liệu (Product ID Param) & Tính độc lập
*Áp dụng cho `PUT /api/products/:id` và `DELETE /api/products/:id`*

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data | Expected Result |
|---|---|---|---|---|---|
| **TC-14** | Xóa sản phẩm hợp lệ | Sản phẩm ID=1 có trong DB | 1. Gọi `DELETE /api/products/1` | N/A | HTTP 200 OK. SP bị xóa khỏi DB. |
| **TC-15** | Xóa sản phẩm không tồn tại | SP ID=9999 không có | 1. Gọi `DELETE /api/products/9999` | N/A | HTTP 404 Not Found. |
| **TC-16** | Cập nhật sản phẩm không tồn tại | SP ID=9999 không có | 1. Gọi `PUT /api/products/9999` | (Body dữ liệu hợp lệ) | HTTP 404 Not Found. |
| **TC-17** | **Độc lập dữ liệu**: Cập nhật 1 SP không ảnh hưởng SP khác | Có SP ID=1 và SP ID=2 | 1. Gọi `PUT /api/products/1` để đổi tên.<br>2. Kiểm tra SP ID=2. | Tên mới cho ID=1 | HTTP 200 OK. Tên SP ID=1 thay đổi, tên SP ID=2 giữ nguyên. |

---

## 🔐 Nhóm 5: Phân quyền bảo mật (Security Roles)

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data | Expected Result |
|---|---|---|---|---|---|
| **TC-18** | **Privilege Escalation**: User thường tạo Sản phẩm | Đăng nhập tài khoản User (Role=user) | Gọi API `POST /api/products` | Dữ liệu SP hợp lệ | HTTP 403 Forbidden. User không được phép thao tác. |
| **TC-19** | **Privilege Escalation**: User thường xóa Sản phẩm | Đăng nhập tài khoản User | Gọi API `DELETE /api/products/1` | N/A | HTTP 403 Forbidden. |
| **TC-20** | **Unauthorized**: Guest thêm Sản phẩm | Không có Token | Gọi API `POST /api/products` | Dữ liệu SP hợp lệ | HTTP 401 Unauthorized. |

---

**Tổng kết:** 20 Test Cases bao trùm hoàn toàn các ràng buộc về chuỗi (Length BVA), số (Value BVA), ràng buộc khóa ngoại, tính toàn vẹn dữ liệu độc lập và phân quyền bảo mật cho chức năng Product CRUD.
