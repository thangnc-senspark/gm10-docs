
# Get Account Profile API

## Tổng quan

API này cho phép lấy thông tin profile của một tài khoản người dùng, bao gồm dữ liệu cá nhân (tên, avatar, level, điểm), thống kê collection, số lần giúp đỡ và thông tin team.

## Prerequisites

- User phải đăng nhập thành công (authenticated)
- WebSocket connection phải đang active

## Endpoint

**WebSocket Command:** `account/get_profile`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "account/get_profile"
  },
  "data": "{\"userId\":\"abc123xyz\"}"
}
```

### Request Data Schema
```json
{
  "userId": "string"  // Optional - ID đã mã hóa của user cần xem profile
}
```

### Parameters

| Field | Type | Required | Mô tả |
|-------|------|----------|-------|
| `userId` | string | ❌ | ID đã mã hóa của user cần xem profile. Nếu không truyền, sẽ lấy profile của chính user đang đăng nhập |

### Use Cases

1. **Xem profile của chính mình**: Không truyền `data` hoặc không truyền `userId`
2. **Xem profile của user khác**: Truyền `userId` của user cần xem

## Response

### Success Response
```json
{
  "rid": {
    "id": 123,
    "cmd": "account/get_profile"
  },
  "data": "{\"userData\":{\"playerName\":\"PlayerOne\",\"playerAvatar\":\"avatar_01\",\"playerFrame\":\"frame_gold\",\"level\":25,\"score\":15000,\"collectionCompleted\":3},\"totalHelps\":42,\"teamData\":{\"hashTeamId\":\"xyz789\",\"teamName\":\"Dream Team\",\"avatar\":\"badge_02\",\"description\":\"Best team ever\",\"teamType\":\"OPEN\",\"minimumScoreRequired\":1000}",
  "errCode": 0,
  "errMsg": null
}
```

### Response Data Schema
```json
{
  "userData": {
    "playerName": "string",           // Tên người chơi
    "playerAvatar": "string",         // Avatar của người chơi
    "playerFrame": "string",          // Khung ảnh đại diện
    "level": "integer",               // Level cao nhất đã chơi
    "score": "integer",               // Điểm cao nhất đã đạt được
    "collectionCompleted": "integer"  // Số bộ sưu tập đã hoàn thành (có đủ 4 items)
  },
  "totalHelps": "integer",            // Tổng số lần user đã giúp đỡ người khác
  "teamData": {                       // Thông tin team (null nếu không tham gia team)
    "hashTeamId": "string",           // ID team đã mã hóa
    "teamName": "string",             // Tên team
    "avatar": "string",               // Badge/avatar của team
    "description": "string",          // Mô tả team
    "teamType": "string",             // "OPEN" hoặc "CLOSED"
    "minimumScoreRequired": "integer", // Điểm tối thiểu yêu cầu để tham gia
  }
}
```

### Error Responses

## Notes

- API này cho phép xem profile của bất kỳ user nào, miễn là biết `userId` đã mã hóa
- Nếu user không có dữ liệu trong `persistent_game_data`, sẽ throw exception
- TeamData sẽ là `null` nếu user chưa tham gia team nào
- Collection completed chỉ đếm những bộ sưu tập có **đúng 4 items**, không nhiều hơn không ít hơn

