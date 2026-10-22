# Gửi email
:::warning
Cloudflare hiện chưa hỗ trợ gửi email.
Cổng 25 bị chặn nên phải sử dụng dịch vụ email của bên thứ ba.
:::

1. Đăng ký tại https://resend.com
   , thêm tên miền của bạn và hoàn tất xác thực DNS.
<img src="../../public/images/send/1.png" class="article-img">

2. Tạo API key và sao chép lại.
<img src="../../public/images/send/3.png" class="article-img">

3. Thêm URL callback trạng thái: `https://<worker-custom-domain>/api/webhooks`
<img src="../../public/images/send/2.png" class="article-img">

4. Chọn các tùy chọn phù hợp.
<img src="../../public/images/send/4.png" class="article-img">

5. Mở Cài đặt hệ thống và cấu hình dịch vụ gửi email.
<img src="../../public/images/send/5.png" class="article-img">