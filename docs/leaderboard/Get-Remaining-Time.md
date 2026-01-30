# Get Leaderboard Remaining Time API

## Tổng quan

API này cho phép lấy thông tin về thời gian còn lại đến khi reset weekly leaderboard, cùng với thông tin về tuần hiện tại.

## Prerequisites

- User phải đăng nhập thành công (authenticated)
- WebSocket connection phải đang active

## Endpoint

**WebSocket Command:** `leaderboard/get_remaining_time`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_remaining_time"
  },
  "data": null
}
```

### Request Data Schema
Không cần truyền data (hoặc data = null).

## Response

### Success Response
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_remaining_time"
  },
  "data": "{\"remainingTimeMs\":432000000,\"weekStartDate\":1704672000000,\"weekEndDate\":1705276800000}",
  "errCode": 0,
  "errMsg": null
}
```

### Response Data Schema
```json
{
  "remainingTimeMs": "long",    // Thời gian còn lại đến khi reset (milliseconds)
  "weekStartDate": "long",      // Timestamp bắt đầu tuần hiện tại (Monday 00:00:00 UTC)
  "weekEndDate": "long"         // Timestamp kết thúc tuần hiện tại (Sunday 23:59:59 UTC + 7 days)
}
```

### Field Descriptions

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `remainingTimeMs` | long | Số milliseconds còn lại đến khi reset tuần mới | 432000000 (5 days) |
| `weekStartDate` | long | Epoch timestamp (ms) của thứ 2 00:00:00 UTC tuần này | 1704672000000 |
| `weekEndDate` | long | Epoch timestamp (ms) của chủ nhật 23:59:59 UTC + 7 days | 1705276800000 |

## Business Logic

### Week Calculation
- Tuần luôn bắt đầu vào **thứ 2 00:00:00 UTC**
- Tuần kết thúc vào **chủ nhật 23:59:59 UTC** (sau 7 ngày)
- Sử dụng timezone **UTC** cho tất cả tính toán

## Notes

- Client tự convert sang local timezone khi hiển thị cho user
- Chỉ nên gọi lúc khỏi đầu không nên gọi quá thường xuyên (polling mỗi giây không cần thiết)
- Client tự cache và tính toán countdown local.
