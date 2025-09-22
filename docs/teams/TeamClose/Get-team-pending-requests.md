# WebSocket Get Team Pending Requests Handler

## Mô tả
Handler `GET_TEAM_PENDING_REQUESTS` cho phép leader hoặc co-leader của team lấy danh sách tất cả các yêu cầu tham gia team đang trong trạng thái chờ xử lý (PENDING). Đây là công cụ quan trọng để quản lý thành viên mới.

## Thông tin Handler
- **Command**: `teams/join_request`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket
- **Quyền hạn**: Chỉ leader hoặc co-leader có thể sử dụng

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/join_request",
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
| `teamId` | String | Có |  ID của team cần lấy danh sách yêu cầu | ID đã được encode |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/join_request",
    "rid": -1
  },
  "data": "[
    {
      {
      \"requestId\": \"encoded_request_id_1\",
      \"teamId\": \"encoded_team_id\",
      \"userId\": \"userId_1\",
      \"status\": \"PENDING\",
    },
    ...
    {
      \"requestId\": \"encoded_request_id_2\",
      \"teamId\": \"encoded_team_id\",
      \"userId\": \"userId_2\",
      \"status\": \"PENDING\",
    }
  ]",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết TeamJoinRequest Object

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `requestId` | String | ID nội bộ của yêu cầu (deprecated) |
| `teamId` | String | ID nội bộ của team (deprecated) |
| `userId` | String | Thông tin chi tiết của user gửi yêu cầu |
| `status` | JoinRequestStatus | Trạng thái yêu cầu (luôn là PENDING) |

## Lỗi có thể gặp

### Lỗi validation dữ liệu đầu vào

#### 1. Thiếu dữ liệu team (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Invalid request data"
}
```

#### 2. Thiếu team ID (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing team ID"
}
```

#### 3. Team ID không hợp lệ (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Invalid team ID format"
}
```

### Lỗi quyền hạn và trạng thái

#### 4. Không phải leader/co-leader (errCode: 4)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 4,
  "errMsg": "Only team leader or co-leader can view join requests"
}
```

#### 5. Team không tồn tại (errCode: 3)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 3,
  "errMsg": "Team not found"
}
```

#### 6. Không thuộc team này (errCode: 4)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 4,
  "errMsg": "User is not member of this team"
}
```

### Lỗi hệ thống

#### 7. Lỗi truy vấn database (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to fetch team join requests"
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

#### 9. Lỗi chung không xác định (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Unexpected error: Unknown error"
}
```

## Lưu ý quan trọng

1. **Quyền hạn**: Chỉ leader hoặc co-leader của team mới có thể xem yêu cầu tham gia
2. **Chỉ PENDING**: Chỉ trả về các yêu cầu có trạng thái PENDING
3. **Sắp xếp**: Yêu cầu được sắp xếp theo thời gian tạo (mới nhất cuối - reversed)
4. **Real-time updates**: Có thể sử dụng với notification để cập nhật realtime

