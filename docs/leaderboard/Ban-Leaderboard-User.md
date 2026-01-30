# Ban Leaderboard User API

## Tổng quan

API này cho phép ban một user khỏi bảng xếp hạng. User bị ban sẽ không xuất hiện trên leaderboard.

## Prerequisites

- User phải đăng nhập thành công (authenticated)

## Endpoint

**WebSocket Command:** `leaderboard/ban_user`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/ban_user"
  },
  "data": "{\"userId\":\"abc123\"}"
}
```

### Request Data Schema
```json
{
  "userId": "string"    // Required - ID user đã mã hóa (HashId)
}
```

### Parameters

| Field | Type | Required | Mô tả |
|-------|------|----------|-------|
| `userId` | string | ✅ | ID của user cần ban, đã được mã hóa bằng HashId |

## Response

### Success Response

```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/ban_user"
  },
  "data": null,
  "errCode": 0,
  "errMsg": null
}
```

### Failed Response

```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/ban_user"
  },
  "data": null,
  "errCode": 1,
  "errMsg": ""
}
```

### Response Fields

| Field | Type | Mô tả |
|-------|------|-------|
| `errCode` | integer | 0 nếu ban thành công, khác 0 nếu thất bại |
| `data` | null | Không có dữ liệu trả về |

## Hiệu ứng của việc ban

Khi user bị ban:

1. **Leaderboard cache** sẽ được invalidate và refresh
2. User sẽ **không xuất hiện** trên tất cả các loại leaderboard:
   - Weekly Player Leaderboard
   - All-time Player Leaderboard
3. User vẫn có thể chơi game bình thường, chỉ không hiển thị trên leaderboard

## Liên quan

- [Is Leaderboard Banned](Is-Leaderboard-Banned.md) - Kiểm tra trạng thái ban của user
- [Remove Leaderboard Ban](Remove-Leaderboard-Ban.md) - Gỡ ban user khỏi leaderboard
