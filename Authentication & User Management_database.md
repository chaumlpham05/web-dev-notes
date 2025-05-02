
---

Trong **phiên bản nền tảng (basic)** của một hệ thống web thông thường, **cả 3 trang: `login.html`, `register.html`, `forgot-password.html` đều chỉ tương tác trực tiếp với *một bảng duy nhất* là `users`** – hoặc có thể là bảng `users` + một bảng phụ trợ (`password_resets`) nếu muốn chia tách cho bảo mật và mở rộng.

3 trang này cùng nằm trong **nghiệp vụ xác thực & quản lý người dùng** (Authentication & User Management).

---

## **1. Trang `register.html` – Đăng ký tài khoản**

### ✅ Nghiệp vụ:

* Người dùng điền thông tin cá nhân → Tạo tài khoản mới → Lưu vào DB.

### ✅ Các trường dữ liệu cần:

| Trường          | Kiểu dữ liệu         | Ghi chú                |
| --------------- | -------------------- | ---------------------- |
| `id`            | INT, AUTO\_INCREMENT | Khóa chính             |
| `username`      | VARCHAR(50)          | Tên đăng nhập duy nhất |
| `email`         | VARCHAR(100)         | Bắt buộc, duy nhất     |
| `password_hash` | VARCHAR(255)         | Lưu mật khẩu đã mã hóa |
| `full_name`     | VARCHAR(100)         | (tuỳ chọn nếu có)      |
| `phone`         | VARCHAR(20)          | (tuỳ chọn)             |
| `created_at`    | DATETIME             | Thời điểm đăng ký      |
| `is_active`     | BOOLEAN              | Xác nhận tài khoản     |

> Nếu có xác thực email, nên có thêm trường: `verification_token`, `email_verified_at`.

---

## **2. Trang `login.html` – Đăng nhập**

### ✅ Nghiệp vụ:

* Người dùng nhập `username/email + password` → kiểm tra có khớp không → cấp quyền truy cập.

### ✅ Trường tương tác:

* `username` hoặc `email`
* `password_hash`
* `is_active` (để chặn tài khoản bị khóa)
* `last_login_at` (để cập nhật lần cuối đăng nhập, nếu muốn)

---

## **3. Trang `forgot-password.html` – Khôi phục mật khẩu**

### ✅ Nghiệp vụ:

* Người dùng nhập email → Hệ thống tạo **token** gửi qua email → cho phép đặt lại mật khẩu.

### ✅ Trường cần thiết:

| Trường                  | Giải thích                           |
| ----------------------- | ------------------------------------ |
| `email`                 | Dùng để xác định tài khoản           |
| `password_reset_token`  | Mã token duy nhất gửi qua email      |
| `password_reset_expiry` | Thời gian hết hạn token              |
| `updated_at`            | Cập nhật thời điểm yêu cầu khôi phục |

> Hoặc tách thành một bảng riêng: `password_resets(email, token, created_at, expires_at)`

---

---

## **Hai cấp độ quản lý**

---

### **1. CẤP ĐỘ CƠ BẢN – Tương tác 1 bảng duy nhất: `users (Master data)`**

| Trang                  | Hành động nghiệp vụ             | Tác động vào bảng `users`                                                              |
| ---------------------- | ------------------------------- | -------------------------------------------------------------------------------------- |
| `register.html`        | Tạo mới 1 dòng                  | `INSERT INTO users (...)`                                                              |
| `login.html`           | Truy vấn và so khớp             | `SELECT * FROM users WHERE username/email = ?`                                         |
| `forgot-password.html` | Cập nhật token đặt lại mật khẩu | `UPDATE users SET password_reset_token = ?, password_reset_expiry = ? WHERE email = ?` |

> Trong trường hợp này, **chỉ dùng một bảng duy nhất: `users`** là đủ để xử lý cả 3 trang.

---

### **2. CẤP ĐỘ CHUYÊN NGHIỆP / MỞ RỘNG – Tách thêm bảng `password_resets (Transaction data)`**

> Khi muốn mở rộng hệ thống ra để **theo dõi các yêu cầu khôi phục mật khẩu**, hoặc tăng tính bảo mật & tách biệt dữ liệu, thì:

* **`login.html` & `register.html`**: vẫn dùng bảng `users`
* **`forgot-password.html`**: dùng thêm bảng **`password_resets`**

#### Cấu trúc bảng `password_resets`:

```sql
CREATE TABLE password_resets (
  id INT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(100) NOT NULL,
  token VARCHAR(255) NOT NULL,
  expires_at DATETIME NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

> Khi user gửi email để khôi phục, mình `INSERT INTO password_resets`, rồi kiểm tra khi đặt lại mật khẩu.

---

Xây dựng **hai bảng chuẩn hóa**, có khả năng mở rộng về sau như xác thực hai bước (2FA), xác minh email, theo dõi đăng nhập, v.v...

---

## **1. Bảng `users` – Quản lý tài khoản người dùng - Master data**

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50) UNIQUE NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  full_name VARCHAR(100),
  phone VARCHAR(20),
  is_active BOOLEAN DEFAULT TRUE,
  email_verified_at DATETIME DEFAULT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

### ✅ Giải thích:

| Cột                        | Ý nghĩa                                        |
| -------------------------- | ---------------------------------------------- |
| `id`                       | Khóa chính                                     |
| `username`                 | Tên đăng nhập                                  |
| `email`                    | Địa chỉ email (duy nhất)                       |
| `password_hash`            | Mật khẩu đã được mã hóa (không lưu plain text) |
| `full_name`, `phone`       | Dữ liệu bổ sung                                |
| `is_active`                | Để khóa/mở tài khoản                           |
| `email_verified_at`        | Để xác thực tài khoản                          |
| `created_at`, `updated_at` | Theo dõi thay đổi dữ liệu                      |

---

## **2. Bảng `password_resets` – Quản lý khôi phục mật khẩu - Transaction data**

```sql
CREATE TABLE password_resets (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT NOT NULL,
  token VARCHAR(255) NOT NULL,
  expires_at DATETIME NOT NULL,
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### ✅ Giải thích:

| Cột          | Ý nghĩa                             |
| ------------ | ----------------------------------- |
| `user_id`    | Khóa ngoại liên kết với `users(id)` |
| `token`      | Mã xác thực gửi qua email           |
| `expires_at` | Thời gian hết hạn của token         |
| `created_at` | Thời điểm tạo yêu cầu khôi phục     |

> **Lợi ích của việc tách riêng bảng này:**

* Có thể lưu nhiều yêu cầu khôi phục khác nhau (log lịch sử)
* Không ảnh hưởng đến dữ liệu người dùng chính
* Dễ thiết kế xác thực đa lớp, tracking, logging

---

## ✅ Tóm tắt quan hệ:

* `users` ↔ 1\:N ↔ `password_resets`
  (Mỗi người dùng có thể có nhiều yêu cầu đặt lại mật khẩu khác nhau)

---
