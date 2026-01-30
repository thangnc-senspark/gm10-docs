# Delete User Data Cheat API

## Tổng quan

API này xóa hoàn toàn tất cả dữ liệu của user hiện tại, bao gồm game data, teams, leaderboards, và các dữ liệu liên quan khác.

⚠️ **Cảnh báo**: Hành động này không thể hoàn tác. Tất cả dữ liệu sẽ bị xóa vĩnh viễn.

## Prerequisites

- User phải đăng nhập thành công (authenticated)

## Endpoint

**WebSocket Command:** `cheat/delete_user_data`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "cheat/delete_user_data"
  },
  "data": null
}
```

### Parameters

Không có parameters cần thiết. API sẽ tự động xóa dữ liệu của user đang đăng nhập.

## Response

### Success Response

```json
{
  "rid": {
    "id": 123,
    "cmd": "cheat/delete_user_data"
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

- API này thường được sử dụng để reset tài khoản trong quá trình testing
- Sau khi xóa, user vẫn có thể đăng nhập lại và sẽ được tạo dữ liệu mới
- Account cơ bản (uid, uniqueId) có thể vẫn được giữ lại tùy thuộc vào implementation
