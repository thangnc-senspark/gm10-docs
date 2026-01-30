# Get Last Week Leaderboard API

## Tổng quan

API này cho phép lấy dữ liệu bảng xếp hạng của tuần trước, hữu ích để xem lại thành tích và thứ hạng của người chơi trong tuần vừa qua.

## Prerequisites

- User phải đăng nhập thành công (authenticated)
- WebSocket connection phải đang active

## Endpoint

**WebSocket Command:** `leaderboard/get_last_week_leaderboard`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_last_week_leaderboard"
  },
  "data": "{\"pageSize\":50,\"pageNumber\":1,\"regionCode\":\"VN\"}"
}
```

### Request Data Schema
```json
{
  "pageSize": "integer",    // Optional - Số entries mỗi trang (default: 50, max: 100)
  "pageNumber": "integer",  // Optional - Số trang bắt đầu từ 1 (default: 1)
  "regionCode": "string"    // Optional - Mã quốc gia để filter (e.g., "VN", "US")
}
```

### Parameters

| Field | Type | Required | Default | Mô tả |
|-------|------|----------|---------|-------|
| `pageSize` | integer | ❌ | 50 | Số lượng entries mỗi trang (min: 1, max: 100) |
| `pageNumber` | integer | ❌ | 1 | Số trang (bắt đầu từ 1) |
| `regionCode` | string | ❌ | null | Mã quốc gia để filter kết quả |

## Response

### Success Response
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_last_week_leaderboard"
  },
  "data": "{\"leaderboardType\":\"WEEKLY_PLAYER\",\"entries\":[{\"rank\":1,\"playerId\":\"abc123\",\"playerName\":\"PlayerOne\",\"playerNation\":\"VN\",\"playerScore\":6500,\"playerAvatar\":\"avatar_01\",\"playerFrame\":\"frame_gold\",\"teamId\":\"team123\",\"teamName\":\"Dream Team\"},{\"rank\":2,\"playerId\":\"def456\",\"playerName\":\"PlayerTwo\",\"playerNation\":\"US\",\"playerScore\":6200,\"playerAvatar\":\"avatar_02\",\"playerFrame\":\"frame_silver\",\"teamId\":\"\",\"teamName\":\"\"}],\"myEntry\":{\"rank\":5,\"playerId\":\"xyz789\",\"playerName\":\"Me\",\"playerNation\":\"VN\",\"playerScore\":5800,\"playerAvatar\":\"avatar_03\",\"playerFrame\":\"frame_bronze\",\"teamId\":\"team123\",\"teamName\":\"Dream Team\"},\"weekStartDate\":1704067200000,\"weekEndDate\":1704671999999}",
  "errCode": 0,
  "errMsg": null
}
```

### Response Data Schema
```json
{
  "leaderboardType": "string",        // Luôn là "WEEKLY_PLAYER"
  "entries": [                        // Danh sách top players tuần trước
    {
      "rank": "integer",              // Thứ hạng (1-based)
      "playerId": "string",           // ID đã mã hóa của player
      "playerName": "string",         // Tên người chơi
      "playerNation": "string",       // Mã quốc gia
      "playerScore": "integer",       // Điểm số tuần trước
      "playerAvatar": "string",       // Avatar của player
      "playerFrame": "string",        // Khung ảnh đại diện
      "teamId": "string",             // ID team đã mã hóa
      "teamName": "string"            // Tên team
    }
  ],
  "myEntry": {                        // Thông tin của user hiện tại trong tuần trước (null nếu không có)
    "rank": "integer",
    "playerId": "string",
    "playerName": "string",
    "playerNation": "string",
    "playerScore": "integer",
    "playerAvatar": "string",
    "playerFrame": "string",
    "teamId": "string",
    "teamName": "string"
  },
  "weekStartDate": "long",            // Timestamp bắt đầu tuần trước
  "weekEndDate": "long"               // Timestamp kết thúc tuần trước
}
```

## Notes

- API này chỉ hỗ trợ WEEKLY_PLAYER leaderboard (không có ALL_PLAYER hoặc ALL_TEAM)
- Dữ liệu tuần trước là read-only và không thay đổi
- Hữu ích để xem lại thành tích và kiểm tra xem có đủ điều kiện nhận thưởng không
- Thời gian (weekStartDate, weekEndDate) luôn tính theo UTC
