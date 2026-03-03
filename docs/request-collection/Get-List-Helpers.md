# WebSocket Get List Helpers For Collection Requests Handler

## Mô tả
Handler `GET_HELP_COLLECTIONS` trả về danh sách các user đã giúp cho từng Request Collection, dựa trên danh sách `requestIds` được cung cấp.

## Thông tin Handler
- **Command**: `chat/helps/user_help_collections`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "chat/helps/user_help_collections",
    "rid": -1
  },
  "data": "{
    \"requestIds\": [123, 456, 789]
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `requestIds` | Array[Long] | Có | Danh sách message ID của các Collection Request cần lấy helpers |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "chat/helps/user_help_collections",
    "rid": -1
  },
  "data": "{
  \"123\": [ \"abc123\"],
  \"456\": [],
  \"789\": [{ \"abv\", \"hashId\", \"def456\" }]
}",
"errCode": 0,
"errMsg": null
}
```

### Chi tiết Response

Response là một **Map** với key là `requestId` (Long dạng String) và value là danh sách `UserId` của những người đã giúp.

| Key | Kiểu Value | Mô tả |
|-----|-----------|-------|
| `"<requestId>"` | Array[UserId] | Danh sách helpers của request đó. Mảng rỗng nếu chưa có ai giúp hoặc request không tồn tại/hết hạn. |


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

## Ghi chú
- Request đã **hết hạn (> 24h)** sẽ trả về mảng rỗng `[]` cho requestId đó.
- `requestId` không tồn tại cũng sẽ trả về mảng rỗng `[]`.
- Mỗi Collection Request chỉ có tối đa **1 helper**.

