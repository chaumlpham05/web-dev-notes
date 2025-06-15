
---

# 📘 GHI CHÚ HỌC TẬP

## **Tư duy phân tích Database mẫu trong đề thi – Quản lý cho thuê khách sạn**

---

## 🎯 MỤC TIÊU CỦA BƯỚC PHÂN TÍCH

> Trước khi bắt đầu viết bất kỳ dòng code hay câu truy vấn SQL nào, người lập trình viên **phải hiểu rõ cấu trúc dữ liệu đang xử lý**:
>
> * Có bao nhiêu bảng?
> * Mỗi bảng chứa thông tin gì?
> * Quan hệ giữa các bảng là gì?
> * Dữ kiện nào là khóa chính, khóa ngoại?
>
> Đây chính là **bước "đọc hiểu đề ngầm ẩn"** để lập bản đồ chiến lược cho việc giải đề sau này.

---

## 🧩 CẤU TRÚC DATABASE MẪU (TỪ ĐỀ BÀI)

| Bảng          | Các cột                                                                   | Vai trò                                          |
| ------------- | ------------------------------------------------------------------------- | ------------------------------------------------ |
| **KHACHHANG** | `MAKH` *(PK)*, `TENKH`, `SDT`, `CCCN`                                     | Lưu thông tin khách hàng                         |
| **PHONG**     | `MAPHONG` *(PK)*, `TENPHONG`, `TINHTRANG`, `LOAIPHONG`                    | Danh sách các phòng trong khách sạn              |
| **HOADON**    | `MAHD` *(PK)*, `TENHD`, `MAKH` *(FK)*, `TONGTIEN`                         | Ghi nhận mỗi lần khách thuê phòng                |
| **THUE**      | `MAHD` *(PK, FK)*, `MAPHONG` *(PK, FK)*, `NGAYTHUE`, `NGAYTRA`, `GIATHUE` | Liên kết giữa hóa đơn và phòng (bảng trung gian) |

---

## 🧠 CÁC DỮ KIỆN THEN CHỐT CẦN TRÍCH RA

### ✅ 1. **Xác định Khóa chính (Primary Key – PK)**

→ Dùng để phân biệt từng bản ghi trong mỗi bảng

→ Đây cũng là cột thường được dùng trong `WHERE`, `JOIN`, hoặc để nhận diện duy nhất

| Bảng      | Khóa chính                          |
| --------- | ----------------------------------- |
| KHACHHANG | `MAKH`                              |
| PHONG     | `MAPHONG`                           |
| HOADON    | `MAHD`                              |
| THUE      | `MAHD + MAPHONG` *(khóa chính kép)* |

---

### ✅ 2. **Xác định Khóa ngoại (Foreign Key – FK)**

→ Là dữ kiện quan trọng cho các mối liên kết

→ Dùng trong các truy vấn JOIN hoặc xác định quan hệ bảng

| Bảng   | Khóa ngoại | Tham chiếu đến bảng |
| ------ | ---------- | ------------------- |
| HOADON | `MAKH`     | KHACHHANG           |
| THUE   | `MAHD`     | HOADON              |
| THUE   | `MAPHONG`  | PHONG               |

---

### ✅ 3. **Xác định loại quan hệ giữa các bảng**

| Quan hệ                   | Giải thích                                   | Loại  |
| ------------------------- | -------------------------------------------- | ----- |
| KHACHHANG → HOADON        | 1 khách có thể có nhiều hóa đơn              | 1 – N |
| HOADON → THUE             | 1 hóa đơn thuê nhiều phòng                   | 1 – N |
| PHONG → THUE              | 1 phòng có thể được thuê trong nhiều hóa đơn | 1 – N |
| HOADON ↔ PHONG (qua THUE) | Hóa đơn ↔ Phòng (many to many)               | N – N |

---

## 🌉 TƯ DUY HÓA THÂN: BIẾN DỮ LIỆU THÀNH CÂU CHUYỆN ĐỜI THỰC

| Bảng      | Tương đương thực tế                                    |
| --------- | ------------------------------------------------------ |
| KHACHHANG | Người đến thuê phòng                                   |
| PHONG     | Các phòng khách sạn                                    |
| HOADON    | Ghi nhận mỗi lần khách thanh toán                      |
| THUE      | Danh sách chi tiết các phòng đã thuê trong mỗi hóa đơn |

---

## 🪄 MẸO PHÂN TÍCH CHUYÊN NGHIỆP

### 🔸 Nếu 1 bảng có 2 cột **vừa là FK vừa là PK** → Đó là bảng **liên kết N–N**

Ví dụ: `THUE(MAHD, MAPHONG)` là PK + FK → bảng trung gian

### 🔸 Nếu FK lặp lại trong bảng → quan hệ là **1 – N**

---

## 📌 KẾT LUẬN TƯ DUY NỀN TẢNG

> Trước khi giải bất kỳ câu nào, hãy luôn bắt đầu bằng việc:
>
> * Vẽ sơ đồ bảng & quan hệ (ERD)
> * Ghi nhớ cột nào là PK, cột nào là FK
> * Hiểu được dòng dữ liệu phản ánh điều gì trong thực tế
> * Xác định bảng trung gian nếu có nhiều-nhiều
> * Từ đó đặt ra chiến lược: mình sẽ cần JOIN những bảng nào?

---
