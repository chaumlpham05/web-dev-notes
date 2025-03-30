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
      <li><a href="/index.html">Trang chủ</a></li>
      <li><a href="#">Dịch vụ</a></li>
      ...
    </ul>
  </nav>

  <div class="auth-links">
    <a href="/pages/login.html" class="auth-link">Đăng nhập</a>
    <a href="/pages/register.html" class="auth-link">Đăng ký</a>
  </div>
  ...
</header>
