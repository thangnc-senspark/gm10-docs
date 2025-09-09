# WebSocket Get My Team Handler

## Mô tả
Handler `GET_MY_TEAM` cho phép người chơi lấy thông tin chi tiết về team hiện tại mà họ đang tham gia.

## Thông tin Handler
- **Command**: `teams/mine`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/mine",
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
    "cmd": "teams/mine",
    "rid": -1
  },
  "data": "{
    \"teamData\": {
      \"hashTeamId\": \"encoded_team_id\",
      \"teamName\": \"My Awesome Team\",
      \"avatar\": \"team_icon\",
      \"description\": \"Best team in the game!\",
      \"teamType\": \"OPEN\",
      \"minimumScoreRequired\": 500,
      \"createdAt\": 1673625600000,
      \"lastModifiedAt\": 1673625600000
    },
    \"memberCount\": 12,
    \"teamScore\": 25000,
    \"activity\": \"HIGH\"
  }",
  "errCode": 0,
  "errMsg": null
}
```
Response data trả về `TeamProfile` objects.

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

#### 2. Lỗi lấy thông tin team (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to get team information"
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
2. **Membership**: Chỉ trả về thông tin nếu user đang là thành viên của team
3. **Real-time**: Thông tin được cập nhật real-time, bao gồm số lượng thành viên và điểm team
