# WebSocket Update Team Handler

## Mô tả
Handler `UPDATE_TEAM` cho phép leader cập nhật thông tin cơ bản của team như tên, avatar, mô tả, loại team và điểm tối thiểu.

## Thông tin Handler
- **Command**: `teams/update_info`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/update_info",
    "rid": -1
  },
  "data": "{
    \"teamId\": \"string\",
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
| `teamId` | String | Có | Hash ID của team cần cập nhật | ID đã được encode |
| `teamName` | String | Có | Tên mới của team | 3-16 ký tự, không được để trống |
| `avatar` | String | Có | Avatar/badge mới của team | - |
| `description` | String | Có | Mô tả mới về team | Tối đa 200 ký tự |
| `teamType` | TeamType | Có | Loại team | `OPEN` (mở) hoặc `CLOSED` (đóng) |
| `minimumScoreRequired` | Int | Có | Điểm tối thiểu mới để tham gia | Không được âm |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/update_info",
    "rid": -1
  },
  "data": "{
    \"success\": true
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết UpdateTeamDescriptionResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `success` | Boolean | Trạng thái cập nhật team thành công |

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

### Lỗi quyền hạn

#### 5. Không phải leader (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "Only team leader can update team profile"
}
```

#### 6. Team không tồn tại (errCode: 3)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 3,
  "errMsg": "Team not found"
}
```

### Lỗi hệ thống

#### 7. Lỗi cập nhật team (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to update team profile"
}
```

#### 8. Lỗi xác thực/phiên (errCode: 7)
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
2. **Quyền hạn**: Chỉ LEADER mới có quyền cập nhật thông tin team
3. **Team type change**: Chuyển từ OPEN sang CLOSED hoặc ngược lại có thể ảnh hưởng đến join requests
