# Remove Leaderboard Ban API

## Tổng quan

API này cho phép gỡ ban một user khỏi bảng xếp hạng. Sau khi gỡ ban, user sẽ xuất hiện trở lại trên leaderboard.

## Prerequisites

- User phải đăng nhập thành công (authenticated)

## Endpoint

**WebSocket Command:** `leaderboard/remove_ban`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/remove_ban"
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
| `userId` | string | ✅ | ID của user cần gỡ ban, đã được mã hóa bằng HashId |

## Response

API này hiện tại không trả về response (`null`).

## Liên quan

- [Ban Leaderboard User](Ban-Leaderboard-User.md) - Ban user khỏi leaderboard
- [Is Leaderboard Banned](Is-Leaderboard-Banned.md) - Kiểm tra trạng thái ban của user
