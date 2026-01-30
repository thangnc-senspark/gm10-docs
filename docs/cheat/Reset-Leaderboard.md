# Reset Leaderboard Cheat API

## Tổng quan

API này trigger reset bảng xếp hạng tuần (weekly leaderboard) một cách thủ công. Thông thường, weekly leaderboard được reset tự động vào cuối tuần (Chủ nhật 23:59:59 UTC).

⚠️ **Lưu ý**: API này chỉ hoạt động trên server test.

## Prerequisites

- User phải đăng nhập thành công (authenticated)
- Server phải ở chế độ test (`isServerTest = true`)

## Endpoint

**WebSocket Command:** `cheat/reset_leaderboard`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "cheat/reset_leaderboard"
  },
  "data": null
}
```

### Parameters

Không có parameters cần thiết.

## Response

### Success Response

```json
{
  "rid": {
    "id": 123,
    "cmd": "cheat/reset_leaderboard"
  },
  "data": null,
  "errCode": 0,
  "errMsg": null
}
```

### Response Fields

| Field | Type | Mô tả |
|-------|------|-------|
| `errCode` | integer | 0 nếu thành công |
| `data` | null | Không có dữ liệu trả về |


## Lưu ý

- Reset leaderboard sẽ ảnh hưởng đến tất cả players
- Nên sử dụng cẩn thận ngay cả trên server test
