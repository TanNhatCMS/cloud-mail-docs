# Triển khai bằng dòng lệnh

## Điều kiện tiên quyết

Nodejs v20.19.6

Một tài khoản Cloudflare (phải đã thêm tên miền vào tài khoản)


**Clone dự án về máy**
``` shell
git clone https://github.com/maillab/cloud-mail
cd cloud-mail/mail-worker
```

**Cài đặt thư viện**
```shell
pnpm i
```

## Cấu hình dự án

mail-worker/wrangler.toml

```toml
[[d1_databases]]
binding = "db"            # Tên binding cơ sở dữ liệu D1 (không được thay đổi)
database_name = ""        # Tên cơ sở dữ liệu D1
database_id = ""          # ID cơ sở dữ liệu D1

[[kv_namespaces]]
binding = "kv"            # Tên binding KV (không được thay đổi)
id = ""                   # ID KV namespace

[[r2_buckets]]
binding = "r2"            # Tên binding R2 bucket (không được thay đổi)
bucket_name = ""          # Tên R2 bucket

[assets]
binding = "assets"        # Tên binding tài sản tĩnh (không được thay đổi)
directory = "./dist"      # Thư mục chứa tài sản frontend đã build (mặc định: dist)

[triggers]
crons = ["0 16 * * *"]    # Tác vụ định kỳ, chạy hằng ngày lúc 00:00 (Asia/Shanghai)

[vars]
orm_log = false
domain = []               # Các tên miền email, hỗ trợ nhiều tên miền, ví dụ: ["example1.com","example2.com"]
admin = ""                # Địa chỉ email quản trị viên, ví dụ: "admin@example.com"
jwt_secret = ""           # Khóa bí mật JWT dùng cho xác thực, điền một chuỗi ngẫu nhiên bất kỳ

```



## Triển khai từ xa

1. Tạo một KV namespace và một cơ sở dữ liệu D1 trong bảng điều khiển Cloudflare.
2. Cấu hình binding cơ sở dữ liệu và các biến môi trường trong mail-worker/wrangler.toml.
3. Chạy lệnh triển khai từ xa:

    ```shell
    pnpm run deploy 
    ```

4. Trong Cloudflare, vào
   Account Home → Tên miền của bạn → Email → Email Routing → Routing Rules → Catch-all Address,
   rồi sửa rule để chuyển tiếp email tới Worker.

5. Mở URL sau trong trình duyệt, nhập `your_jwt_secret` vào biểu mẫu (form) rồi gửi để khởi tạo hoặc cập nhật cơ sở dữ liệu: https://your-worker-domain/api/init