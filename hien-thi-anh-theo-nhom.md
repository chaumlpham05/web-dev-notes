
# 📘 TÀI LIỆU TỔNG HỢP – HIỂN THỊ ẢNH THEO TỪNG NHÓM 4 TẤM (JAVASCRIPT)

---

## 🧩 Bước 1: Chuẩn bị mảng dữ liệu ảnh – `products[]`

```javascript
var products = [
  "images/1.jpg", "images/2.jpg", ..., "images/24.jpg"
];
```

**Giải thích**:
- Đây là **mảng (array)** chứa 24 đường dẫn ảnh.
- Mỗi phần tử là một đường dẫn tới hình sản phẩm.
- Ảnh sẽ được hiển thị dần dần theo từng nhóm 4 ảnh mỗi lần bấm nút **“Xem thêm”**.

---

## 🔢 Bước 2: Biến `currentIndex` theo dõi trạng thái

```javascript
var currentIndex = 0;
```

**Giải thích**:
- Ghi nhớ đã hiển thị bao nhiêu ảnh.
- Mỗi lần bấm nút → tăng thêm 4.
- Dùng để biết phải hiển thị ảnh tiếp theo từ vị trí nào.

---

## 🔗 Bước 3: Lấy phần tử nút HTML “Xem thêm”

```javascript
var btnSeeMore = document.getElementById("btnSeeMore");
```

**Giải thích**:
- Lấy thẻ nút `<button id="btnSeeMore">` trong HTML.
- Gán vào biến để dễ xử lý sau này.

---

## 🖱️ Bước 4: Gắn sự kiện click cho nút “Xem thêm”

```javascript
btnSeeMore.onclick = function() {
  displayMoreProducts();
};
```

**Giải thích**:
- Mỗi lần người dùng click vào nút này → gọi hàm `displayMoreProducts()`.
- Đây là **cửa ngõ** để kích hoạt toàn bộ logic hiển thị ảnh.

---

## 🧠 Bước 5: Hàm xử lý chính `displayMoreProducts()`

```javascript
function displayMoreProducts() {
  var container = document.getElementById("divSearchResult");

  if (currentIndex >= products.length) {
    alert("Đã hiển thị toàn bộ sản phẩm!");
    return;
  }

  let limit = Math.min(currentIndex + 4, products.length);

  for (var i = currentIndex; i < limit; i++) {
    var img = document.createElement("img");
    img.src = products[i];
    img.className = "col-md-3 col-sm-6 col-xs-12 thumbnail";
    container.appendChild(img);
  }

  currentIndex = limit;
}
```

---

## 📌 Giải thích chi tiết trong hàm:

### ✅ 1. Lấy phần tử chứa ảnh
```javascript
var container = document.getElementById("divSearchResult");
```
> Đây là nơi để ảnh được chèn vào giao diện.

---

### ✅ 2. Kiểm tra nếu đã hết ảnh
```javascript
if (currentIndex >= products.length) {
  alert("Đã hiển thị toàn bộ sản phẩm!");
  return;
}
```
> Nếu đã hiển thị hết 24 ảnh → hiện thông báo và dừng lại.

---

### ✅ 3. Tính số lượng ảnh sẽ hiển thị tiếp theo
```javascript
let limit = Math.min(currentIndex + 4, products.length);
```
> Tối đa 4 ảnh/lần. Nếu chỉ còn 2 ảnh thì chỉ hiển thị 2.

---

### ✅ 4. Vòng lặp hiển thị từng ảnh
```javascript
for (var i = currentIndex; i < limit; i++) {
  ...
}
```
> Lặp từ `currentIndex` đến `limit - 1`.  
> Với mỗi ảnh:
> - Tạo thẻ `<img>`
> - Gán đường dẫn ảnh
> - Thêm class để Bootstrap hiển thị đẹp
> - Chèn ảnh vào HTML

---

### ✅ 5. Cập nhật vị trí sau khi hiển thị
```javascript
currentIndex = limit;
```
> Đảm bảo lần sau bấm sẽ hiển thị tiếp từ hình kế tiếp.

---

## 🔄 LUỒNG DỮ LIỆU TỔNG QUÁT (DATA FLOW)

```
1️⃣ Trang web được tải lần đầu:
   ├─ Biến products[] được tạo, chứa 24 ảnh
   ├─ currentIndex = 0 (chưa hiển thị gì)
   └─ Lấy nút btnSeeMore và gán sự kiện click

2️⃣ Người dùng click vào nút “Xem thêm”:
   └─ Gọi hàm displayMoreProducts()

3️⃣ Trong hàm displayMoreProducts():
   ├─ Tìm phần tử container (divSearchResult)
   ├─ KIỂM TRA:
   │   ├─ Nếu currentIndex >= products.length:
   │   │   └─ ➤ Hiển thị alert “Đã hiển thị toàn bộ sản phẩm!”
   │   │      ➤ Kết thúc hàm (return)
   │   └─ Ngược lại:
   │       ➤ Còn ảnh để hiển thị → Tiếp tục

4️⃣ TÍNH GIỚI HẠN:
   └─ limit = Math.min(currentIndex + 4, products.length)
      ➤ Hiển thị tối đa 4 ảnh hoặc ít hơn nếu sắp hết

5️⃣ LẶP HIỂN THỊ ẢNH:
   ├─ for i = currentIndex → limit - 1:
   │   ├─ Tạo thẻ <img>
   │   ├─ Gán ảnh: img.src = products[i]
   │   ├─ Gán class CSS để ảnh hiển thị đẹp
   │   └─ Gắn ảnh vào container

6️⃣ CẬP NHẬT VỊ TRÍ:
   └─ currentIndex = limit
      ➤ Sẵn sàng cho lần click tiếp theo

7️⃣ Lần sau bấm nút:
   └─ Lặp lại từ bước 2 ➝ Cho đến khi hết ảnh
```
