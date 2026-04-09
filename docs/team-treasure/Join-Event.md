# WebSocket Join Team Treasure Event Handler

## Mô tả
Handler `JOIN_TEAM_TREASURE_EVENT` cho phép người chơi tham gia sự kiện Team Treasure đang active của team mình.  Nếu người chơi đã là participant, API vẫn trả về thành công (idempotent).

## Thông tin Handler
- **Command**: `team_treasure/join`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "team_treasure/join",
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
    "cmd": "team_treasure/join",
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

### 3. Lỗi ghi DB (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to join team treasure event"
}
```
Lỗi này xảy ra khi thao tác INSERT vào DB không ghi được bản ghi nào (trường hợp hiếm gặp).

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
3. **Active event**: Team phải có sự kiện Team Treasure đang active (status = `ACTIVE`)
4. **Idempotent**: Nếu người chơi đã là participant của sự kiện, API vẫn trả về `errCode: 0` thành công mà không tạo bản ghi trùng
5. **Sau khi join**: Người chơi có thể dùng `team_treasure/add_wheels` để đóng góp ship wheels và `team_treasure/claim_step` / `team_treasure/claim_bonus` để nhận phần thưởng

