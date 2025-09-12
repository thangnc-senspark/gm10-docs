# Send Log Event HTTP API

## Tổng quan

API HTTP này cho phép client devices gửi log events trực tiếp đến server thông qua HTTP request.
## Endpoint

**HTTP Method:** `POST`  
**URL:** `{base_api}/log/send-event`  
**Content-Type:** `application/json`

## Request

### Request Body Schema
```json
{
  "deviceId": "string",                     // ID của device, không được để trống
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
| `deviceId` | string | ✅ | ID định danh của device gửi log. Ví dụ: "iPhone-13-Pro", "Android-Galaxy-S21" |
| `eventName` | string | ✅ | Tên event cần log. Ví dụ: "screen_view", "button_click", "user_action" |
| `parameters` | object | ❌ | Key-value pairs chứa thông tin bổ sung của event |

### Example Request

```bash
curl -X POST http://localhost:9000/gm10/log/send-event \
  -H "Content-Type: application/json" \
  -d '{
    "deviceId": "iPhone-13-Pro",
    "eventName": "screen_view",
    "parameters": {
      "screen_name": "home",
      "user_id": "12345",
      "timestamp": "2025-09-12T10:30:00Z"
    }
  }'
```

## Response

### Success Response (HTTP 200)
```json
{
  "success": true,
  "message": "Log event sent successfully"
}
```