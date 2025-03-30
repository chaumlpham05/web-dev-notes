# 🌟 Ghi chú: Tự động gán class "active" cho link trong header

## ✅ Mục đích
- Làm nổi bật (highlight) link tương ứng với trang hiện tại.
- Giúp người dùng biết rõ họ đang đứng ở đâu trên website.
- Hỗ trợ cả `href` dạng tương đối (`./`, `../`) lẫn tuyệt đối (`/index.html`).

---

## 🧠 Kiến thức liên quan
- `window.location.pathname`: Trả về đường dẫn hiện tại trên URL (VD: `/pages/login.html`)
- `new URL(href, base)`: Tạo URL tuyệt đối từ một `href`, chuẩn hoá mọi trường hợp.

---

## 🧩 Cấu trúc HTML liên quan
```html
<header>
  ...
  <nav>
    <ul class="nav-list">
      <li><a href="../index.html">Trang chủ</a></li>
      <li><a href="#">Dịch vụ</a></li>
      ...
    </ul>
  </nav>

  <div class="auth-links">
    <a href="./login.html" class="auth-link">Đăng nhập</a>
    <a href="./pages/register.html" class="auth-link">Đăng ký</a>
  </div>
  ...
</header>
```

---

## 🎨 CSS áp dụng cho class active
```css
/* ===== ĐỔI MÀU CHO LINK ĐANG TRONG TRẠNG THÁI "ACTIVE" ===== */

/* 
 * Khi link trong phần "Đăng nhập / Đăng ký" (auth-link) được active
 * hoặc khi người dùng di chuột vào link trong menu (nav-item > a:hover),
 * thì sẽ đổi màu chữ thành màu tím chủ đạo của thương hiệu (#410075)
 */
.auth-link.active,
.nav-item > a:hover {
  color: #410075;
}

/* 
 * Dành cho bất kỳ thẻ <a> nào được gán class "active"
 * => Áp dụng cho cả link Trang chủ, Dịch vụ, Blog, v.v.
 * Class này được thêm bằng JavaScript dựa theo URL trang hiện tại
 */
a.active {
  color: #410075;
}
```

---

## 💻 JavaScript: Gán class active cho link tương ứng
```js
// ========================================================================
// 🌟 ACTIVE-LINK.JS — Tự động gán class "active" cho các link <a> trong <header>
// ------------------------------------------------------------------------
// 💡 Mục đích:
//    - Highlight (làm nổi bật) link <a> tương ứng với trang hiện tại.
//    - Giúp người dùng biết họ đang ở đâu trong website.
// 🎯 Ưu điểm:
//    - Hoạt động đúng với mọi loại href: "/", "./", "../", tuyệt đối, v.v.
//    - Tự động bỏ qua các link không trỏ đến trang thật (href="#")
//    - Dễ mở rộng và bảo trì sau này.
// ========================================================================

// ✅ BƯỚC 1: In ra console xác nhận script đã được load
console.log("🟢 File active-link.js đã chạy!");

// ✅ BƯỚC 2: Lấy đường dẫn hiện tại từ URL đang hiển thị trên trình duyệt
// Ví dụ: "/index.html", "/pages/login.html"
const currentPath = window.location.pathname;
console.log("🔍 Đường dẫn hiện tại:", currentPath);

// ✅ BƯỚC 3: Tìm tất cả các thẻ <a> trong phần <header> có thuộc tính href
const links = document.querySelectorAll("header a[href]");

// ✅ BƯỚC 4: Xóa toàn bộ class "active" trước khi gán lại
links.forEach(link => link.classList.remove("active"));

// ✅ BƯỚC 5: Duyệt từng link để so sánh đường dẫn
for (const link of links) {
  // 🚫 Bỏ qua nếu <a> nằm trong phần logo (thường là <a id="header-logo">)
  if (link.closest("#header-logo")) continue;

  // 📌 Lấy href từ thẻ <a>
  const href = link.getAttribute("href");

  // ❌ Bỏ qua nếu href rỗng hoặc chỉ là "#"
  if (!href || href.trim() === "#") continue;

  // 🔁 CHUẨN HÓA đường dẫn:
  // - Dù href là "../", "./", "/" hay URL tuyệt đối đều được xử lý chính xác
  // - Dùng window.location.href làm base vì nó phản ánh chính xác vị trí hiện tại
  const absoluteURL = new URL(href, window.location.href);
  const linkPath = absoluteURL.pathname;

  // 🐞 In ra console để kiểm tra quá trình so sánh
  console.log(`🔗 So sánh: ${currentPath} ⇨ ${linkPath}`);

  // ✅ Nếu linkPath trùng khớp với currentPath ⇒ Gán class "active"
  if (currentPath === linkPath) {
    link.classList.add("active");
    console.log("✅ Gán active cho link:", link);
    break; // Dừng lại sau khi gán thành công cho 1 link
  }
}
```
---

## 📌 Kinh nghiệm
- **Không nên gán nhiều link cùng lúc** → gây rối.
- **Chỉ gán link đầu tiên tìm thấy khớp với URL hiện tại** để đảm bảo UX rõ ràng.
- Sử dụng đường dẫn tuyệt đối trong HTML để giảm lỗi do base URL sai.

---

## ❤️ Ghi chú của Châu
> “Code không chỉ để chạy – mà còn để **hiểu, giữ lại, và truyền lại**.”  
> Mỗi đoạn ghi chú như vầy là một viên gạch trong hành trình học lập trình của mình.

---
