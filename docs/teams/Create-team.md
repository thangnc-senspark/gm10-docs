# WebSocket Create Team Handler

## Mô tả
Handler `CREATE_TEAM` cho phép người chơi tạo một nhóm (team) mới trong game. Người tạo team sẽ tự động trở thành leader của team đó.

## Thông tin Handler
- **Command**: `teams/create`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/create",
    "rid": -1
  },
  "data": "{
    \"teamName\": \"string\",
    \"avatar\": \"string\", 
    \"description\": \"string\",
    \"teamType\": \"OPEN|CLOSED\",
    \"minimumScoreRequired\": number
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `teamName` | String | Có | Tên nhóm | 3-16 ký tự, không được để trống |
| `avatar` | String | Có | Avatar/badge của nhóm | - |
| `description` | String | Có | Mô tả về nhóm | Tối đa 200 ký tự |
| `teamType` | TeamType | Có | Loại nhóm | `OPEN` (mở) hoặc `CLOSED` (đóng) |
| `minimumScoreRequired` | Int | Không | Điểm tối thiểu để tham gia | Mặc định: 0, không được âm |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/create",
    "rid": -1,
  },
  "data": "{
    \"team\": {
      \"hashTeamId\": \"encoded_team_id\",
      \"teamName\": \"Team Rocket\",
      \"avatar\": \"rocket_icon\",
      \"description\": \"Team chuyên nghiệp dành cho các thợ mỏ giỏi!\",
      \"teamType\": \"OPEN\",
      \"minimumScoreRequired\": 1000,
      \"createdAt\": 1673625600000,
      \"lastModifiedAt\": 1673625600000
    }
  }",
  "errCode": 0,
  "errMsg": null
}
```
Response data trả về `TeamData` objects.

## Lỗi có thể gặp

### Lỗi validation dữ liệu đầu vào

#### 1. Thiếu dữ liệu team (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing team data"
}
```

#### 2. Tên team không hợp lệ (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Team name must be 3-16 characters"
}
```

#### 3. Mô tả quá dài (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Description too long (max 200 characters)"
}
```

#### 4. Điểm tối thiểu không hợp lệ (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Minimum score cannot be negative"
}
```

### Lỗi hệ thống

#### 5. Lỗi tạo team (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to create team"
}
```

#### 6. Lỗi xác thực/phiên (errCode: 7)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 7,
  "errMsg": "Invalid session"
}
```

#### 7. Lỗi chung không xác định (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Unexpected error: Unknown error"
}
```


## Lưu ý quan trọng

1. **Xác thực**: Handler yêu cầu người dùng phải đăng nhập hợp lệ
2. **Quyền hạn**: Người tạo team tự động trở thành leader
3. **Tên team**: Phải từ 3-16 ký tự, không được để trống hoặc chỉ chứa khoảng trắng
4. **Mô tả**: Tối đa 200 ký tự
5. **Điểm tối thiểu**: Phải >= 0

