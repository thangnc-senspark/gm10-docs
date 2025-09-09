# WebSocket Get Suggestion Teams Handler

## Mô tả
Handler `GET_SUGGESTION_TEAMS` cho phép người chơi lấy danh sách các team được gợi ý dựa trên thuật toán recommendation của hệ thống. Các team được gợi ý thường là team hoạt động sôi nổi.

## Thông tin Handler
- **Command**: `teams/get_suggest_teams`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/get_suggest_teams",
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
    "cmd": "teams/get_suggest_teams",
    "rid": -1
  },
  "data": "[
    {
      \"teamData\": {
        \"hashTeamId\": \"encoded_team_id_1\",
        \"teamName\": \"Rising Stars\",
        \"avatar\": \"star_icon\",
        \"description\": \"Team for ambitious players\",
        \"teamType\": \"OPEN\",
        \"minimumScoreRequired\": 800,
        \"createdAt\": 1673625600000,
        \"lastModifiedAt\": 1673625600000
      },
      \"memberCount\": 12,
      \"teamScore\": 35000,
      \"activity\": \"HIGH\"
    },
    ...
    {
      \"teamData\": {
        \"hashTeamId\": \"encoded_team_id_2\",
        \"teamName\": \"Casual Miners\",
        \"avatar\": \"pickaxe_icon\",
        \"description\": \"Relaxed team for casual play\",
        \"teamType\": \"OPEN\",
        \"minimumScoreRequired\": 500,
        \"createdAt\": 1673626600000,
        \"lastModifiedAt\": 1673626600000
      },
      \"memberCount\": 8,
      \"teamScore\": 18000,
      \"activity\": \"MEDIUM\"
    }
  ]",
  "errCode": 0,
  "errMsg": null
}
```

Response trả về một mảng các `TeamProfile` objects.

## Lỗi có thể gặp

### Lỗi hệ thống

#### 1. Lỗi lấy gợi ý team (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to get team suggestions"
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
## Thuật toán Gợi ý


## Lưu ý quan trọng

1. **Xác thực**: Handler yêu cầu người dùng phải đăng nhập hợp lệ
2. **Real-time**: Danh sách được cập nhật theo thời gian thực
3. **Limit**: Thường giới hạn 50 team để tránh overwhelm
4. **Refresh frequency**: Danh sách gợi ý được refresh định kỳ
