
---

## 🧭 **Luồng xử lý chức năng Đăng ký tài khoản người dùng (có OTP xác thực)**

---

### 🔹 **BƯỚC 1: Hiển thị form đăng ký**

* Người dùng truy cập địa chỉ `/register` từ trình duyệt.
* Hệ thống gọi phương thức `showRegister()` trong `AuthController`.
* Hàm này kiểm tra xem người dùng đã đăng nhập chưa (nếu rồi thì redirect).
* Nếu chưa, trả về **giao diện form đăng ký** thông qua `render_view('register')`.

🪷 *Mục tiêu:* Cung cấp UI cho người dùng nhập thông tin đăng ký.

---

| Layer               | Vai trò                                                                                                     |
| ------------------- | ----------------------------------------------------------------------------------------------------------- |
| **View**            | Người dùng truy cập `/register`, giao diện `register.php` được hiển thị.                                    |
| **Controller**      | `AuthController@showRegister()` kiểm tra nếu đã login thì redirect, nếu chưa thì `render_view('register')`. |
| **Không gọi Model** | Vì chưa thao tác với dữ liệu.                                                                               |

✅ **MVC giúp cô lập phần giao diện khỏi logic đăng nhập.**

---

### 🔹 **BƯỚC 2: Gửi thông tin đăng ký**

* Người dùng nhập họ tên, email, số điện thoại, mật khẩu và xác nhận mật khẩu.
* Form được gửi đi qua **AJAX POST** đến `/register`.
* Controller gọi `RestApi::setHeaders()` để thiết lập header phản hồi JSON.
* Sau đó gọi `RestApi::getBody()` để lấy dữ liệu từ `php://input`.

🪷 *Mục tiêu:* Gửi thông tin người dùng lên server để xử lý.

---

| Layer          | Vai trò                                                           |
| -------------- | ----------------------------------------------------------------- |
| **View**       | Gửi AJAX POST `/register` với dữ liệu form.                       |
| **Controller** | `AuthController@register()` gọi `RestApi::getBody()` để lấy data. |
| **Model**      | Chưa dùng ở bước này, chỉ mới lấy dữ liệu đầu vào.                |

✅ **Controller hoạt động như một “người trung gian thông minh”**, chỉ nhận dữ liệu đầu vào.

---

### 🔹 **BƯỚC 3: Kiểm tra dữ liệu hợp lệ**

* Hệ thống kiểm tra:

  * **Email đã tồn tại?**
  * **Số điện thoại đã dùng chưa?**
* Thông qua 2 truy vấn:

  ```sql
  SELECT * FROM accounts WHERE email = ?
  SELECT * FROM accounts WHERE phone = ?
  ```
* Nếu trùng, trả lỗi.
* Nếu không trùng, tiếp tục kiểm tra:

  * Email đúng định dạng?
  * Mật khẩu đủ mạnh và trùng xác nhận?
  * Số điện thoại đúng chuẩn Việt Nam?

🪷 *Mục tiêu:* Ngăn ngừa trùng lặp tài khoản và xác thực đầu vào.

---

| Layer          | Vai trò                                                                          |
| -------------- | -------------------------------------------------------------------------------- |
| **Model**      | `Account::findByEmail()` và `Account::findByPhone()` kiểm tra dữ liệu trùng lặp. |
| **Controller** | Kiểm tra logic hợp lệ (định dạng email, độ dài password…).                       |
| **View**       | Không thay đổi, chỉ đợi phản hồi.                                                |

✅ **Logic nghiệp vụ (C) được tách khỏi tầng dữ liệu (M)** → dễ tái sử dụng cho API khác hoặc mobile app.

---

### 🔹 **BƯỚC 4: Tạo OTP và lưu session tạm thời**

* OTP 6 chữ số được sinh ngẫu nhiên.
* Thời hạn OTP (mặc định 5 phút) được tính từ thời điểm hiện tại.
* Hệ thống lưu toàn bộ thông tin vào `$_SESSION['register_temp']`:

  ```php
  [
    name, email, phone, hash(password),
    otp, otp_expiry, created_at
  ]
  ```

🪷 *Mục tiêu:* Lưu tạm dữ liệu trước khi xác minh để tránh tạo tài khoản giả.

---

| Layer          | Vai trò                                                     |
| -------------- | ----------------------------------------------------------- |
| **Controller** | Tạo mã OTP, lưu thông tin vào `$_SESSION['register_temp']`. |
| **Model**      | Không liên quan.                                            |
| **View**       | Không thay đổi.                                             |

✅ **Không dính dáng đến View và Model** – chỉ xử lý logic phiên làm việc.

---

### 🔹 **BƯỚC 5: Gửi email chứa mã OTP**

* Hệ thống tạo nội dung email OTP (có thể có template HTML).
* Gọi `Mail::send(email, subject, content)`.
* Mail class sử dụng thư viện `PHPMailer` để gửi OTP đến địa chỉ người dùng.
* Gửi thành công → phản hồi JSON:

  ```json
  {
    "message": "Mã OTP đã gửi",
    "metadata": { "redirect_url": "/register/otp" }
  }
  ```

🪷 *Mục tiêu:* Yêu cầu người dùng xác minh danh tính qua email.

---

| Layer          | Vai trò                                              |
| -------------- | ---------------------------------------------------- |
| **Controller** | Tạo nội dung, gọi `Mail::send()` gửi email xác thực. |
| **Mail Core**  | Gửi mail qua SMTP server.                            |
| **View**       | Nhận phản hồi JSON: `redirect: /register/otp`        |

✅ **Controller kết nối các “dịch vụ phụ trợ” như gửi mail, trong khi giữ cho View luôn tách biệt.**

---

### 🔹 **BƯỚC 6: Hiển thị form nhập OTP**

* Người dùng được redirect đến `/register/otp`.
* Giao diện `register_otp.php` được gọi qua `showRegisterOtp()`.
* Kiểm tra lại session `register_temp` còn không và OTP có hết hạn chưa.
* Nếu hợp lệ → render view hiển thị ô nhập 6 số OTP.

🪷 *Mục tiêu:* Cho phép người dùng nhập OTP đã nhận để xác thực tài khoản.

---

| Layer          | Vai trò                                                                  |
| -------------- | ------------------------------------------------------------------------ |
| **View**       | `register_otp.php` hiển thị các ô nhập OTP.                              |
| **Controller** | `showRegisterOtp()` kiểm tra session còn tồn tại không, rồi render View. |
| **Model**      | Không tham gia.                                                          |

✅ Tách biệt rõ: View hiển thị, Controller quyết định hiển thị hay redirect.

---

### 🔹 **BƯỚC 7: Người dùng nhập OTP**

* Người dùng nhập 6 số → gửi qua AJAX đến `/register/otp/verify`.
* Controller lấy dữ liệu từ body bằng `getBody()`.
* So sánh OTP nhập vào với `$_SESSION['register_temp']['otp']`.
* Nếu không trùng → trả lỗi: "Mã OTP không đúng".
* Nếu trùng → tiếp tục tạo tài khoản.

🪷 *Mục tiêu:* Xác thực người dùng thật sự sở hữu email đã đăng ký.

---

| Layer          | Vai trò                                                                |
| -------------- | ---------------------------------------------------------------------- |
| **View**       | Gửi OTP qua AJAX POST `/register/otp/verify`.                          |
| **Controller** | So sánh OTP nhập với session. Nếu sai → báo lỗi, nếu đúng → gọi Model. |
| **Model**      | Bắt đầu dùng lại để lưu dữ liệu đăng ký vào DB.                        |

✅ **Controller kiểm soát “luồng logic”, quyết định khi nào dùng Model.**

---

### 🔹 **BƯỚC 8: Tạo tài khoản mới**

* Dữ liệu từ `register_temp` được dùng để gọi `Account::create([...])`.
* Dữ liệu được lưu vào bảng `accounts` trong database.
* Các trường: name, email, phone, password (đã hash), role, verify\_email\_at, rating

🪷 *Mục tiêu:* Tạo tài khoản mới sau khi xác minh danh tính thành công.

---

| Layer          | Vai trò                                                    |
| -------------- | ---------------------------------------------------------- |
| **Model**      | `Account::create([...])` thực hiện `INSERT INTO accounts`. |
| **Controller** | Xoá session tạm, chuẩn bị dữ liệu đăng nhập.               |
| **View**       | Chưa biết gì, chỉ đợi phản hồi.                            |

✅ **Mọi thao tác với cơ sở dữ liệu đều dồn vào Model**, giúp cô lập logic lưu trữ.

---

### 🔹 **BƯỚC 9: Tự động đăng nhập**

* Sau khi tạo tài khoản, hệ thống gọi `Cookies::setAuth(user)`.
* Dữ liệu user được mã hóa thành **JWT token**, lưu trong cookie `session_token`.
* Trả phản hồi JSON:

  ```json
  {
    "message": "Đăng ký thành công",
    "metadata": { "redirect_url": "/" }
  }
  ```

🪷 *Mục tiêu:* Cho trải nghiệm mượt mà, người dùng không cần login lại.

---

| Layer          | Vai trò                                    |
| -------------- | ------------------------------------------ |
| **Controller** | Gọi `Cookies::setAuth()` để lưu JWT token. |
| **Core**       | Gửi cookie về trình duyệt.                 |
| **View**       | Nhận JSON redirect về `/`.                 |

✅ **Controller đóng vai trò “điều phối tổng thể”**, thực hiện các hành động liên quan đến xác thực.

---

### 🔹 **BƯỚC 10: Truy cập hệ thống**

* Trình duyệt nhận được cookie → người dùng đã đăng nhập.
* Trình duyệt chuyển hướng đến `/` (trang chủ).
* Giao diện hệ thống chào đón người dùng mới đăng ký.

🪷 *Mục tiêu:* Kết thúc hành trình đăng ký → đưa người dùng vào hệ thống.

---

| Layer              | Vai trò                                            |
| ------------------ | -------------------------------------------------- |
| **User + Browser** | Trình duyệt gửi cookie, JWT xác minh tự động.      |
| **System**         | Xác định user hợp lệ, hiển thị nội dung dashboard. |

✅ **Authentication qua JWT giữ logic tách biệt khỏi Controller và View.**

---

### 🔸 **TÙY CHỌN: Gửi lại mã OTP**

* Nếu người dùng nhấn “Gửi lại mã”:

  * Gửi POST `/register/otp/resend`
  * Hệ thống tạo OTP mới
  * Ghi đè vào `$_SESSION['register_temp']`
  * Gửi lại email OTP mới

🪷 *Mục tiêu:* Tạo sự thuận tiện nếu người dùng chưa nhận được mã hoặc mã hết hạn.

---

### 🌼 TÙY CHỌN: Gửi lại OTP

| Layer          | Vai trò                                                 |
| -------------- | ------------------------------------------------------- |
| **Controller** | Cập nhật lại `register_temp['otp']`, gửi lại email mới. |
| **Mail Core**  | Gửi mail.                                               |
| **Model**      | Không liên quan.                                        |
| **View**       | Nhận lại JSON thông báo.                                |

✅ **Controller có thể gọi lại bất kỳ dịch vụ phụ nào mà không làm rối Model hoặc View.**

---
