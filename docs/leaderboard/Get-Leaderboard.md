# Get Leaderboard API

## Tổng quan

API này cho phép lấy dữ liệu bảng xếp hạng theo 3 loại:
- **WEEKLY_PLAYER**: Xếp hạng người chơi theo điểm tuần (current_week_score - last_week_score)
- **ALL_PLAYER**: Xếp hạng tất cả người chơi theo điểm cao nhất (highest_score)
- **ALL_TEAM**: Xếp hạng đội nhóm theo tổng điểm của các thành viên

## Prerequisites

- User phải đăng nhập thành công (authenticated)

## Endpoint

**WebSocket Command:** `leaderboard/get_leaderboard`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_leaderboard"
  },
  "data": "{\"rankingType\":\"WEEKLY_PLAYER\",\"pageSize\":50,\"pageNumber\":1,\"regionCode\":\"VN\"}"
}
```

### Request Data Schema
```json
{
  "rankingType": "string",  // Required - "WEEKLY_PLAYER", "ALL_PLAYER", hoặc "ALL_TEAM"
  "pageSize": "integer",    // Optional - Số entries mỗi trang (default: 50, max: 100)
  "pageNumber": "integer",  // Optional - Số trang bắt đầu từ 1 (default: 1)
  "regionCode": "string"    // Optional - Mã quốc gia để filter (e.g., "VN", "US")
}
```

### Parameters

| Field | Type | Required | Default | Mô tả |
|-------|------|----------|---------|-------|
| `rankingType` | string | ✅ | - | Loại bảng xếp hạng: "WEEKLY_PLAYER", "ALL_PLAYER", "ALL_TEAM" |
| `pageSize` | integer | ❌ | 50 | Số lượng entries mỗi trang (min: 1, max: 100) |
| `pageNumber` | integer | ❌ | 1 | Số trang (bắt đầu từ 1) |
| `regionCode` | string | ❌ | null | Mã quốc gia để filter kết quả (e.g., "VN", "US", "JP") |

## Response

### Success Response - Player Leaderboard (WEEKLY_PLAYER hoặc ALL_PLAYER)

```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_leaderboard"
  },
  "data": "{\"leaderboardType\":\"WEEKLY_PLAYER\",\"entries\":[{\"rank\":1,\"playerId\":\"abc123\",\"playerName\":\"PlayerOne\",\"playerNation\":\"VN\",\"playerScore\":5000,\"playerAvatar\":\"avatar_01\",\"playerFrame\":\"frame_gold\",\"teamId\":\"team123\",\"teamName\":\"Dream Team\"},{\"rank\":2,\"playerId\":\"def456\",\"playerName\":\"PlayerTwo\",\"playerNation\":\"US\",\"playerScore\":4500,\"playerAvatar\":\"avatar_02\",\"playerFrame\":\"frame_silver\",\"teamId\":\"\",\"teamName\":\"\"}],\"myEntry\":{\"rank\":15,\"playerId\":\"xyz789\",\"playerName\":\"Me\",\"playerNation\":\"VN\",\"playerScore\":3200,\"playerAvatar\":\"avatar_03\",\"playerFrame\":\"frame_bronze\",\"teamId\":\"team123\",\"teamName\":\"Dream Team\"},\"weekStartDate\":1704672000000,\"weekEndDate\":1705276799999}",
  "errCode": 0,
  "errMsg": null
}
```

### Player Leaderboard Response Schema
```json
{
  "leaderboardType": "string",        // "WEEKLY_PLAYER" hoặc "ALL_PLAYER"
  "entries": [                        // Danh sách top players
    {
      "rank": "integer",              // Thứ hạng (1-based)
      "playerId": "string",           // ID đã mã hóa của player
      "playerName": "string",         // Tên người chơi
      "playerNation": "string",       // Mã quốc gia (e.g., "VN", "US")
      "playerScore": "integer",       // Điểm số (weekly score hoặc highest score)
      "playerAvatar": "string",       // Avatar của player
      "playerFrame": "string",        // Khung ảnh đại diện
      "teamId": "string",             // ID team đã mã hóa (empty nếu không có team)
      "teamName": "string"            // Tên team (empty nếu không có team)
    }
  ],
  "myEntry": {                        // Thông tin của user hiện tại (null nếu không có)
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
  "weekStartDate": "long",            // Timestamp bắt đầu tuần (chỉ có với WEEKLY_PLAYER)
  "weekEndDate": "long"               // Timestamp kết thúc tuần (chỉ có với WEEKLY_PLAYER)
}
```

### Success Response - Team Leaderboard (ALL_TEAM)

```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_leaderboard"
  },
  "data": "{\"leaderboardType\":\"ALL_TEAM\",\"entries\":[{\"rank\":1,\"teamData\":{\"teamId\":100,\"teamName\":\"Alpha Team\",\"teamAvatar\":\"badge_01\",\"teamDescription\":\"Best team\",\"teamRegion\":\"VN\",\"teamTag\":\"ALPHA\"},\"memberCount\":15,\"teamScore\":250000,\"activity\":\"ACTIVE\"}]}",
  "errCode": 0,
  "errMsg": null
}
```

### Team Leaderboard Response Schema
```json
{
  "leaderboardType": "string",        // "ALL_TEAM"
  "entries": [                        // Danh sách top teams
    {
      "rank": "integer",              // Thứ hạng (1-based)
      "teamData": {
        "teamId": "integer",          // ID của team
        "teamName": "string",         // Tên team
        "teamAvatar": "string",       // Badge/avatar của team
        "teamDescription": "string",  // Mô tả team
        "teamRegion": "string",       // Khu vực của team
        "teamTag": "string"           // Tag của team
      },
      "memberCount": "integer",       // Số lượng thành viên
      "teamScore": "long",            // Tổng điểm của team
      "activity": "string"            // Trạng thái hoạt động: "ACTIVE", "INACTIVE"
    }
  ]
}
```

## Business Logic

### WEEKLY_PLAYER Leaderboard
- Điểm được tính theo công thức: **current_week_score - last_week_score**
- Chỉ tính điểm của tuần hiện tại (từ thứ 2 00:00:00 UTC)
- Reset mỗi tuần vào Chủ Nhật 23:59:59 UTC
- Top 3 người chơi có điểm cao nhất sẽ được đánh dấu có phần thưởng

### ALL_PLAYER Leaderboard
- Điểm được tính theo **highest_score** từ bảng `persistent_game_data`
- Không có reset định kỳ
- Là bảng xếp hạng vĩnh viễn của tất cả người chơi

### ALL_TEAM Leaderboard
- Điểm team = **Tổng highest_score của tất cả thành viên**
- Cập nhật realtime khi thành viên thay đổi hoặc điểm thành viên thay đổi
- Chỉ tính điểm của các thành viên hiện tại (không tính thành viên đã rời team)

### Region Filter
- Khi `regionCode` được truyền vào, chỉ hiển thị players/teams từ quốc gia đó
- Sử dụng field `country_code` từ bảng `persistent_game_data`
- Nếu `regionCode = null`, hiển thị global leaderboard

### Pagination
- `offset = (pageNumber - 1) * pageSize`
- Ví dụ: pageNumber=1, pageSize=50 → offset=0, lấy 50 entries đầu tiên
- Ví dụ: pageNumber=2, pageSize=50 → offset=50, lấy 50 entries tiếp theo

