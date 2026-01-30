# Claim Rewards API

## Tổng quan

API này cho phép user claim (nhận) phần thưởng từ bảng xếp hạng tuần trước nếu họ đủ điều kiện.

## Prerequisites

- User phải đăng nhập thành công (authenticated)
- WebSocket connection phải đang active
- User phải có `has_reward = true` trong tuần trước
- User chưa claim phần thưởng (`claimed_reward = false`)

## Endpoint

**WebSocket Command:** `leaderboard/claim_rewards`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/claim_rewards"
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
    "cmd": "leaderboard/claim_rewards"
  },
  "data": null,
  "errCode": 0,
  "errMsg": null
}
```

### Error Response - Not Found
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/claim_rewards"
  },
  "data": null,
  "errCode": 1,
  "errMsg": "Not found"
}
```

## Business Logic

### Claim Conditions
User có thể claim phần thưởng khi:
1. ✅ Có record trong `player_weekly_scores` với `session_id = currentSession - 1`
2. ✅ `has_reward = true` (thuộc top 3 tuần trước)
3. ✅ `claimed_reward = false` (chưa claim)

### Claim Process
Khi claim thành công:
1. Đánh dấu `claimed_reward = true` trong database
2. Trả về `errCode = 0` (NO_ERROR)
3. Client có thể trao phần thưởng cho user (gems, coins, items, etc.)


## Notes

- Phần thưởng chỉ có thể claim **một lần duy nhất**
- Sau khi claim, không thể claim lại dù cho có reset game
- Phần thưởng có thời hạn: Chỉ claim được trong tuần mới sau khi tuần trước kết thúc
- Server chỉ đánh dấu claim status, việc trao thưởng thực tế do client xử lý

