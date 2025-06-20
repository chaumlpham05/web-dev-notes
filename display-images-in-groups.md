
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

---

## 🧾 Full JavaScript Code (with Explanation)

```javascript
// Đây là một mảng (array) trong JavaScript.
// Mảng tên là products
// Bên trong chứa 24 chuỗi (string), mỗi chuỗi là đường dẫn của một ảnh (tên file).
// products[0] là "images/1.jpg"
// products[1] là "images/2.jpg"
// ...
// products[23] là "images/24.jpg"
// 🎯 Ý nghĩa: Đây là danh sách 24 tấm hình cần được hiển thị trên trang web. Mỗi lần bấm nút “Xem thêm”, mình sẽ lấy hình từ trong mảng này ra để hiển thị dần.
var products = [
    "images/1.jpg", "images/2.jpg", "images/3.jpg", "images/4.jpg",
    "images/5.jpg", "images/6.jpg", "images/7.jpg", "images/8.jpg",
    "images/9.jpg", "images/10.jpg", "images/11.jpg", "images/12.jpg",
    "images/13.jpg", "images/14.jpg", "images/15.jpg", "images/16.jpg",
    "images/17.jpg", "images/18.jpg", "images/19.jpg", "images/20.jpg",
    "images/21.jpg", "images/22.jpg", "images/23.jpg", "images/24.jpg"
];

//Handle SeeMore button by Tin Trinh 2020.04.19

// currentIndex là một biến (variable).
// Được gán cho giá trị khởi đầu của nó là 0.
// ➡️ Biến này sẽ ghi nhớ mình đã hiển thị tới hình số mấy rồi trong mảng products.
// Lúc đầu, chưa hiện tấm hình nào → currentIndex = 0.
// Sau khi hiển thị 4 hình → em sẽ tăng currentIndex lên 4.
// Lần sau bấm nút → hiển thị tiếp từ products[4] đến products[7].
// ➡️ Nhờ currentIndex, ta biết chỗ nào trong mảng đã hiện rồi và chỗ nào chưa hiện.
// 🎯 Ý nghĩa: Đây là con trỏ vị trí hiện tại, giúp mình biết hiện hình từ đâu mỗi khi bấm nút "Xem thêm".
var currentIndex = 0; // Vị trí bắt đầu

// Dòng này đang tìm phần tử HTML có id="btnSeeMore" – tức là nút “Xem thêm” trên giao diện.
// Sau đó, gán nó vào biến có tên là btnSeeMore.
// ➡️ Từ giờ trở đi, mỗi khi mình muốn điều khiển cái nút đó (gắn sự kiện, thay nội dung, ẩn/hiện...) thì chỉ cần dùng biến btnSeeMore là đủ.
// 📌 Trong HTML của anh, nút đó là: <button id="btnSeeMore" class="btn btn-lg btn-danger">Xem thêm</button>
// 🎯 Ý nghĩa: Mình đang kết nối giữa JavaScript và HTML, để JavaScript “nắm được tay” cái nút “Xem thêm” – từ đó xử lý hành động khi người dùng bấm vào.
var btnSeeMore = document.getElementById("btnSeeMore");

// Dòng này gán một hàm (function) cho sự kiện .onclick của nút btnSeeMore.
// Nói cách khác: Khi người dùng click vào nút “Xem thêm” → hàm displayMoreProducts() sẽ được gọi.
// 🎯 Ý nghĩa: Dòng này giúp kích hoạt logic hiển thị ảnh mỗi khi mình bấm nút "Xem thêm".
btnSeeMore.onclick = function(){
    displayMoreProducts(); //Display next 4 products
};

// document.onscroll = function(){
//     if (document.offsetHeight + document.scrollTop >= document.scrollHeight) {
//         displayMoreProducts();
//       }
// }

// 📌 Hàm displayMoreProducts() được gọi khi người dùng bấm vào nút “Xem thêm”
// (Vì ở dòng trước đó ta đã gán: btnSeeMore.onclick = function() { displayMoreProducts(); })
// 🎯 Ý nghĩa: Đây là hàm chịu trách nhiệm chính cho việc hiển thị hình ảnh – mỗi lần người dùng bấm nút.
function displayMoreProducts(){ // Đây là cách khai báo một hàm (function) trong JavaScript.

    // document.getElementById("divSearchResult").innerHTML += '<img class="col-md-4 thumbnail" src="images/7.jpg"/>';
    // document.getElementById("divSearchResult").innerHTML += '<img class="col-md-4 thumbnail" src="images/8.jpg"/>';
    // document.getElementById("divSearchResult").innerHTML += '<img class="col-md-4 thumbnail" src="images/9.jpg"/>';
    // document.getElementById("divSearchResult").innerHTML += '<img class="col-md-4 thumbnail" src="images/10.jpg"/>';

    // 🎯 Ý nghĩa: Mình cần lấy phần tử divSearchResult này để đưa ảnh mới vào bên trong nó.
    var container = document.getElementById("divSearchResult"); // Đây là câu lệnh đi tìm phần tử HTML có id="divSearchResult"
                                                                // Rồi gán phần tử đó vào biến container
                                                                // 📌 Trong HTML chỗ đó là: <div id="divSearchResult" class="row"></div>

    // currentIndex: là vị trí hiện tại trong mảng products
    // products.length: là tổng số sản phẩm trong mảng (tức là 24)
    // → Nên khi currentIndex >= 24 nghĩa là đã hiển thị hết toàn bộ hình ảnh.
    // 🎯 Ý nghĩa: Nếu đã hết hình để hiển thị, thì thông báo cho người dùng biết, và ngưng thực hiện phần còn lại của hàm.
    if (currentIndex >= products.length) {          // ✅ Nếu điều kiện đúng (tức là đã hết hình), thì:
        alert("Đã hiển thị toàn bộ sản phẩm!");     // Thông báo cho người dùng
        return;                                     // ➡️ return có nghĩa là kết thúc hàm tại đây, không chạy tiếp nữa, cũng không thêm hình nào nữa.
    }

    // currentIndex + 4: nghĩa là dự kiến sẽ hiển thị thêm 4 sản phẩm tiếp theo.
    // products.length: là tổng số sản phẩm trong mảng (ở đây là 24).
    // Math.min(a, b): là hàm toán học trong JavaScript dùng để lấy giá trị nhỏ hơn giữa a và b.
    // 🎯 Ý nghĩa: Dòng này đảm bảo rằng dù còn ít hơn 4 hình, mình chỉ hiển thị đúng số hình còn lại, không vượt quá số lượng trong mảng.
    // 💡 Nếu không có Math.min, khi currentIndex = 22, nó sẽ cố gắng hiển thị từ 22 → 25, mà mảng chỉ có 0 → 23, sẽ lỗi!
    let limit = Math.min(currentIndex + 4, products.length);
    // ✅ Ví dụ cụ thể:
    // currentIndex     currentIndex + 4        limit = Math.min(...)
    //      0	                4	                    4
    //      4	                8	                    8
    //      20	                24	                    24
    //      22	                26	                    24 ← chỉ còn 2 hình
    //      24	                28	                    24 ← hết hình

    // ✅ 1. Dòng for (...):
    for (var i = currentIndex; i < limit; i++) {    // Đây là vòng lặp for – nó sẽ lặp từ currentIndex đến limit - 1.
                                                    // Mỗi lần lặp, i sẽ đại diện cho vị trí ảnh cần hiển thị trong mảng products.
        // ✅ 2. Tạo phần tử hình ảnh mới:
        // var img: là khai báo một biến mới có tên là img
        // document.createElement("img"): là câu lệnh dùng để tạo ra một thẻ HTML mới – cụ thể là một thẻ <img>
        // ➡️ Nhưng lúc này thẻ <img> đó chưa hiện ra trên trang, nó chỉ mới tồn tại trong bộ nhớ tạm (RAM) của trình duyệt.
        // 🎯 Ý nghĩa: Một thẻ <img> mới được tạo ra trong bộ nhớ → Biến img sẽ nắm giữ thẻ đó
        var img = document.createElement("img");    // Dòng này tạo ra một thẻ <img> mới bằng JavaScript – nó chưa được thêm lên giao diện, chỉ mới nằm trong bộ nhớ.
        
        // ✅ 3. Gán đường dẫn ảnh cho thẻ đó:
        // .src là thuộc tính của thẻ <img> – dùng để chỉ đường dẫn của tấm hình
        // products[i] là cách lấy ra đường dẫn hình ảnh tương ứng từ mảng tương ứng với thứ tự thứ i
        // ➡️ Như vậy, tấm hình mà thẻ <img> này sẽ hiển thị chính là ảnh tương ứng trong mảng products.
        img.src = products[i];                      // Dòng này gắn đường dẫn ảnh tương ứng từ mảng products.
                                                    // Ví dụ: nếu i = 5 thì products[5] = "images/6.jpg" → ảnh số 6.
        
        // ✅ 4. Thêm class để hiển thị đẹp:
        // .className là cách gán class CSS cho thẻ HTML
        // Các class ở đây là của Bootstrap – giúp ảnh hiển thị đẹp theo lưới, responsive:
        //      col-md-3: khi màn hình lớn thì 4 ảnh/1 hàng
        //      col-sm-6: khi màn hình vừa thì 2 ảnh/1 hàng
        //      col-xs-12: khi màn hình nhỏ thì 1 ảnh/1 hàng
        //      thumbnail: thêm viền và hiệu ứng nhẹ đẹp cho ảnh
        // ➡️ Mục tiêu: làm cho ảnh hiển thị đẹp, gọn gàng, đều đặn trên mọi thiết bị.
        img.className = "col-md-3 col-sm-6 col-xs-12 thumbnail";    // Gán các lớp CSS (class) để ảnh hiển thị theo lưới Bootstrap đẹp đẽ.
        
        // ✅ 5. Thêm ảnh vào giao diện:
        // container chính là biến mà mình đã khai báo ở dòng ➜ var container = document.getElementById("divSearchResult");
        // container lúc này đang giữ cái thẻ <div id="divSearchResult">...</div> – nơi chứa các ảnh
        // .appendChild(img) nghĩa là: “Gắn cái thẻ <img> mới được tạo (từ biến img) vào bên trong phần tử container đó.”
        // ➡️ Và chính nhờ vậy, tấm hình mới sẽ xuất hiện lên giao diện, nằm trong khung chứa có id "divSearchResult".
        container.appendChild(img);         // Cuối cùng, ảnh được gắn vào trong divSearchResult – và hiện lên trang.
    }   //📌 Tóm tắt toàn đoạn:
        //      Lặp qua từng hình tiếp theo → tạo thẻ ảnh <img> → gắn đường dẫn + class → chèn vào giao diện để hiện lên.

    // limit là chỉ số cuối cùng vừa được hiển thị xong (ví dụ: 4, 8, 12, 16...)
    // Sau khi đã hiển thị từ currentIndex → limit - 1, thì mình phải cập nhật lại currentIndex = limit để:
    // ➜ Lần sau bấm nút, chương trình biết là bắt đầu từ hình kế tiếp, không bị lặp lại hình cũ.
    // 🎯 Ý nghĩa: Đây là bước ghi nhớ lại vị trí cuối cùng đã hiển thị, để đảm bảo mỗi lần bấm nút là hiển thị phần mới, không lặp.
    currentIndex = limit;
    // 📌 Ví dụ:
    // Trước khi bấm	currentIndex	limit tính ra	Hiển hình nào?	Sau khi bấm (currentIndex = limit)
    //      Lần 1	          0	              4	         images 1 → 4	    4
    //      Lần 2	          4	              8	         images 5 → 8	    8
    //      Lần 3	          8	              12	     images 9 → 12	    12
    //      ...	              ...	          ...	     ...	            ...
}
```

---

## 🧾 JavaScript Code - ES6 syntax (with Explanation)

```javascript
// (ES6 syntax)
// Dùng const products = [...] thay vì var products = [...]
// const: đảm bảo không thay đổi giá trị mảng 
const products = [
    "images/1.jpg", "images/2.jpg", "images/3.jpg", "images/4.jpg",
    "images/5.jpg", "images/6.jpg", "images/7.jpg", "images/8.jpg",
    "images/9.jpg", "images/10.jpg", "images/11.jpg", "images/12.jpg",
    "images/13.jpg", "images/14.jpg", "images/15.jpg", "images/16.jpg",
    "images/17.jpg", "images/18.jpg", "images/19.jpg", "images/20.jpg",
    "images/21.jpg", "images/22.jpg", "images/23.jpg", "images/24.jpg"
];

// ✅ Biến theo dõi vị trí hiện tại (dùng let vì sẽ thay đổi)
// Dùng let currentIndex = 0 thay vì var currentIndex = 0
// let: cho phép thay đổi giá trị an toàn hơn
// ✅ Lợi ích: Tránh lỗi rò rỉ phạm vi biến (scope) và đảm bảo an toàn khi dùng.
let currentIndex = 0;

// ✅ Lấy phần tử nút bằng cú pháp hiện đại
const btnSeeMore = document.getElementById("btnSeeMore");

// ✅ Gán sự kiện click (arrow function cho gọn)
// Dùng addEventListener() thay vì .onclick
//  ✅ Lợi ích:
//              Cho phép gán nhiều sự kiện cùng lúc
//              Cách viết hiện đại hơn
//              Dễ quản lý & bảo trì code
btnSeeMore.addEventListener("click", () => {
  displayMoreProducts();
});

// ✅ Hàm hiển thị ảnh (dùng cú pháp function hiện đại - arrow function)
// Dùng arrow function thay vì function: function displayMoreProducts() → const displayMoreProducts = () => {}
// ✅ Lợi ích:
//              Gọn gàng, rõ ràng
//              Không bị thay đổi this (rất quan trọng khi làm với object hoặc class)
//              Hợp thời, chuẩn ES6
const displayMoreProducts = () => {
  const container = document.getElementById("divSearchResult");

  if (currentIndex >= products.length) {
    alert("Đã hiển thị toàn bộ sản phẩm!");
    return;
  }

  const limit = Math.min(currentIndex + 4, products.length);

  // ✅ Sử dụng DocumentFragment để tối ưu hiệu năng khi thêm nhiều phần tử (nhiều ảnh)
  const fragment = document.createDocumentFragment();

  for (let i = currentIndex; i < limit; i++) {
    const img = document.createElement("img");
    img.src = products[i];
    img.className = "col-md-3 col-sm-6 col-xs-12 thumbnail";

    // Thay vì gán container.appendChild(img) trong mỗi vòng lặp
    // Cách làm mới: Tạo fragment, thêm ảnh vào fragment, rồi gắn 1 lần
    // ✅ Lợi ích:
    //              Chỉ thay đổi DOM 1 lần duy nhất
    //              Giảm giật lag khi có nhiều ảnh
    //              Cải thiện hiệu năng, nhất là với dữ liệu lớn
    fragment.appendChild(img); // Thêm vào fragment thay vì trực tiếp vào DOM
  }

  container.appendChild(fragment); // Gắn toàn bộ ảnh vào DOM chỉ 1 lần

  currentIndex = limit;
};

// 🔄 LUỒNG DỮ LIỆU TỔNG QUÁT (DATA FLOW)
// 1️⃣ Trang web được tải lần đầu:
//    ├─ Biến products[] được tạo, chứa 24 ảnh
//    ├─ currentIndex = 0 (chưa hiển thị gì)
//    └─ Lấy nút btnSeeMore và gán sự kiện click

// 2️⃣ Người dùng click vào nút “Xem thêm”:
//    └─ Gọi hàm displayMoreProducts()

// 3️⃣ Trong hàm displayMoreProducts():
//    ├─ Tìm phần tử container (divSearchResult)
//    ├─ KIỂM TRA:
//    │   ├─ Nếu currentIndex >= products.length:
//    │   │   └─ ➤ Hiển thị alert “Đã hiển thị toàn bộ sản phẩm!”
//    │   │      ➤ Kết thúc hàm (return)
//    │   └─ Ngược lại:
//    │       ➤ Còn ảnh để hiển thị → Tiếp tục

// 4️⃣ TÍNH GIỚI HẠN:
//    └─ limit = Math.min(currentIndex + 4, products.length)
//       ➤ Hiển thị tối đa 4 ảnh hoặc ít hơn nếu sắp hết

// 5️⃣ LẶP HIỂN THỊ ẢNH:
//    ├─ Tạo fragment chứa ảnh mới
//    ├─ for i = currentIndex → limit - 1:
//    │   ├─ Tạo thẻ <img>
//    │   ├─ Gán ảnh: img.src = products[i]
//    │   ├─ Gán class CSS để ảnh hiển thị đẹp
//    │   └─ Gắn ảnh vào fragment
//    └─ Sau vòng lặp: Gắn fragment vào container

// 6️⃣ CẬP NHẬT VỊ TRÍ:
//    └─ currentIndex = limit
//       ➤ Sẵn sàng cho lần click tiếp theo

// 7️⃣ Lần sau bấm nút:
//    └─ Lặp lại từ bước 2 ➝ Cho đến khi hết ảnh
```

---

### ✨ jQuery Version

```jQuery
// ✅ Danh sách ảnh
const products = [
    "images/1.jpg", "images/2.jpg", "images/3.jpg", "images/4.jpg",
    "images/5.jpg", "images/6.jpg", "images/7.jpg", "images/8.jpg",
    "images/9.jpg", "images/10.jpg", "images/11.jpg", "images/12.jpg",
    "images/13.jpg", "images/14.jpg", "images/15.jpg", "images/16.jpg",
    "images/17.jpg", "images/18.jpg", "images/19.jpg", "images/20.jpg",
    "images/21.jpg", "images/22.jpg", "images/23.jpg", "images/24.jpg"
];

// ✅ Biến theo dõi vị trí hiển thị hiện tại
let currentIndex = 0;

// ✅ Gán sự kiện click bằng jQuery hiện đại
$(document).ready(() => {
  $("#btnSeeMore").on("click", () => {
    displayMoreProducts();
  });
});

// ✅ Hàm hiển thị ảnh – phiên bản jQuery
const displayMoreProducts = () => {
  const $container = $("#divSearchResult");

  if (currentIndex >= products.length) {
    alert("Đã hiển thị toàn bộ sản phẩm!");
    return;
  }

  const limit = Math.min(currentIndex + 4, products.length);

  // ✅ Tạo fragment ảo dùng jQuery (tạm thời là 1 mảng các DOM node)
  const $fragment = $(document.createDocumentFragment());

  for (let i = currentIndex; i < limit; i++) {
    const $img = $("<img>", {
      src: products[i],
      class: "col-md-3 col-sm-6 col-xs-12 thumbnail",
    });
    $fragment.append($img);
  }

  // ✅ Gắn toàn bộ ảnh vào giao diện chỉ 1 lần
  $container.append($fragment);

  // ✅ Cập nhật lại vị trí chỉ số hiện tại
  currentIndex = limit;
};

// 🔄 LUỒNG DỮ LIỆU TỔNG QUÁT (DATA FLOW)
// 1️⃣ Trang web được tải lần đầu:
//    ├─ Biến products[] được tạo, chứa 24 ảnh
//    ├─ currentIndex = 0 (chưa hiển thị gì)
//    └─ Gán sự kiện click bằng jQuery

// 2️⃣ Người dùng click vào nút “Xem thêm”:
//    └─ Gọi hàm displayMoreProducts()

// 3️⃣ Trong hàm displayMoreProducts():
//    ├─ Kiểm tra currentIndex >= products.length
//    ├─ Nếu hết ảnh → thông báo + return
//    └─ Ngược lại → hiển thị tiếp ảnh mới

// 4️⃣ Tính chỉ số giới hạn:
//    └─ limit = Math.min(currentIndex + 4, products.length)

// 5️⃣ Duyệt qua từng ảnh:
//    └─ Tạo thẻ img bằng jQuery → thêm vào $fragment

// 6️⃣ Sau vòng lặp:
//    └─ append $fragment vào $container bằng jQuery

// 7️⃣ Cập nhật currentIndex:
//    └─ currentIndex = limit
```
