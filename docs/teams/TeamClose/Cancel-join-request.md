# WebSocket Cancel Join Request Handler

## Mô tả
Handler `CANCEL_JOIN_REQUEST` cho phép người dùng hủy yêu cầu tham gia team mà họ đã gửi trước đó. Yêu cầu sẽ được đánh dấu là đã hủy và không thể được leader xử lý nữa.

## Thông tin Handler
- **Command**: `teams/request_join/cancel`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket
- **Quyền hạn**: Chỉ người gửi yêu cầu mới có thể hủy

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/request_join/cancel",
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
| `requestId` | String | Có | Hash ID của yêu cầu tham gia cần hủy | ID đã được encode |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/request_join/cancel",
    "rid": -1
  },
  "data": "{
    \"message\": \"Join request cancelled successfully\"
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết CancelJoinRequestResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `message` | String | Thông báo xác nhận hủy yêu cầu thành công |

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

#### 4. Không phải người gửi yêu cầu (errCode: 4)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 4,
  "errMsg": "Only request owner can cancel the request"
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
  "errMsg": "Cannot cancel already processed request"
}
```

#### 7. Yêu cầu đã được hủy trước đó (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Request already cancelled"
}
```

### Lỗi hệ thống

#### 8. Lỗi hủy yêu cầu (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to cancel join request"
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

1. **Quyền sở hữu**: Chỉ người gửi yêu cầu ban đầu mới có thể hủy yêu cầu của chính mình
2. **Trạng thái yêu cầu**: Chỉ các yêu cầu có trạng thái `PENDING` mới có thể được hủy
3. **Không thể hoàn tác**: Sau khi hủy, yêu cầu sẽ có trạng thái `CANCELLED` và không thể khôi phục
4. **Tạo yêu cầu mới**: User có thể gửi yêu cầu mới sau khi hủy yêu cầu cũ
5. **Notification**: Leader/co-leader sẽ được thông báo về việc hủy yêu cầu

## Flow xử lý

### Quy trình hủy yêu cầu:
1. Xác thực user đã đăng nhập
2. Validate request ID
3. Kiểm tra quyền sở hữu yêu cầu (user phải là người gửi)
4. Kiểm tra trạng thái yêu cầu (phải là PENDING)
5. Cập nhật trạng thái yêu cầu thành CANCELLED
6. Ghi lại thời gian hủy
7. Gửi notification đến leader/co-leader của team
8. Trả về kết quả thành công

### Trường hợp đặc biệt:
- Nếu user đã tham gia team khác: Yêu cầu tự động được hủy
- Nếu team đã bị giải tán: Yêu cầu tự động có trạng thái EXPIRED
- User có thể gửi lại yêu cầu mới sau khi hủy

## Tác động đến hệ thống

1. **Database**: Cập nhật bảng join_team_request với trạng thái CANCELLED
2. **Notification**: Gửi thông báo realtime đến leader/co-leader
3. **UI Updates**: Cập nhật danh sách yêu cầu pending của team
4. **Analytics**: Ghi lại metrics về tỷ lệ hủy yêu cầu

## Các trường hợp sử dụng phổ biến

1. **Đổi ý**: User không muốn tham gia team nữa
2. **Tìm team khác**: User tìm được team phù hợp hơn
3. **Quá lâu chờ**: Leader không phản hồi trong thời gian dài
4. **Thay đổi kế hoạch**: User tạm dừng việc tìm team
5. **Lỗi gửi nhầm**: User gửi yêu cầu đến sai team

## Best Practices

1. **User experience**: Cho phép user dễ dàng hủy yêu cầu từ UI
2. **Feedback**: Hiển thị trạng thái yêu cầu rõ ràng
3. **Limitations**: Có thể giới hạn số lần gửi/hủy yêu cầu trong một khoảng thời gian
4. **Notifications**: Đảm bảo thông báo đến đúng người liên quan
