# WebSocket Reject Join Request Handler

## Mô tả
Handler `REJECT_JOIN_REQUEST` cho phép leader hoặc co-leader của team từ chối yêu cầu tham gia team. Yêu cầu sẽ được đánh dấu là bị từ chối và user không thể tham gia team.

## Thông tin Handler
- **Command**: `teams/join_request/reject`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket
- **Quyền hạn**: Chỉ leader hoặc co-leader có thể sử dụng

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/join_request/reject",
    "rid": -1
  },
  "data": "{
    \"requestId\": \"string\"
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `requestId` | String | Có |  ID của yêu cầu tham gia cần từ chối | ID đã được encode |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/join_request/reject",
    "rid": -1
  },
  "data": "{
    \"message\": \"Join request rejected successfully\"
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết RejectJoinRequestResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `message` | String | Thông báo xác nhận từ chối yêu cầu thành công |

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

#### 2. Thiếu request ID (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing request ID"
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
  "errMsg": "Only team leader or co-leader can reject join requests"
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

#### 7. Không thuộc team này (errCode: 4)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 4,
  "errMsg": "Cannot reject request for different team"
}
```

### Lỗi hệ thống

#### 8. Lỗi từ chối yêu cầu (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to reject join request"
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

## Lưu ý quan trọng

1. **Quyền hạn**: Chỉ leader hoặc co-leader của team mới có thể từ chối yêu cầu tham gia
2. **Trạng thái yêu cầu**: Chỉ các yêu cầu có trạng thái `PENDING` mới có thể được từ chối
3. **Không thể hoàn tác**: Sau khi từ chối, yêu cầu sẽ có trạng thái `REJECTED` và không thể thay đổi
4. **Notification**: Hệ thống sẽ gửi thông báo đến user bị từ chối
5. Nếu user đã tham gia team khác: Tự động đánh dấu request là CANCELLED
6. Nếu team đã bị giải tán: Tự động đánh dấu request là EXPIRED
