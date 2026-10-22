# Triển khai qua Dashboard

## Tạo dự án

1. Fork hoặc clone repo về tài khoản GitHub của bạn: [https://github.com/maillab/cloud-mail](https://github.com/maillab/cloud-mail)

<img src="../../public/images/dashboard/0.png" class="article-img" />

2. Tạo một dự án Worker mới

<img src="../../public/images/en/dashboard/1.png" class="article-img" />

3. Chọn **Import from GitHub**

<img src="../../public/images/en/dashboard/2.png" class="article-img" />

4. Đặt thư mục là `mail-worker` rồi triển khai dự án

<img src="../../public/images/en/dashboard/3.png" class="article-img" />

## Cấu hình biến môi trường

| Tên biến      | Bắt buộc | Mô tả                                                                                     |
| ------------- | :------: | ----------------------------------------------------------------------------------------- |
| domain        |     ✅    | Tên miền email. Hỗ trợ nhiều tên miền (ví dụ: `["example.com", "example2.com"]`)          |
| admin         |     ✅    | Địa chỉ email quản trị viên (ví dụ: `admin@example.com`)                                  |
| jwt_secret    |     ✅    | Khóa bí mật JWT. Dùng một chuỗi ngẫu nhiên bất kỳ; không chứa ký tự đặc biệt              |

<img src="../../public/images/en/dashboard/5.png" class="article-img" />

## Gắn cơ sở dữ liệu

1. Tạo cơ sở dữ liệu KV và D1

<img src="../../public/images/en/dashboard/4-4.png" class="article-img" />

2. Thêm binding. <span style="color: red">Tên biến bắt buộc phải là `kv` và `db`</span>

<img src="../../public/images/en/dashboard/4.png" class="article-img" />

## Cấu hình chuyển tiếp email

<img src="../../public/images/en/dashboard/6.png" class="article-img" />
<img src="../../public/images/en/dashboard/7.png" class="article-img" />
<img src="../../public/images/en/dashboard/8.png" class="article-img" />

## Đăng nhập trang web

1. Mở URL sau trong trình duyệt để khởi tạo cơ sở dữ liệu:

   `https://<your_worker_domain>/api/init/<your_jwt_secret>`

<img src="../../public/images/dashboard/10.png" class="article-img" />

2. Mở tên miền worker trong trình duyệt, <span style="color: red">đăng ký tài khoản quản trị viên</span>, rồi đăng nhập vào trang web

<img src="../../public/images/dashboard/9.png" class="article-img" />
