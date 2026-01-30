# Get Rewards API

## Tổng quan

API này cho phép kiểm tra xem user có phần thưởng từ bảng xếp hạng tuần trước hay không, và nếu có thì thứ hạng là bao nhiêu.

## Prerequisites

- User phải đăng nhập thành công (authenticated)

## Endpoint

**WebSocket Command:** `leaderboard/get_rewards`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_rewards"
  },
  "data": null
}
```

### Request Data Schema
Không cần truyền data (hoặc data = null).

## Response

### Success Response - Has Reward
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_rewards"
  },
  "data": "{\"hasReward\":true,\"rank\":2}",
  "errCode": 0,
  "errMsg": null
}
```

### Success Response - No Reward
```json
{
  "rid": {
    "id": 123,
    "cmd": "leaderboard/get_rewards"
  },
  "data": "{\"hasReward\":false,\"rank\":-1}",
  "errCode": 0,
  "errMsg": null
}
```

### Response Data Schema
```json
{
  "hasReward": "boolean",   // true nếu user có phần thưởng, false nếu không
  "rank": "integer"         // Thứ hạng tuần trước (nếu hasReward=true), -1 nếu không có
}
```


