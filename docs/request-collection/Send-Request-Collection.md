# WebSocket Send Request Collection Message Handler

## Mô tả
Handler `SEND_REQUEST_COLLECTION` cho phép người dùng gửi một yêu cầu xin trợ giúp vật phẩm collection vào phòng chat. Mỗi user chỉ được gửi tối đa 1 request trong vòng **24 giờ**. Request sẽ tự động hết hạn sau **24 giờ** kể từ thời điểm tạo.

## Thông tin Handler
- **Command**: `chat/send_request_collection`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket
- **Response**: Không trả về response trực tiếp — server sẽ broadcast message tới các thành viên trong phòng

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "chat/send_request_collection",
    "rid": -1
  },
  "data": "{
    \"roomId\": \"string\",
    \"collectionId\": 1,
    \"itemId\": \"string\"
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `roomId` | String | Có | Hash ID của phòng chat | ID đã được encode bằng HashIdGenerator |
| `collectionId` | Int | Có | ID của bộ sưu tập cần xin item | Phải là ID collection hợp lệ |
| `itemId` | String | Có | ID của vật phẩm trong bộ sưu tập cần xin | Không được rỗng |

## Response Data

Handler này **không trả về response trực tiếp**. Sau khi message được tạo thành công, server sẽ broadcast message mới tới tất cả thành viên trong phòng qua command `chat/new`.

### Broadcast được gửi đi (command: `chat/new`)
```json
{
  "rid": {
    "cmd": "chat/new",
    "rid": -1
  },
  "data": "{
    \"id\": 123,
    \"type\": \"REQUEST_COLLECTION\",
    \"collectionRequestMessage\": {
      \"userRequest\": {
        \"uid\": 1,
        \"hashId\": \"abc123\"
      },
      \"helper\": [],
      \"collectionId\": 1,
      \"itemId\": \"item_001\"
    },
    \"createAt\": 1709500000000
  }"
}
```

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

### 2. User vẫn đang trong cooldown 24h (không gửi được request mới)
Server sẽ không tạo message mới. Dùng API [`chat/helps/time_for_next_collection`](Get-Time-For-Next-Request.md) để kiểm tra thời gian còn lại.

## Ghi chú
- Request hết hạn sau **24 giờ** — sau đó sẽ không còn xuất hiện trong danh sách.
- Một user chỉ có **1 request active** tại một thời điểm (cooldown 24h).
- Chỉ cần **1 helper** để hoàn thành request (`MAX_PROGRESS = 1`).

