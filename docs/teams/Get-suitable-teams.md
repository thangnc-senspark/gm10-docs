# WebSocket Get Suitable Teams Handler

## Mô tả
Handler `GET_SUITABLE_TEAMS` cho phép người chơi lấy danh sách các team phù hợp người chơi. Khác với suggestion teams, suitable teams tập trung vào các team phù hợp với người chơi và có thể join.

## Thông tin Handler
- **Command**: `teams/get_suitable_teams`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/get_suitable_teams",
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
    "cmd": "teams/get_suitable_teams",
    "rid": -1
  },
  "data": "[
    {
      \"teamData\": {
        \"hashTeamId\": \"encoded_team_id_1\",
        \"teamName\": \"Skill Masters\",
        \"avatar\": \"master_icon\",
        \"description\": \"High-skill competitive team\",
        \"teamType\": \"CLOSED\",
        \"minimumScoreRequired\": 1500,
        \"createdAt\": 1673625600000,
        \"lastModifiedAt\": 1673625600000
      },
      \"memberCount\": 15,
      \"teamScore\": 60000,
      \"activity\": \"HIGH\"
    },
    {
      \"teamData\": {
        \"hashTeamId\": \"encoded_team_id_2\",
        \"teamName\": \"Progress Together\",
        \"avatar\": \"growth_icon\",
        \"description\": \"Team focused on improvement\",
        \"teamType\": \"OPEN\",
        \"minimumScoreRequired\": 1200,
        \"createdAt\": 1673626600000,
        \"lastModifiedAt\": 1673626600000
      },
      \"memberCount\": 10,
      \"teamScore\": 42000,
      \"activity\": \"MEDIUM\"
    }
  ]",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết Response

Response trả về một mảng các `TeamProfile` objects.

## Lỗi có thể gặp

### Lỗi hệ thống

#### 1. Lỗi lấy team phù hợp (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to get suitable teams"
}
```

#### 2. Lỗi xác thực/phiên (errCode: 7)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 7,
  "errMsg": "Invalid session"
}
```


## Thuật toán Suitable Teams



## Lưu ý quan trọng

1. **Xác thực**: Handler yêu cầu người dùng phải đăng nhập hợp lệ
2. **Qualification focused**: Tập trung vào các team mà user đủ điều kiện tham gia
3. **Real-time**: Danh sách được cập nhật theo thời gian thực
4. **Team changes**: Tự động update khi team thay đổi requirements
5. **Empty result**: Có thể không có team nào suitable nếu user score quá thấp/cao
