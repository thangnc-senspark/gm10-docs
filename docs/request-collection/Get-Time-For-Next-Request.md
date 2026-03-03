# WebSocket Get Time For Next Collection Request Handler

## Mô tả
Handler `TIME_FOR_NEXT_HELP_COLLECTION` trả về thời gian (tính bằng giây) mà user phải chờ trước khi có thể gửi một Request Collection mới. Mỗi user có cooldown **24 giờ** giữa các lần gửi request.

## Thông tin Handler
- **Command**: `chat/helps/time_for_next_collection`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

Không yêu cầu `data` — chỉ cần gửi `rid`.

```json
{
  "rid": {
    "cmd": "chat/helps/time_for_next_collection",
    "rid": -1
  },
  "data": null
}
```

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "chat/helps/time_for_next_collection",
    "rid": -1
  },
  "data": "{
    \"remainingTimeSeconds\": 3600
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết TimeForNextRequestResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `remainingTimeSeconds` | Long | Số giây còn lại đến khi user có thể gửi request mới. `0` = có thể gửi ngay. |

### Ý nghĩa giá trị `remainingTimeSeconds`

| Giá trị | Ý nghĩa |
|---------|---------|
| `0` | User có thể gửi Request Collection ngay lập tức |
| `> 0` | Số giây phải chờ thêm trước khi gửi được request mới |

## Ghi chú
- Cooldown **24 giờ** được tính từ thời điểm tạo request gần nhất.
- Request đã hết hạn (> 24h) không tính vào cooldown — `remainingTimeSeconds` sẽ trả về `0`.

