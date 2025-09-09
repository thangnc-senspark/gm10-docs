# WebSocket Kick Member Handler

## Mô tả
Handler `KICK_MEMBER` cho phép leader hoặc co-leader đuổi một thành viên ra khỏi team. Chỉ có thể kick những thành viên có level quyền hạn thấp hơn.

## Thông tin Handler
- **Command**: `teams/kick_member`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/kick_member",
    "rid": -1
  },
  "data": "{
    \"targetUserId\": \"string\"
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `targetUserId` | String | Có | Hash ID của user cần kick | ID đã được encode |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/kick_member",
    "rid": -1
  },
  "data": "{
    \"success\": true
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết KickMemberResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `success` | Boolean | Trạng thái kick thành viên thành công |

## Lỗi có thể gặp

### Lỗi validation dữ liệu đầu vào

#### 1. Thiếu dữ liệu kick (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing kick data"
}
```

#### 2. Target user không tồn tại (errCode: 3)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 3,
  "errMsg": "Target user not found"
}
```

### Lỗi quyền hạn

#### 3. Không có quyền kick (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "Failed to kick member"
}
```

#### 4. Không thể kick leader (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "Cannot kick team leader"
}
```

#### 5. Co-leader không thể kick co-leader khác (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "Co-leader cannot kick other co-leaders"
}
```

#### 6. Không thể kick chính mình (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "Cannot kick yourself"
}
```

#### 7. Target không phải thành viên team (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "Target user is not a member of your team"
}
```

### Lỗi hệ thống

#### 8. Lỗi kick thành viên (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to kick member"
}
```

#### 9. Lỗi xác thực/phiên (errCode: 7)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 7,
  "errMsg": "Invalid session"
}
```

## Quy tắc Kick Member

### Hierarchy quyền hạn
1. **LEADER**: Có thể kick tất cả (COLEADER và MEMBER)
2. **COLEADER**: Chỉ có thể kick MEMBER, không thể kick LEADER hoặc COLEADER khác
3. **MEMBER**: Không có quyền kick ai

## Lưu ý quan trọng

1. **Xác thực**: Handler yêu cầu người dùng phải đăng nhập hợp lệ
2. **Quyền hạn**: Chỉ LEADER và COLEADER mới có quyền kick thành viên
3. **Notification**: Thành viên bị kick sẽ nhận được thông báo