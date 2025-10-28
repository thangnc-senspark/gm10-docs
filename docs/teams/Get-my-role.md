# WebSocket Get My Role Handler

## Mô tả
Handler `GET_MY_ROLE` cho phép người chơi lấy thông tin về vai trò (role) của chính họ trong team hiện tại mà họ đang tham gia.

## Thông tin Handler
- **Command**: `team/role/mine`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "team/role/mine",
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
    "cmd": "team/role/mine",
    "rid": -1
  },
  "data": "{
    \"role\": \"MEMBER\"
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết GetMemberRoleResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `role` | MemberRole | Vai trò của user trong team hiện tại |

#### Các giá trị MemberRole

| Giá trị | Mô tả |
|---------|-------|
| `MEMBER` | Thành viên thường - không có quyền quản lý |
| `COLEADER` | Đồng đội trưởng - có quyền quản lý một số chức năng |
| `LEADER` | Đội trưởng - có toàn quyền quản lý team |

## Lỗi có thể gặp

### Lỗi logic nghiệp vụ

#### 1. Không có team (errCode: 3)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 3,
  "errMsg": "User is not in any team"
}
```

### Lỗi hệ thống

#### 2. Lỗi lấy thông tin role (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to get member role"
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
