# WebSocket Search Team Handler

## Mô tả
Handler `SEARCH_TEAM` cho phép người chơi tìm kiếm các nhóm (team) theo tên hoặc từ khóa. Kết quả trả về danh sách các team phù hợp.

## Thông tin Handler
- **Command**: `teams/search_by_text`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/search_by_text",
    "rid": -1
  },
  "data": "{
    \"searchText\": \"string\"
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `searchText` | String | Có | Từ khóa tìm kiếm | Tối thiểu 1 ký tự, không được để trống |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/search_by_text",
    "rid": -1
  },
  "data": "[
    {
      \"teamData\": {
        \"hashTeamId\": \"encoded_team_id\",
        \"teamName\": \"Team Alpha\",
        \"avatar\": \"alpha_icon\",
        \"description\": \"Professional mining team\",
        \"teamType\": \"OPEN\",
        \"minimumScoreRequired\": 1000,
        \"createdAt\": 1673625600000,
        \"lastModifiedAt\": 1673625600000
      },
      \"memberCount\": 15,
      \"teamScore\": 50000,
      \"activity\": \"HIGH\"
    }
  ]",
  "errCode": 0,
  "errMsg": null
}
```
Response trả về một mảng các `TeamProfile` objects.

## Lỗi có thể gặp

### Lỗi validation dữ liệu đầu vào

#### 1. Thiếu tham số tìm kiếm (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing search parameters"
}
```

### Lỗi hệ thống

#### 2. Lỗi tìm kiếm (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to search teams"
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
2. **Tìm kiếm**: Hỗ trợ tìm kiếm theo tên team
