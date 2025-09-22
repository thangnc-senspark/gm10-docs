# WebSocket Get My Pending Requests Handler

## Mô tả
Handler `GET_MY_PENDING_REQUESTS` cho phép người dùng lấy danh sách tất cả các yêu cầu tham gia team mà họ đã gửi và đang trong trạng thái chờ xử lý (PENDING).

## Thông tin Handler
- **Command**: `teams/join_request/mine`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket
- **Quyền hạn**: Mọi user đã đăng nhập

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/join_request/mine",
    "rid": -1
  },
  "data": null
}
```

### Chi tiết các trường dữ liệu

Không cần dữ liệu đầu vào.

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/join_request/mine",
    "rid": -1
  },
  "data": "[
    {
      \"requestId\": \"encoded_request_id_1\",
      \"teamId\": \"encoded_team_id_1\",
      \"userId\": \"userId\",
      \"status\": \"PENDING\",
    },
    ...
    {
      \"requestId\": \"encoded_request_id_2\",
      \"teamId\": \"encoded_team_id_2\",
      \"userId\": \"userId\",
      \"status\": \"PENDING\",
    }
  ]",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết PendingJoinRequest Object

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `requestId` | String | ID nội bộ của yêu cầu |
| `teamId` | String | ID nội bộ của team  |
| `userId` | String | ID user gửi yêu cầu |
| `status` | JoinRequestStatus | Trạng thái yêu cầu (luôn là PENDING) |


## Lỗi có thể gặp

### Lỗi xác thực

#### 1. Lỗi xác thực/phiên (errCode: 7)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 7,
  "errMsg": "Invalid session"
}
```

#### 2. Lỗi chung không xác định (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Unexpected error: Unknown error"
}
```

## Lưu ý quan trọng

1. **Chỉ PENDING**: Chỉ trả về các yêu cầu có trạng thái PENDING
2. **Sắp xếp**: Yêu cầu được sắp xếp theo thời gian tạo (mới nhất trước)
3. **Real-time**: Có thể sử dụng kết hợp với notification để cập nhật realtime
4. **Performance**: Handler được tối ưu cho việc truy vấn nhanh
