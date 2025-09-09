# WebSocket Get Team Members Handler

## Mô tả
Handler `GET_TEAM_MEMBERS` cho phép người chơi lấy danh sách thành viên của một team cụ thể, bao gồm thông tin về role và trạng thái của từng thành viên.

## Thông tin Handler
- **Command**: `teams/get_members`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/get_members",
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
    "cmd": "teams/get_members",
    "rid": -1
  },
  "data": "{
    \"members\": [
      {
        \"memberId\": \"encoded_user_id\",
        \"role\": \"LEADER\",
        \"joinAt\": 1673625600000
      },
      {
        \"memberId\": \"encoded_user_id_2\",
        \"role\": \"COLEADER\",
        \"joinAt\": 1673626600000
      },
      {
        \"memberId\": \"encoded_user_id_3\",
        \"role\": \"MEMBER\",
        \"joinAt\": 1673627600000
      }
    ]
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết GetTeamMembersResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `members` | List<TeamMember> | Danh sách thành viên trong team |


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

### Lỗi quyền hạn

#### 3. Không có quyền xem danh sách thành viên (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "Permission denied to view team members"
}
```

### Lỗi hệ thống

#### 4. Lỗi lấy danh sách thành viên (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to get team members"
}
```

#### 5. Lỗi xác thực/phiên (errCode: 7)
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
2. **Limited info**: Chỉ trả về thông tin cơ bản, không bao gồm dữ liệu nhạy cảm
3. **Role hierarchy**: LEADER > COLEADER > MEMBER trong quyền hạn
