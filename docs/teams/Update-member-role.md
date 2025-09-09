# WebSocket Update Member Role Handler

## Mô tả
Handler `UPDATE_MEMBER_ROLE` cho phép leader thay đổi vai trò của thành viên trong team, bao gồm promote thành co-leader hoặc demote về member thường.

## Thông tin Handler
- **Command**: `teams/update_member_role`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/update_member_role",
    "rid": -1
  },
  "data": "{
    \"targetUserId\": \"string\",
    \"newRole\": \"MEMBER\"
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `targetUserId` | String | Có | Hash ID của user cần thay đổi role | ID đã được encode |
| `newRole` | MemberRole | Có | Vai trò mới của thành viên | `MEMBER`, `COLEADER` |


## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/update_member_role",
    "rid": -1
  },
  "data": "{
    \"success\": true
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết UpdateMemberRoleResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `success` | Boolean | Trạng thái thay đổi role thành công |

## Lỗi có thể gặp

### Lỗi validation dữ liệu đầu vào

#### 1. Thiếu dữ liệu role update (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing role update data"
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

#### 3. Không có quyền hạn (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "Permission denied"
}
```

### Lỗi hệ thống

#### 4. Lỗi cập nhật role (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to update member role"
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
2. **Leader only**: Chỉ LEADER mới có quyền thay đổi role thành viên
3. **Co-leader limit**: Mỗi team có giới hạn số lượng co-leader
4. **Notification**: Thành viên được thay đổi role sẽ nhận thông báo
