# WebSocket Leave Team Treasure Event Handler

## Mô tả
Handler `LEAVE_TEAM_TREASURE_EVENT` cho phép người chơi rời khỏi sự kiện Team Treasure đang active của team mình. Khi rời, toàn bộ dữ liệu đóng góp (ship wheels, claimed rewards) của người chơi trong sự kiện sẽ bị xóa khỏi DB. Người chơi sau đó có thể join lại bằng `team_treasure/join` nếu muốn.

## Thông tin Handler
- **Command**: `team_treasure/leave`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "team_treasure/leave",
    "rid": -1
  },
  "data": null
}
```

### Chi tiết các trường dữ liệu
Handler này **không cần dữ liệu đầu vào**.

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "team_treasure/leave",
    "rid": -1
  },
  "data": null,
  "errCode": 0,
  "errMsg": null
}
```

Handler này trả về empty success response (không có data trong response body). Kiểm tra thành công hay không bằng `errCode` (0 là thành công, khác 0 là lỗi).

## Lỗi có thể gặp

### 1. Người dùng không thuộc team nào (errCode: 5)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 5,
  "errMsg": "User not in a team"
}
```

### 2. Team không có sự kiện active (errCode: 5)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 5,
  "errMsg": "No active event for user's team"
}
```

### 3. Người dùng không phải participant của sự kiện (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to leave team treasure event"
}
```
Lỗi này xảy ra khi người chơi chưa join sự kiện (chưa là participant), hoặc thao tác DELETE DB không ảnh hưởng bản ghi nào.

### 4. Lỗi xác thực/phiên (errCode: 7)
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
3. **Ảnh hưởng tổng team**: `teamCurrentWheels` sẽ giảm tương ứng với số wheels người chơi đã đóng góp trước khi rời
4. **Join lại**: Sau khi rời, người chơi có thể join lại bằng `team_treasure/join` với trạng thái mới (wheels = 0, chưa claim bất kỳ phần thưởng nào)

