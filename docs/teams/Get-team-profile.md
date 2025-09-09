# WebSocket Get Team Profile Handler

## Mô tả
Handler `GET_TEAM_PROFILE` cho phép người chơi lấy thông tin chi tiết về một team cụ thể thông qua team ID, bao gồm thông tin cơ bản, số thành viên và tổng điểm team.

## Thông tin Handler
- **Command**: `teams/profile`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/profile",
    "rid": -1
  },
  "data": "{
    \"teamId\": \"string\"
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `teamId` | String | Có | Hash ID của team cần lấy thông tin | ID đã được encode |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/profile",
    "rid": -1
  },
  "data": "{
    \"teamData\": {
      \"hashTeamId\": \"encoded_team_id\",
      \"teamName\": \"Elite Warriors\",
      \"avatar\": \"warrior_icon\",
      \"description\": \"Top tier competitive team\",
      \"teamType\": \"CLOSED\",
      \"minimumScoreRequired\": 2000,
      \"createdAt\": 1673625600000,
      \"lastModifiedAt\": 1673625600000
    },
    \"memberCount\": 18,
    \"teamScore\": 75000,
    \"activity\": \"HIGH\"
  }",
  "errCode": 0,
  "errMsg": null
}
```
Response data trả về `TeamProfile` objects.


## Lỗi có thể gặp

### Lỗi validation dữ liệu đầu vào

#### 1. Thiếu team ID (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing team ID"
}
```

#### 2. Team không tồn tại (errCode: 3)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 3,
  "errMsg": "Team not found"
}
```

### Lỗi hệ thống

#### 3. Lỗi lấy thông tin team (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to get team profile"
}
```

#### 4. Lỗi xác thực/phiên (errCode: 7)
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
2. **Public access**: Có thể xem profile của bất kỳ team nào (không cần là thành viên)
3. **Caching**: Dữ liệu có thể được cache để tối ưu hiệu suất
