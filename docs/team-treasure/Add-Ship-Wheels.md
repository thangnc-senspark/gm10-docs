# WebSocket Add Ship Wheels Handler

## Mô tả
Handler `ADD_SHIP_WHEELS` cho phép người chơi thêm ship wheels vào sự kiện Team Treasure của team mình. Wheels được tích lũy từ các hoạt động chơi game. Handler này ghi nhận số wheels mà người chơi kiếm được và cộng vào tổng của team.

## Thông tin Handler
- **Command**: `team_treasure/add_wheels`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "team_treasure/add_wheels",
    "rid": -1
  },
  "data": "{
    \"amount\": 10
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `amount` | Int | Có | Số ship wheels muốn thêm | Phải > 0 |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "team_treasure/add_wheels",
    "rid": -1
  },
  "data": null,
  "errCode": 0,
  "errMsg": null
}
```

Handler này trả về empty success response (không có data trong response body). Kiểm tra thành công hay không bằng `errCode` (0 là thành công, khác 0 là lỗi).

## Lỗi có thể gặp

### 1. Thiếu dữ liệu request (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing data"
}
```

### 2. Người dùng không thuộc team nào (errCode: 5)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 5,
  "errMsg": "User not in a team"
}
```

### 3. Team không có sự kiện active (errCode: 5)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 5,
  "errMsg": "No active event for user's team"
}
```

### 4. Người dùng không phải là participant của sự kiện (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to add ship wheels"
}
```
Lỗi này xảy ra khi người dùng chưa được đăng ký làm participant của sự kiện (có thể do người dùng mới join team sau khi sự kiện bắt đầu).

### 5. Lỗi xác thực/phiên (errCode: 7)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 7,
  "errMsg": "Invalid session"
}
```

## Lưu ý quan trọng

1. **Xác thực**: Handler yêu cầu người dùng phải đăng nhập hợp lệ
2. **Team membership**: Người dùng phải là thành viên của một team
3. **Active event**: Team phải có sự kiện Team Treasure đang active (status = `ACTIVE`)
4. **Participant**: Người dùng phải là participant của sự kiện
5. **Cộng dồn**: Wheels được cộng dồn vào:
   - Tổng cá nhân của người chơi (`shipWheelsEarned` của participant)
   - Tổng của toàn team (`teamCurrentWheels`)


