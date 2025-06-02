
---

## 💳 MÔ TẢ LOGIC XỬ LÝ CHỨC NĂNG THANH TOÁN VNPay (THEO SEQUENCE DIAGRAM)

---

### 🔶 **BƯỚC 1: NGƯỜI DÙNG TRUY CẬP GIAO DIỆN XÁC NHẬN**

* Người dùng hoàn tất bước chọn thú cưng, dịch vụ, lịch hẹn, nhân viên.
* Hệ thống hiển thị trang xác nhận lại toàn bộ thông tin đặt lịch (view `cart/finished.php`):

  * Tên thú cưng, dịch vụ đã chọn, ngày hẹn, nhân viên, tổng tiền...
  * Có nút **"Xác Nhận Đặt Lịch"** và checkbox đồng ý điều khoản.

---

### 🔶 **BƯỚC 2: NGƯỜI DÙNG CLICK "XÁC NHẬN ĐẶT LỊCH"**

* Người dùng tick chọn "Tôi đồng ý điều khoản..." và nhấn nút “Xác Nhận Đặt Lịch”.
* Lúc này, đoạn script trong giao diện sẽ chạy hàm `confirmBooking()` bằng AJAX.

#### 🎯 Kết quả:

* Gửi một request **POST `/cart/confirm-booking`** đến server, không cần reload trang.
* Người dùng thấy loading xoay xoay ("Đang xử lý...").

---

### 🔶 **BƯỚC 3: CONTROLLER NHẬN YÊU CẦU ĐẶT LỊCH**

Trong hàm `CartController@confirmBooking`, hệ thống thực hiện:

1. **Lấy lại thông tin đơn booking** từ database (bảng `bookings`).

2. **Tạo giao dịch VNPay mới**:

   * Tạo `vnp_TxnRef` (mã giao dịch) duy nhất từ `booking_code` + `timestamp`.
   * Tính `vnp_Amount = tổng tiền * 100`.
   * Ghi log vào bảng `vnpay_transactions` với trạng thái `created`.

3. **Tạo URL thanh toán**:

   * Gọi `createVNPayUrl()` để gắn đầy đủ tham số → hash SHA512 → tạo link.
   * Ví dụ: `https://sandbox.vnpayment.vn/paymentv2/vpcpay.html?...`

4. **Cập nhật link URL vừa tạo vào bản ghi giao dịch VNPay**.

5. **Trả JSON response về lại frontend**:

   ```json
   {
     "message": "Đặt lịch thành công!",
     "metadata": {
       "payment_url": "https://sandbox.vnpayment.vn/paymentv2/vpcpay.html?..."
     }
   }
   ```

---

### 🔶 **BƯỚC 4: TRÌNH DUYỆT TỰ ĐỘNG REDIRECT ĐẾN VNPay**

* Nhận được link `payment_url`, JS sẽ redirect người dùng đến trang thanh toán VNPay.

#### 🌐 Tại VNPay:

* Người dùng chọn ngân hàng, nhập OTP hoặc quét QR.
* Sau khi thanh toán thành công hoặc thất bại, VNPay **redirect lại website** theo link `vnp_ReturnUrl` đã cấu hình.

---

### 🔶 **BƯỚC 5: VNPay GỬI KẾT QUẢ VỀ HỆ THỐNG**

* VNPay redirect người dùng về đường dẫn `GET /cart/finished/success?vnp_*`
* Trong URL có rất nhiều tham số: `vnp_ResponseCode`, `vnp_TxnRef`, `vnp_TransactionNo`, `vnp_BankCode`, `vnp_PayDate`, `vnp_SecureHash`,...

---

### 🔶 **BƯỚC 6: CONTROLLER PHÂN TÍCH KẾT QUẢ TRẢ VỀ**

Trong hàm `CartController@vnpayReturn()`:

1. **Lấy các tham số từ `$_GET`**.
2. **Tìm bản ghi giao dịch** trong bảng `vnpay_transactions` theo `vnp_TxnRef`.

#### Nếu giao dịch **thành công** (ResponseCode = `"00"`):

* Cập nhật `vnpay_transaction`:

  * `status = 'success'`
  * `vnp_transaction_no`, `vnp_pay_date`, `bank_code`, `secure_hash`, v.v.

* Cập nhật booking:

  * `payment_status = 'paid'`
  * `status = 'confirmed'`
  * `paid_at = hiện tại`

* Redirect người dùng về: `/booking/success?code=...`

#### Nếu giao dịch **thất bại**:

* Cập nhật `vnpay_transaction.status = 'failed'`
* Cập nhật `booking.payment_status = 'failed'`
* Redirect về `/booking/failed` kèm thông báo lỗi.

---

## ✅ TÓM TẮT NGẮN GỌN QUÁ TRÌNH:

| Giai đoạn       | Diễn giải                                                |
| --------------- | -------------------------------------------------------- |
| 🖥 Giao diện    | Hiển thị thông tin booking, dịch vụ, nhân viên, giá tiền |
| 📦 Người dùng   | Tick đồng ý → Nhấn xác nhận                              |
| 📡 Gửi AJAX     | Gửi request đặt lịch đến server                          |
| 🧠 Controller   | Tạo giao dịch → Tạo URL → Trả lại JSON                   |
| 🚀 JS           | Nhận link → Redirect sang VNPay                          |
| 💸 VNPay        | Thanh toán OTP/QR                                        |
| 🔄 Trả kết quả  | VNPay redirect lại web                                   |
| 🧾 Server xử lý | Cập nhật trạng thái giao dịch + trạng thái booking       |
| ✅ Giao diện     | Hiển thị kết quả thành công/thất bại                     |

---
