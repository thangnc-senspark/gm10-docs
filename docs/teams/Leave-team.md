# WebSocket Leave Team Handler

## Mô tả
Handler `LEAVE_TEAM` cho phép người chơi rời khỏi nhóm (team) hiện tại. Thành viên có thể tự động rời team.

## Thông tin Handler
- **Command**: `teams/leave`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/leave",
    "rid": -1
  },
  "data": null
}
```

### Chi tiết các trường dữ liệu

Không cần dữ liệu đầu vào. 

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/leave",
    "rid": -1
  },
  "data": "{
    \"success\": true
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết LeaveTeamResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `success` | Boolean | Trạng thái rời team thành công |

## Lỗi có thể gặp

### Lỗi logic nghiệp vụ

#### 1. Không có team để rời (errCode: 3)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 3,
  "errMsg": "User is not in any team"
}
```

### Lỗi hệ thống

#### 2. Lỗi rời team (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to leave team"
}
```

#### 3. Lỗi xác thực/phiên (errCode: 7)
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
2. **Leader cuối cùng**: Nếu leader rời team. 1 thành viên khác sẽ lên chức leader thay thế cho leader cũ. 
3. **Auto-dissolve**: Nếu team chỉ còn 1 thành viên sau khi rời, team sẽ tự động bị giải tán
