# Báo cáo Test Cases - FR-09: Áp dụng Mã giảm giá (Mobile View)

**Mô tả:** Bộ test cases dành riêng cho tính năng nhập và áp dụng mã giảm giá (Coupon) tại màn hình Checkout trên Mobile App. 
**Phương pháp:** Domain Testing, Boundary Value Analysis (BVA), và Kiểm thử UI/UX nền tảng Mobile.

---

## 💰 Nhóm 1: Ranh giới Ngưỡng đơn hàng (`total_amount`)
*(Giả định: Mã `SAVE10` yêu cầu đơn hàng tối thiểu `300,000 đ`)*

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data (Input) | Expected Result |
|---|---|---|---|---|---|
| **TC-01** | Tổng tiền dưới ngưỡng 1đ (min - 1) | User có đơn hàng tổng `299,999đ` ở Checkout | 1. Nhập mã giảm giá<br>2. Nhấn "Áp dụng" | `SAVE10` | Thông báo lỗi (Toast/Text): "Đơn hàng chưa đạt giá trị tối thiểu". Tiền không giảm. |
| **TC-02** | Tổng tiền chạm ngưỡng (min) | User có đơn hàng tổng `300,000đ` | Như trên | `SAVE10` | Áp dụng thành công. Tiền giảm 10% (`30,000đ`). `final_amount` = `270,000đ`. |
| **TC-03** | Tổng tiền vượt ngưỡng 1đ (min + 1) | User có đơn hàng tổng `300,001đ` | Như trên | `SAVE10` | Áp dụng thành công. Tiền giảm `30,000đ` (hoặc làm tròn tùy cơ chế). |

---

## ⏳ Nhóm 2: Ranh giới Thời gian Hạn sử dụng (`expired_at`)
*(Giả định: Mã `EXPIRED` hết hạn vào lúc `23:59:59 31/12/2026`)*

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data | Expected Result |
|---|---|---|---|---|---|
| **TC-04** | Áp mã sát lúc hết hạn (min - 1) | Thời gian hệ thống: `23:59:58 31/12/2026` | Nhập mã `EXPIRED` và Áp dụng | Mã hợp lệ khác | Áp dụng thành công. |
| **TC-05** | Áp mã đúng lúc hết hạn (min) | Thời gian hệ thống: `23:59:59 31/12/2026` | Nhập mã `EXPIRED` và Áp dụng | `EXPIRED` | Thông báo lỗi: "Mã giảm giá đã hết hạn sử dụng". (Do đk: hiện tại < expired_at). |
| **TC-06** | Áp mã ngay sau khi hết hạn (min + 1)| Thời gian hệ thống: `00:00:00 01/01/2027` | Nhập mã `EXPIRED` và Áp dụng | `EXPIRED` | Thông báo lỗi: "Mã giảm giá đã hết hạn sử dụng". |

---

## 🎟️ Nhóm 3: Ranh giới Số lượt sử dụng (`usage_count`)
*(Giả định: Mã `VIP100` cho phép max 2 lần / user)*

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data | Expected Result |
|---|---|---|---|---|---|
| **TC-07** | Áp mã khi còn lượt (max - 1) | User chưa dùng hoặc mới dùng 1 lần | Nhập mã và Áp dụng | `VIP100` | Áp dụng thành công. Giảm tiền cho đơn. |
| **TC-08** | Áp mã khi hết lượt (max) | User đã dùng đủ 2 lần trước đó | Nhập mã và Áp dụng | `VIP100` | Thông báo lỗi: "Bạn đã hết lượt sử dụng mã giảm giá này". |

---

## 🧮 Nhóm 4: Toán học & Edge Cases tính tiền (Math Logic)

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data | Expected Result |
|---|---|---|---|---|---|
| **TC-09** | Thuật toán tính phần trăm (`percent`) | Đơn hàng 500k, mã giảm 15% | Nhập mã và Áp dụng | Mã 15% | `discount` = 75k, `final` = 425k. |
| **TC-10** | Mã `fixed` có giá trị > tổng tiền đơn | Đơn hàng 50k, mã fixed giảm 100k, min_order=0 | Nhập mã và Áp dụng | Mã Fixed 100k | `discount` = 50k, `final` = 0. Không được âm tiền. |
| **TC-11** | Làm tròn (Rounding) số lẻ | Đơn 99,999đ, mã giảm 10% | Nhập mã và Áp dụng | Mã 10% | Tiền giảm tính ra 9,999.9đ -> Kiểm tra UI hiển thị làm tròn (vd: 10,000đ) không bị lỗi crash chữ. |

---

## 📱 Nhóm 5: Tương tác UI/UX trên Mobile App & Lỗi phổ biến

| TC ID | Mô tả (Description) | Pre-conditions | Test Steps | Test Data | Expected Result |
|---|---|---|---|---|---|
| **TC-12** | **White-space trim:** Gõ dính dấu cách | Ở màn hình Checkout (Mobile) | 1. Nhập mã có dấu cách cuối<br>2. Nhấn "Áp dụng" | `"SAVE10 "` | Hệ thống tự động trim khoảng trắng và áp dụng thành công. |
| **TC-13** | **Case Sensitivity:** Chữ thường | Như trên | 1. Nhập mã chữ thường | `"save10"` | Tự động chuyển Upper hoặc xử lý API thành công. |
| **TC-14** | Mã không tồn tại / Ngừng kích hoạt | Như trên | 1. Nhập mã sai hoặc bị vô hiệu hóa | `"FAKECODE"` | Thông báo lỗi "Mã không hợp lệ hoặc không tồn tại". |
| **TC-15** | **Auth Check:** Chưa đăng nhập | Guest (không token) mở App Checkout | 1. Cố gắng áp dụng mã | Bất kỳ mã nào | UI điều hướng sang trang Đăng nhập hoặc Toast "Vui lòng đăng nhập". (Theo C4). |
| **TC-16** | **UI Feedback:** Cập nhật Live View | Áp dụng 1 mã thành công | 1. Bấm nút Áp dụng | Mã hợp lệ | 1. Hiển thị Toast/Icon success.<br>2. Giao diện Cập nhật Live dòng "Giảm giá" và "Tổng cộng" ngay lập tức mà không cần F5/Reload trang. |

---

**Tổng kết:** 16 Test Cases được tối ưu hóa cho màn hình Mobile Checkout, bao quát toán học, logic giới hạn và các tình huống gõ phím ảo đặc trưng.
