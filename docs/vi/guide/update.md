# Cập nhật dự án

## Cập nhật khi triển khai bằng Action
Sau khi đồng bộ code, GitHub Actions sẽ tự động cập nhật.

## Cập nhật khi triển khai qua giao diện
1. Sau khi đồng bộ code, Worker sẽ tự động cập nhật.
2. Gắn lại cơ sở dữ liệu `d1` và `kv`.
3. Truy cập `https://<worker-custom-domain>/api/init/<your_jwt_secret>` để cập nhật cơ sở dữ liệu (chỉ áp dụng các thay đổi mới, không ghi đè dữ liệu hiện có).
