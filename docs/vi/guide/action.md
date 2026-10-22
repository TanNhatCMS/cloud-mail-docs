# Triển khai bằng GitHub Actions

## Tạo API Token

1. Tạo token bằng một mẫu có sẵn.
   <img src="../../public/images/action/4.png" class="article-img">

2. Cấu hình các quyền cần thiết.
   <img src="../../public/images/action/5.png" class="article-img">

3. Sao chép API token vừa tạo.
   <img src="../../public/images/action/6.png" class="article-img">

## Thiết lập môi trường

1. Fork repo: [https://github.com/maillab/cloud-mail](https://github.com/maillab/cloud-mail)
   <img src="../../public/images/action/8.png" class="article-img">

2. Cấu hình các secret cho GitHub Actions.

| Tên secret            | Bắt buộc | Mô tả                                                  |
|-----------------------|:--------:|--------------------------------------------------------|
| CLOUDFLARE_API_TOKEN  |    ✅     | Cloudflare API token                                   |
| CLOUDFLARE_ACCOUNT_ID |    ✅     | Cloudflare account ID                                  |
| CUSTOM_DOMAIN         |    ✅     | Tên miền tùy chỉnh của Worker, ví dụ: `skymail.ink`    |
| DOMAIN                |    ✅     | Các tên miền email, ví dụ: `["example.com", "example2.com"]` |
| ADMIN                 |    ✅     | Địa chỉ email quản trị viên, ví dụ: `admin@example.com` |
| JWT_SECRET            |    ✅     | Khóa bí mật JWT (chuỗi ngẫu nhiên bất kỳ, không dùng ký tự đặc biệt) |
| NAME                  |    ❌     | Tên dự án Worker (tùy chọn)                            |
| D1_DATABASE_ID        |    ❌     | ID cơ sở dữ liệu D1 (tùy chọn)                          |
| KV_NAMESPACE_ID       |    ❌     | ID KV namespace (tùy chọn)                               |

<img src="../../public/images/action/9.png" class="article-img" />

## Triển khai

1. Chạy workflow GitHub Actions.
   <img src="../../public/images/action/10.png" class="article-img" />

2. Chờ workflow hoàn tất.
   <img src="../../public/images/action/11.png" class="article-img" />

## Cấu hình chuyển tiếp email

1. Xem các bước trong phần triển khai qua Dashboard: [Cấu hình chuyển tiếp email](dashboard.html#cau-hinh-chuyen-tiep-email) 

2. Mở tên miền tùy chỉnh trong trình duyệt và tạo tài khoản admin để đăng nhập.
   <img src="../../public/images/action/13.png" class="article-img" />
