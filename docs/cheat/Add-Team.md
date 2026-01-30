# Add Team Cheat API

## Tổng quan

API này tạo một team mới với bot làm leader. Bot leader sẽ được tự động tạo với dữ liệu fake.

⚠️ **Lưu ý**: API này chỉ hoạt động trên server test.

## Prerequisites

- User phải đăng nhập thành công (authenticated)
- Server phải ở chế độ test (`isServerTest = true`)

## Endpoint

**WebSocket Command:** `cheat/add_team`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "cheat/add_team"
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
    "cmd": "cheat/add_team"
  },
  "data": "{\"success\":true,\"botLeaderName\":\"Bot leader 1234\",\"botLeaderId\":100,\"teamId\":\"abc123\",\"teamName\":\"Nova 34\"}",
  "errCode": 0,
  "errMsg": null
}
```

### Response Data Schema
```json
{
  "success": "boolean",       // true nếu tạo thành công
  "botLeaderName": "string",  // Tên của bot leader
  "botLeaderId": "integer",   // UID của bot leader
  "teamId": "string",         // ID team đã mã hóa (HashId)
  "teamName": "string"        // Tên team được generate ngẫu nhiên
}
```

### Response Fields

| Field | Type | Mô tả |
|-------|------|-------|
| `success` | boolean | Trạng thái tạo team |
| `botLeaderName` | string | Tên bot leader (format: "Bot leader XXXX") |
| `botLeaderId` | integer | UID của bot leader trong database |
| `teamId` | string | Team ID đã được mã hóa bằng HashId |
| `teamName` | string | Tên team ngẫu nhiên (format: "[TênNgẫuNhiên] XX") |

## Team Generation Details

Khi tạo team, hệ thống sẽ:

1. **Tạo bot leader** với:
   - Tên: "Bot leader XXXX" (XXXX là số ngẫu nhiên 4 chữ số)
   - UniqueId: "bot_leader_{timestamp}"
   - Dữ liệu game fake (điểm, level, avatar, frame...)

2. **Tạo team** với:
   - Tên team ngẫu nhiên từ danh sách: Nova, Rift, Apex, Vex, StormX, ShadowZ, BlazeUp, IronFox, Night10, FuryX, ZenPro, SkyNet, Volt, TitanX, Hexa, Ghosty, Nebula, Lynx, Raptor, Omega
   - Badge ngẫu nhiên
   - Team type: OPEN
   - Minimum score required: ngẫu nhiên (0 - 7000)
