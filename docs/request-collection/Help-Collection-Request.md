# WebSocket Help Collection Request Handler

## Mô tả
Handler `HELP_COLLECTION_REQUEST` cho phép một user giúp đỡ một Request Collection của người khác trong cùng phòng chat. Khi giúp thành công, server sẽ broadcast cập nhật message tới tất cả thành viên trong phòng.

## Thông tin Handler
- **Command**: `chat/helps/help_collection_request`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "chat/helps/help_collection_request",
    "rid": -1
  },
  "data": "{
    \"requestId\": 123
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `requestId` | Long | Có | Message ID của Collection Request cần giúp |

## Response Data
Không có response trực tiếp. Sau khi giúp thành công, server sẽ broadcast cập nhật message tới tất cả thành viên trong phòng qua command `chat/update`.

## Broadcast khi thành công (command: `chat/update`)

Sau khi giúp thành công, server sẽ broadcast message được cập nhật tới tất cả thành viên trong phòng:

```json
{
  "rid": {
    "cmd": "chat/update",
    "rid": -1
  },
  "data": "{
    \"id\": 123,
    \"type\": \"REQUEST_COLLECTION\",
    \"collectionRequestMessage\": {
      \"userRequest\": { \"uid\": 1, \"hashId\": \"abc123\" },
      \"helper\": [
        { \"uid\": 2, \"hashId\": \"def456\" }
      ],
      \"collectionId\": 1,
      \"itemId\": \"item_001\"
    },
    \"createAt\": 1709500000000
  }"
}
```

## Các điều kiện thất bại

| Điều kiện | Mô tả |
|-----------|-------|
| Request không tồn tại | `requestId` không tìm thấy trong hệ thống |
| Request đã hết hạn | Request đã quá **24 giờ** kể từ `createAt` |
| Tự giúp chính mình | User không thể giúp request do chính mình tạo |
| Đã giúp trước đó | User đã giúp request này rồi |
| Request đã đủ helper | Request đã có đủ 1 helper (đã hoàn thành) |

## Lỗi có thể gặp

### 1. Thiếu dữ liệu request (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Invalid request data"
}
```

