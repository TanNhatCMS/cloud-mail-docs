# Tài liệu API

::: tip
Ngoại trừ Đăng nhập, hãy gửi token lấy từ Đăng nhập trong header `Authorization` (không thêm tiền tố Bearer). Các API của Quản trị viên yêu cầu quyền tương ứng; tài khoản Quản trị viên không bị giới hạn.
:::

## API Đăng nhập

### Đăng nhập

**Mô tả**: Đăng nhập bằng Email và Mật khẩu, trả về một token xác thực

**Endpoint**: `POST /api/login`

#### Tham số của request

| Tham số   | Kiểu   | Mặc định | Bắt buộc | Mô tả                                            |
| --------- | ------ | -------- | -------- | ------------------------------------------------ |
| email     | string |          | Có       | Địa chỉ email đầy đủ, ví dụ `admin@example.com`  |
| password  | string |          | Có       | Mật khẩu email                                   |

#### Ví dụ request

```bash
curl -X POST "https://skymail.ink/api/login" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@example.com",
    "password": "password"
  }'
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  }
}
```

### Thông tin người dùng hiện tại

**Mô tả**: Lấy tài khoản, Vai trò và danh sách quyền của người dùng đang đăng nhập

**Endpoint**: `GET /api/my/loginUserInfo`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Ví dụ request

```bash
curl -X GET "https://skymail.ink/api/my/loginUserInfo" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": {
      "userId": 1,                          // ID người dùng
      "email": "admin@example.com",         // Email người dùng
      "name": "admin",                      // Tên người dùng
      "type": 0,                            // ID vai trò (Quản trị viên là 0)
      "sendCount": 0,                       // Số đã gửi
      "permKeys": ["*"],                    // Danh sách quyền, Quản trị viên là ["*"]
      "account": {
          "accountId": 1,                   // ID địa chỉ
          "email": "admin@example.com",
          "name": "admin"
      },
      "role": {
          "name": "admin",                  // Tên vai trò
          "sendCount": 0,                   // Giới hạn gửi, 0 là Không giới hạn
          "sendType": "count",              // Kiểu giới hạn gửi (`count` tổng, `day` theo ngày)
          "accountCount": 0                 // Hạn mức địa chỉ, 0 là Không giới hạn
      }
  }
}
```

## Người dùng - Email

### Danh sách email

**Mô tả**: Truy vấn email của người dùng đang đăng nhập, sử dụng phân trang bằng con trỏ `emailId`

**Endpoint**: `GET /api/email/list`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số    | Kiểu    | Mặc định | Bắt buộc | Mô tả                                                                                                |
| ---------- | ------- | -------- | -------- | ---------------------------------------------------------------------------------------------------- |
| accountId  | integer |          | Có       | ID địa chỉ                                                                                           |
| type       | integer | 0        | Không    | Loại email (0 Thư đến, 1 Thư đã gửi)                                                                 |
| emailId    | integer |          | Không    | ID email con trỏ, không bắt buộc với lần truy vấn đầu tiên. Khi `timeSort` là 0 thì lấy các email cũ hơn, khi là 1 thì lấy các email mới hơn |
| size       | integer | 10       | Không    | Kích thước trang, tối đa 50                                                                         |
| timeSort   | integer |          | Không    | Sắp xếp theo thời gian (0 mới nhất, 1 cũ nhất)                                                       |
| allReceive | integer |          | Không    | Có truy vấn tất cả địa chỉ của người dùng này không (0 Không, 1 Có). Nếu bỏ trống, sử dụng cài đặt nhận tất cả của chính Địa chỉ này |
| full       | integer | 1        | Không    | Có trả về đầy đủ các trường không (0 tóm tắt, 1 toàn bộ nội dung và File đính kèm)                   |

#### Ví dụ request

```bash
curl -X GET "https://skymail.ink/api/email/list?accountId=1&size=10" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "total": 1,
    "latestEmail": {
      "emailId": 999,
      "accountId": 1,
      "userId": 1
    },
    "list": [
      {
        "emailId": 999,                       // ID email
        "sendEmail": "hello@example.com",     // Email người gửi
        "name": "hello",                      // Tên người gửi
        "subject": "Hello word",              // Tiêu đề
        "toEmail": "admin@example.com",       // Email người nhận
        "accountId": 1,                       // ID địa chỉ
        "type": 0,                            // Loại email (0 Thư đến, 1 Thư đã gửi)
        "status": 0,                          // Trạng thái email (0 Thư đến, 1 Thư đã gửi, 2 Đã chuyển phát, 3 Bị trả lại, 4 Bị khiếu nại, 5 Bị trì hoãn, 6 Đang lưu, 7 Không có người nhận, 8 Thất bại)
        "unread": 0,                          // Chưa đọc (0 chưa đọc, 1 đã đọc)
        "isDel": 0,                           // Đã xóa (0 Bình thường, 1 Đã xóa)
        "isStar": 0,                          // Đã gắn sao (0 Không, 1 Có)
        "content": "<div>Hello word</div>",   // HTML của email, trả về khi full=1
        "text": "Hello word",                 // Văn bản thuần của email
        "createTime": "2099-12-30 23:59:59",  // Thời gian nhận hoặc gửi (UTC)
        "attList": [                          // Danh sách file đính kèm, trả về khi full=1
          {
            "attId": 1,
            "filename": "file.txt",
            "mimeType": "text/plain",
            "size": 1024
          }
        ]
      }
    ]
  }
}
```

### Gửi email

**Mô tả**: Gửi email bằng Địa chỉ của người dùng hiện tại. Tối đa 10 File đính kèm, tối đa 10 ảnh nội tuyến trong nội dung

**Endpoint**: `POST /api/email/send`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số        | Kiểu                 | Mặc định | Bắt buộc | Mô tả                                                                |
| -------------- | -------------------- | -------- | -------- | -------------------------------------------------------------------- |
| accountId      | integer              |          | Có       | ID địa chỉ gửi                                                       |
| receiveEmail   | array &lt;string&gt; |          | Có       | Danh sách email người nhận                                           |
| subject        | string               |          | Có       | Tiêu đề                                                              |
| content        | string               |          | Có       | HTML của email                                                       |
| text           | string               |          | Không    | Văn bản thuần của email                                              |
| name           | string               |          | Không    | Tên người gửi, tự động trích xuất từ tiền tố email nếu bỏ trống      |
| sendType       | string               |          | Không    | Loại gửi (trống là email mới, `reply` Trả lời, `forward` Chuyển tiếp) |
| emailId        | integer              |          | Không    | ID email gốc, bắt buộc khi `sendType` là `reply`                     |
| attachments    | array &lt;object&gt; |          | Không    | Danh sách file đính kèm                                              |
| └─ filename    | string               |          | Có       | Tên file                                                             |
| └─ content     | string               |          | Có       | Nội dung file, Base64                                                |
| └─ contentType | string               |          | Không    | Kiểu MIME của file                                                   |
| └─ size        | integer              |          | Không    | Kích thước file                                                      |

#### Ví dụ request

```bash
curl -X POST "https://skymail.ink/api/email/send" \
  -H "Authorization: YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "accountId": 1,
    "name": "admin",
    "receiveEmail": ["hello@example.com"],
    "subject": "Hello word",
    "content": "<div>Hello word</div>",
    "text": "Hello word",
    "sendType": "",
    "attachments": [
      {
        "filename": "file.txt",
        "contentType": "text/plain",
        "content": "SGVsbG8gd29yZA=="
      }
    ]
  }'
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "emailId": 999,                         // ID email
      "sendEmail": "admin@example.com",       // Email người gửi
      "name": "admin",                        // Tên người gửi
      "subject": "Hello word",                // Tiêu đề
      "content": "<div>Hello word</div>",     // HTML của email
      "text": "Hello word",                   // Văn bản thuần của email
      "type": 1,                              // Loại email (1 Thư đã gửi)
      "status": 1,                            // Trạng thái email (1 Thư đã gửi, 2 Đã chuyển phát)
      "accountId": 1,
      "userId": 1,
      "createTime": "2099-12-30 23:59:59",
      "attList": []
    }
  ]
}
```

### Xóa email

**Mô tả**: Xóa email của người dùng hiện tại. Mặc định đánh dấu là đã xóa; xóa vĩnh viễn khi tính năng Sync Delete của hệ thống được bật

**Endpoint**: `DELETE /api/email/delete`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số   | Kiểu   | Mặc định | Bắt buộc | Mô tả                                            |
| --------- | ------ | -------- | -------- | ------------------------------------------------ |
| emailIds  | string |          | Có       | ID email, nhiều giá trị cách nhau bằng dấu phẩy  |

#### Ví dụ request

```bash
curl -X DELETE "https://skymail.ink/api/email/delete?emailIds=1,2" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```

## Người dùng - Địa chỉ

### Danh sách địa chỉ

**Mô tả**: Truy vấn các địa chỉ email của người dùng đang đăng nhập, phân trang theo thứ tự Ghim

**Endpoint**: `GET /api/account/list`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số   | Kiểu    | Mặc định | Bắt buộc | Mô tả                                                                                                |
| --------- | ------- | -------- | -------- | ---------------------------------------------------------------------------------------------------- |
| size      | integer |          | Không    | Kích thước trang, tối đa 30                                                                         |
| accountId | integer |          | Không    | ID địa chỉ con trỏ, không bắt buộc với lần truy vấn đầu tiên                                         |
| lastSort  | integer |          | Không    | Giá trị sắp xếp con trỏ, không bắt buộc với lần truy vấn đầu tiên. Với trang tiếp theo, hãy truyền `sort` và `accountId` của mục cuối cùng từ trang trước |

#### Ví dụ request

```bash
curl -X GET "https://skymail.ink/api/account/list?size=15" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "accountId": 1,                         // ID địa chỉ
      "email": "admin@example.com",           // Email
      "name": "admin",                        // Tên địa chỉ
      "userId": 1,                            // ID người dùng sở hữu
      "allReceive": 0,                        // Nhận tất cả (0 Không, 1 Có)
      "sort": 0,                              // Giá trị sắp xếp, càng lớn càng được Ghim cao hơn
      "isDel": 0,                             // Đã xóa (0 Bình thường, 1 Đã xóa)
      "createTime": "2099-12-30 23:59:59"     // Thời gian tạo
    }
  ]
}
```

### Thêm địa chỉ email

**Mô tả**: Thêm địa chỉ email cho người dùng hiện tại. Tên miền email phải là một tên miền email đã được cấu hình. Không thể thêm Địa chỉ đã tồn tại hoặc đã bị hủy

**Endpoint**: `POST /api/account/add`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số | Kiểu   | Mặc định | Bắt buộc | Mô tả                                                                |
| ------- | ------ | -------- | -------- | -------------------------------------------------------------------- |
| email   | string |          | Có       | Địa chỉ email đầy đủ, ví dụ `hello@example.com`                      |
| token   | string |          | Không    | token Captcha, bắt buộc khi hệ thống đã bật Xác minh thêm email      |

#### Ví dụ request

```bash
curl -X POST "https://skymail.ink/api/account/add" \
  -H "Authorization: YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "hello@example.com"
  }'
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "accountId": 2,                           // ID địa chỉ
    "email": "hello@example.com",             // Email
    "name": "hello",                          // Tên địa chỉ, tự động trích xuất từ tiền tố email
    "userId": 1,
    "allReceive": 0,
    "sort": 0,
    "isDel": 0,
    "createTime": "2099-12-30 23:59:59",
    "addVerifyOpen": false                    // Có yêu cầu captcha cho lần thêm tiếp theo hay không
  }
}
```

### Xóa địa chỉ

**Mô tả**: Xóa Địa chỉ của người dùng hiện tại. Không thể xóa email chính dùng để Đăng nhập. Mặc định đánh dấu là đã xóa; xóa vĩnh viễn khi tính năng Sync Delete của hệ thống được bật

**Endpoint**: `DELETE /api/account/delete`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số   | Kiểu    | Mặc định | Bắt buộc | Mô tả       |
| --------- | ------- | -------- | -------- | ----------- |
| accountId | integer |          | Có       | ID địa chỉ  |

#### Ví dụ request

```bash
curl -X DELETE "https://skymail.ink/api/account/delete?accountId=2" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```

## Quản trị viên - Tất cả người dùng

### Danh sách người dùng

**Mô tả**: Truy vấn người dùng theo phân trang, có thể lọc theo Email, trạng thái và trạng thái đã xóa

**Endpoint**: `GET /api/user/list`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số  | Kiểu    | Mặc định | Bắt buộc | Mô tả                                                                                                |
| -------- | ------- | -------- | -------- | ---------------------------------------------------------------------------------------------------- |
| num      | integer | 1        | Không    | Số trang                                                                                             |
| size     | integer | 50       | Không    | Kích thước trang, tối đa 50                                                                          |
| email    | string  |          | Không    | Email, khớp mờ theo tiền tố                                                                          |
| timeSort | integer |          | Không    | Sắp xếp theo thời gian (0 mới nhất, 1 cũ nhất)                                                         |
| status   | integer |          | Không    | Trạng thái người dùng (0 Bình thường, 1 Đã vô hiệu hóa). Khi là 0 hoặc 1, chỉ trả về người dùng chưa bị xóa; bỏ trống hoặc để -1 để không lọc theo trạng thái |
| isDel    | integer |          | Không    | Đã xóa (0 Bình thường, 1 Đã xóa)                                                                     |

#### Ví dụ request

```bash
curl -X GET "https://skymail.ink/api/user/list?num=1&size=50" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "total": 1,
    "list": [
      {
        "userId": 1,                          // ID người dùng
        "email": "admin@example.com",         // Email người dùng
        "type": 0,                            // ID vai trò (Quản trị viên là 0)
        "status": 0,                          // Trạng thái người dùng (0 Bình thường, 1 Đã vô hiệu hóa)
        "isDel": 0,                           // Đã xóa (0 Bình thường, 1 Đã xóa)
        "sendCount": 0,                       // Số đã gửi
        "createTime": "2099-12-30 23:59:59",  // Thời gian đăng ký
        "activeTime": "2099-12-30 23:59:59",  // Thời gian hoạt động gần đây
        "createIp": "127.0.0.1",              // IP đăng ký
        "activeIp": "127.0.0.1",              // IP gần đây
        "os": "Windows",                      // Hệ điều hành
        "browser": "Chrome",                  // Trình duyệt
        "device": "Desktop",                  // Thiết bị
        "receiveEmailCount": 10,              // Số thư đã nhận
        "sendEmailCount": 2,                  // Số thư đã gửi
        "accountCount": 1,                    // Số địa chỉ
        "delReceiveEmailCount": 0,            // Số thư đến đã xóa
        "delSendEmailCount": 0,               // Số thư đã gửi đã xóa
        "delAccountCount": 0,                 // Số địa chỉ đã xóa
        "username": null,                     // Tên người dùng đăng nhập bên thứ ba
        "name": null,                         // Biệt danh đăng nhập bên thứ ba
        "avatar": null,                       // Ảnh đại diện đăng nhập bên thứ ba
        "platform": null,                     // Nền tảng đăng nhập bên thứ ba
        "trustLevel": null,                   // Cấp độ đăng nhập bên thứ ba
        "sendAction": {
          "hasPerm": true,                    // Có quyền gửi
          "sendType": "count",                // Kiểu giới hạn gửi (`count` tổng, `day` theo ngày)
          "sendCount": 0                      // Giới hạn gửi, 0 là Không giới hạn
        }
      }
    ]
  }
}
```

### Thêm người dùng

**Mô tả**: Quản trị viên thêm một người dùng. Tên miền email phải là một tên miền email đã được cấu hình. Mật khẩu phải có ít nhất 6 ký tự

**Endpoint**: `POST /api/user/add`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số   | Kiểu   | Mặc định | Bắt buộc | Mô tả                                            |
| --------- | ------ | -------- | -------- | ------------------------------------------------ |
| email     | string |          | Có       | Địa chỉ email đầy đủ, ví dụ `user@example.com`   |
| password  | string |          | Có       | Mật khẩu, ít nhất 6 ký tự                        |

#### Ví dụ request

```bash
curl -X POST "https://skymail.ink/api/user/add" \
  -H "Authorization: YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "password"
  }'
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```

### Đổi trạng thái

**Mô tả**: Kích hoạt hoặc Vô hiệu hóa một người dùng. Sau khi bị Vô hiệu hóa, mọi phiên Đăng nhập của người dùng đó đều trở nên không hợp lệ

**Endpoint**: `PUT /api/user/setStatus`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số | Kiểu    | Mặc định | Bắt buộc | Mô tả                                                |
| ------- | ------- | -------- | -------- | ---------------------------------------------------- |
| userId  | integer |          | Có       | ID người dùng                                        |
| status  | integer |          | Có       | Trạng thái người dùng (0 Bình thường, 1 Đã vô hiệu hóa) |

#### Ví dụ request

```bash
curl -X PUT "https://skymail.ink/api/user/setStatus" \
  -H "Authorization: YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "userId": 1,
    "status": 1
  }'
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```

### Xóa người dùng

**Mô tả**: Xóa vĩnh viễn một người dùng cùng các địa chỉ, email, mục Đã gắn sao và liên kết đăng nhập bên thứ ba của họ. Không thể khôi phục

**Endpoint**: `DELETE /api/user/delete`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số | Kiểu   | Mặc định | Bắt buộc | Mô tả                                              |
| ------- | ------ | -------- | -------- | -------------------------------------------------- |
| userIds | string |          | Có       | ID người dùng, nhiều giá trị cách nhau bằng dấu phẩy |

#### Ví dụ request

```bash
curl -X DELETE "https://skymail.ink/api/user/delete?userIds=1,2" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```

## Quản trị viên - Tất cả thư

### Danh sách email

**Mô tả**: Truy vấn email của tất cả người dùng, sử dụng phân trang bằng con trỏ `emailId`. Có thể lọc theo loại, Người gửi, Tiêu đề, Email của người dùng và Địa chỉ gửi/nhận

**Endpoint**: `GET /api/allEmail/list`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số      | Kiểu    | Mặc định  | Bắt buộc | Mô tả                                                                                                |
| ------------ | ------- | --------- | -------- | ---------------------------------------------------------------------------------------------------- |
| emailId      | integer |           | Không    | ID email con trỏ, không bắt buộc với lần truy vấn đầu tiên. Khi `timeSort` là 0 thì lấy các email cũ hơn, khi là 1 thì lấy các email mới hơn |
| size         | integer | 10        | Không    | Kích thước trang, tối đa 50                                                                          |
| timeSort     | integer |           | Không    | Sắp xếp theo thời gian (0 mới nhất, 1 cũ nhất)                                                         |
| type         | string  | `receive` | Không    | Loại email (`all` Tất cả, `receive` Thư đến, `send` Thư đã gửi, `delete` Đã xóa, `noone` Không có người nhận) |
| name         | string  |           | Không    | Tên người gửi, khớp mờ theo tiền tố                                                                  |
| subject      | string  |           | Không    | Tiêu đề, khớp mờ theo tiền tố                                                                        |
| userEmail    | string  |           | Không    | Email người dùng, khớp mờ theo tiền tố                                                               |
| accountEmail | string  |           | Không    | Email người gửi hoặc người nhận, khớp mờ theo tiền tố                                                |
| full         | integer | 1         | Không    | Có trả về đầy đủ các trường không (0 tóm tắt, 1 toàn bộ nội dung và File đính kèm)                   |

#### Ví dụ request

```bash
curl -X GET "https://skymail.ink/api/allEmail/list?type=receive&size=10" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "total": 1,
    "latestEmail": {
      "emailId": 999,                         // ID email Thư đến mới nhất, dùng để làm mới bằng polling
      "accountId": 1,
      "userId": 1
    },
    "list": [
      {
        "emailId": 999,                       // ID email
        "sendEmail": "hello@example.com",     // Email người gửi
        "name": "hello",                      // Tên người gửi
        "subject": "Hello word",              // Tiêu đề
        "toEmail": "admin@example.com",       // Email người nhận
        "toName": "admin",                    // Tên người nhận
        "userEmail": "admin@example.com",     // Email người dùng
        "accountId": 1,                       // ID địa chỉ
        "userId": 1,                          // ID người dùng
        "type": 0,                            // Loại email (0 Thư đến, 1 Thư đã gửi)
        "status": 0,                          // Trạng thái email (0 Thư đến, 1 Thư đã gửi, 2 Đã chuyển phát, 3 Bị trả lại, 4 Bị khiếu nại, 5 Bị trì hoãn, 6 Đang lưu, 7 Không có người nhận, 8 Thất bại)
        "unread": 0,                          // Chưa đọc (0 chưa đọc, 1 đã đọc)
        "isDel": 0,                           // Đã xóa (0 Bình thường, 1 Đã xóa)
        "content": "<div>Hello word</div>",   // HTML của email, trả về khi full=1
        "text": "Hello word",                 // Văn bản thuần của email
        "cc": "[]",                           // CC
        "bcc": "[]",                          // BCC
        "createTime": "2099-12-30 23:59:59",  // Thời gian nhận hoặc gửi (UTC)
        "attList": [                          // Danh sách file đính kèm, trả về khi full=1
          {
            "attId": 1,
            "filename": "file.txt",
            "mimeType": "text/plain",
            "size": 1024
          }
        ]
      }
    ]
  }
}
```

### Xóa email

**Mô tả**: Xóa vĩnh viễn các email đã chỉ định cùng File đính kèm và các mục Đã gắn sao của chúng. Không thể khôi phục

**Endpoint**: `DELETE /api/allEmail/delete`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số   | Kiểu   | Mặc định | Bắt buộc | Mô tả                                            |
| --------- | ------ | -------- | -------- | ------------------------------------------------ |
| emailIds  | string |          | Có       | ID email, nhiều giá trị cách nhau bằng dấu phẩy  |

#### Ví dụ request

```bash
curl -X DELETE "https://skymail.ink/api/allEmail/delete?emailIds=1,2" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```

## Quản trị viên - Mã mời

### Danh sách mã mời

**Mô tả**: Truy vấn tất cả Mã mời, có thể lọc theo tiền tố Mã mời. Mã mời đã hết hạn sẽ trả về `null` cho `expireTime`

**Endpoint**: `GET /api/regKey/list`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số | Kiểu   | Mặc định | Bắt buộc | Mô tả                             |
| ------- | ------ | -------- | -------- | --------------------------------- |
| code    | string |          | Không    | Mã mời, khớp mờ theo tiền tố      |

#### Ví dụ request

```bash
curl -X GET "https://skymail.ink/api/regKey/list?code=Ab12" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "regKeyId": 1,                          // ID mã mời
      "code": "Ab12Cd34",                     // Mã mời
      "count": 10,                            // Số lần dùng còn lại
      "roleId": 1,                            // ID vai trò
      "roleName": "user",                     // Tên vai trò
      "userId": 1,                            // ID người dùng tạo
      "expireTime": "2099-12-30 00:00:00",    // Hạn dùng, null nếu Đã hết hạn
      "createTime": "2099-12-30 23:59:59"     // Thời gian tạo
    }
  ]
}
```

### Thêm mã mời

**Mô tả**: Tạo một Mã mời. Mã mời không được trùng lặp. Người dùng đăng ký bằng Mã mời này sẽ nhận được Vai trò tương ứng

**Endpoint**: `POST /api/regKey/add`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số    | Kiểu    | Mặc định | Bắt buộc | Mô tả                                |
| ---------- | ------- | -------- | -------- | ------------------------------------ |
| code       | string  |          | Có       | Mã mời                               |
| roleId     | integer |          | Có       | ID vai trò                           |
| count      | integer |          | Có       | Số lần dùng khả dụng                 |
| expireTime | string  |          | Có       | Hạn dùng, ví dụ `2099-12-30`         |

#### Ví dụ request

```bash
curl -X POST "https://skymail.ink/api/regKey/add" \
  -H "Authorization: YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "code": "Ab12Cd34",
    "roleId": 1,
    "count": 10,
    "expireTime": "2099-12-30"
  }'
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```

### Lịch sử

**Mô tả**: Truy vấn danh sách người dùng đã hoàn tất đăng ký bằng Mã mời này

**Endpoint**: `GET /api/regKey/history`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số  | Kiểu    | Mặc định | Bắt buộc | Mô tả       |
| -------- | ------- | -------- | -------- | ----------- |
| regKeyId | integer |          | Có       | ID mã mời   |

#### Ví dụ request

```bash
curl -X GET "https://skymail.ink/api/regKey/history?regKeyId=1" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": [
    {
      "email": "user@example.com",            // Email người dùng đã đăng ký
      "createTime": "2099-12-30 23:59:59"     // Thời gian đăng ký
    }
  ]
}
```

### Xóa mã mời

**Mô tả**: Xóa các Mã mời đã chỉ định

**Endpoint**: `DELETE /api/regKey/delete`

#### Header của request

| Header        | Bắt buộc | Mô tả          |
| ------------- | -------- | -------------- |
| Authorization | Có       | token xác thực |

#### Tham số của request

| Tham số   | Kiểu   | Mặc định | Bắt buộc | Mô tả                                               |
| --------- | ------ | -------- | -------- | --------------------------------------------------- |
| regKeyIds | string |          | Có       | ID mã mời, nhiều giá trị cách nhau bằng dấu phẩy    |

#### Ví dụ request

```bash
curl -X DELETE "https://skymail.ink/api/regKey/delete?regKeyIds=1,2" \
  -H "Authorization: YOUR_TOKEN"
```

#### Ví dụ response

```json
{
  "code": 200,
  "message": "success",
  "data": null
}
```
