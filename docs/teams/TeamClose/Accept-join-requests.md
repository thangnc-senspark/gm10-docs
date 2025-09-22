# WebSocket Accept Join Requests Handler

## Mô tả
Handler `ACCEPT_JOIN_REQUESTS` cho phép leader hoặc co-leader của team chấp nhận nhiều yêu cầu tham gia team cùng một lúc. Thành viên được chấp nhận sẽ tự động được thêm vào team với vai trò member.

## Thông tin Handler
- **Command**: `teams/join_request/accepts`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket
- **Quyền hạn**: Chỉ leader hoặc co-leader có thể sử dụng

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/join_request/accepts",
    "rid": -1
  },
  "data": "{
    \"requestIds\": [\"string\", \"string\", ...]
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `requestIds` | Array[String] | Có | Danh sách ID của các yêu cầu tham gia cần chấp nhận | Tối thiểu 1 phần tử |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/join_request/accepts",
    "rid": -1
  },
  "data": "{
    \"message\": \"Join requests accepted successfully\"
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết AcceptJoinRequestsResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `message` | String | Thông báo xác nhận chấp nhận yêu cầu thành công |

## Lỗi có thể gặp

### Lỗi validation dữ liệu đầu vào

#### 1. Thiếu dữ liệu yêu cầu (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Invalid request data"
}
```

#### 2. Danh sách requestIds trống (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Request IDs list cannot be empty"
}
```

#### 3. Request ID không hợp lệ (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Invalid request ID format"
}
```

### Lỗi quyền hạn và trạng thái

#### 4. Không phải leader/co-leader (errCode: 4)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 4,
  "errMsg": "Only team leader or co-leader can accept join requests"
}
```

#### 5. Yêu cầu không tồn tại (errCode: 3)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 3,
  "errMsg": "Join request not found"
}
```

#### 6. Yêu cầu đã được xử lý (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Join request already processed"
}
```

#### 7. Team đã đầy (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Team is full, cannot accept more members"
}
```

### Lỗi hệ thống

#### 8. Lỗi chấp nhận yêu cầu (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to accept join requests"
}
```

#### 9. Lỗi xác thực/phiên (errCode: 7)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 7,
  "errMsg": "Invalid session"
}
```

#### 10. Lỗi chung không xác định (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Unexpected error: Unknown error"
}
```


## Flow xử lý

### Quy trình chấp nhận yêu cầu:
1. Xác thực quyền hạn của user (leader/co-leader)
2. Validate danh sách request IDs
3. Kiểm tra trạng thái các yêu cầu (phải là PENDING)
4. Kiểm tra giới hạn thành viên của team
5. Chấp nhận các yêu cầu hợp lệ
6. Cập nhật trạng thái yêu cầu thành APPROVED
7. Thêm user vào team với vai trò MEMBER
8. Gửi notification đến các user được chấp nhận
9. Trả về kết quả thành công

