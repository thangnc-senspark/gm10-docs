# Is Leaderboard Banned API

## Tổng quan

API này cho phép kiểm tra xem một user có đang bị ban khỏi bảng xếp hạng hay không.

## Prerequisites

- User phải đăng nhập thành công (authenticated)

## Endpoint

**WebSocket Command:** `leaderboard/is_banned`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/is_banned"
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
| `userId` | string | ✅ | ID của user cần kiểm tra, đã được mã hóa bằng HashId |

## Response

### Success Response - User bị ban

```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/is_banned"
  },
  "data": "{\"userId\":\"abc123\",\"isBanned\":true}",
  "errCode": 0,
  "errMsg": null
}
```

### Success Response - User không bị ban

```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/is_banned"
  },
  "data": "{\"userId\":\"abc123\",\"isBanned\":false}",
  "errCode": 0,
  "errMsg": null
}
```

### Response Data Schema
```json
{
  "userId": "string",     // ID user đã mã hóa (giống request)
  "isBanned": "boolean"   // true nếu user bị ban, false nếu không
}
```

## Caching

Kết quả kiểm tra ban được cache trong 30 phút để tối ưu performance. Cache sẽ được cập nhật tự động khi:
- User bị ban mới
- User được gỡ ban

## Liên quan

- [Ban Leaderboard User](Ban-Leaderboard-User.md) - Ban user khỏi leaderboard
- [Remove Leaderboard Ban](Remove-Leaderboard-Ban.md) - Gỡ ban user khỏi leaderboard
