# Lưu trữ đối tượng

:::warning
File đính kèm email mặc định được lưu trong KV.
Có thể chuyển nơi lưu trữ sang R2 hoặc bất kỳ dịch vụ nào tương thích S3.
:::


1. Tạo một R2 bucket.
<img src="../../public/images/r2/1.png" class="article-img">

2. Cấu hình tên miền tùy chỉnh cho bucket.
<img src="../../public/images/r2/2.png" class="article-img">

3. Thêm vào Action Secret hoặc <span style='color: red'>Bindings</span> của Worker

| Tên binding Worker  | Action Secret       | Bắt buộc | Mô tả          |
|---------------------|---------------------|:--------:|----------------|
| r2                  | R2_BUCKET_NAME      |    ✅    | Tên R2 bucket  |

4. Mở Cài đặt hệ thống và cấu hình lưu trữ đối tượng.
<img src="../../public/images/r2/3.png" class="article-img">