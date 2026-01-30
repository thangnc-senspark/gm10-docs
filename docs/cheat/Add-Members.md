# Add Members Cheat API

## Tổng quan

API này thêm nhiều bot members vào một team có sẵn. Mỗi bot member sẽ được tự động tạo với dữ liệu fake và join vào team.

⚠️ **Lưu ý**: API này chỉ hoạt động trên server test.

## Prerequisites

- User phải đăng nhập thành công (authenticated)
- Server phải ở chế độ test (`isServerTest = true`)
- Team phải tồn tại

## Endpoint

**WebSocket Command:** `cheat/add_members`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "cheat/add_members"
  },
  "data": "{\"n\":5,\"teamId\":\"abc123\"}"
}
```

### Request Data Schema
```json
{
  "n": "integer",       // Required - Số lượng bot members cần thêm (1-50)
  "teamId": "string"    // Required - ID team đã mã hóa (HashId)
}
```

### Parameters

| Field | Type | Required | Range | Mô tả |
|-------|------|----------|-------|-------|
| `n` | integer | ✅ | 1-50 | Số lượng bot members cần thêm vào team |
| `teamId` | string | ✅ | - | Team ID đã được mã hóa bằng HashId |

## Response

### Success Response

```json
{
  "rid": {
    "id": 123,
    "cmd": "cheat/add_members"
  },
  "data": "{\"success\":true,\"teamId\":\"abc123\",\"teamName\":\"Dream Team\",\"membersAdded\":5,\"bots\":[{\"botName\":\"Bot 1234\"},{\"botName\":\"Bot 5678\"},{\"botName\":\"Bot 9012\"},{\"botName\":\"Bot 3456\"},{\"botName\":\"Bot 7890\"}]}",
  "errCode": 0,
  "errMsg": null
}
```

### Response Data Schema
```json
{
  "success": "boolean",       // true nếu thêm thành công
  "teamId": "string",         // ID team đã mã hóa
  "teamName": "string",       // Tên team
  "membersAdded": "integer",  // Số members đã thêm thành công
  "bots": [                   // Danh sách bots đã tạo
    {
      "botName": "string"     // Tên của bot
    }
  ]
}
```

### Response Fields

| Field | Type | Mô tả |
|-------|------|-------|
| `success` | boolean | Trạng thái thêm members |
| `teamId` | string | Team ID đã được mã hóa |
| `teamName` | string | Tên team |
| `membersAdded` | integer | Số bot đã join thành công (có thể < n nếu có bot join thất bại) |
| `bots` | array | Danh sách thông tin các bot đã tạo |
| `bots[].botName` | string | Tên của bot (format: "Bot XXXX") |

## Bot Generation Details

Với mỗi bot member, hệ thống sẽ:

1. **Tạo bot account** với:
   - Tên: "Bot XXXX" (XXXX là số ngẫu nhiên 4 chữ số)
   - UniqueId: "bot_member_{timestamp}"

2. **Tạo dữ liệu game fake** bao gồm:
   - Điểm cao nhất: 5000 - 200000
   - Level cao nhất: 20 - 100
   - Avatar ngẫu nhiên
   - Frame ngẫu nhiên
   - Region: VN hoặc US

3. **Thêm vào weekly leaderboard** với điểm tương ứng

4. **Join vào team**

