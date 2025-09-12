# Send Log Event API

## Tổng quan

API này cho phép devices đã đăng ký gửi log events real-time đến server. Events sẽ được lưu trữ và broadcast đến các analytics clients đang lắng nghe.

## Prerequisites

- Device đã phải được đăng ký thành công qua `log/register_device`
- WebSocket connection phải đang active

## Endpoint

**WebSocket Command:** `log/event`

## Request

### Format
```json
{
  "rid": {
    "id": 124,
    "cmd": "log/event"
  },
  "data": "{\"eventName\":\"screen_view\",\"parameters\":{\"screen_name\":\"home\",\"user_id\":\"12345\"}}"
}
```

### Request Data Schema
```json
{
  "eventName": "string",                    // Tên event, không được để trống
  "parameters": {                           // Optional parameters
    "key1": "value1",
    "key2": "value2"
  }
}
```

### Parameters

| Field | Type | Required | Mô tả |
|-------|------|----------|-------|
| `eventName` | string | ✅ | Tên event (ví dụ: "screen_view", "button_click") |
| `parameters` | object | ❌ | Key-value pairs chứa thông tin bổ sung của event |

## Response

**Đặc biệt:** API này **KHÔNG trả về response**.
