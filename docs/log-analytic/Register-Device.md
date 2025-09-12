# Register Device API

## Tổng quan

API này cho phép client devices đăng ký với server để có thể gửi log events. Device đã đăng ký sẽ được quản lý bởi hệ thống và có thể gửi log events real-time.

## Endpoint

**WebSocket Command:** `log/register_device`

## Request

### Format
```json
{
  "rid": {
    "id": 123,
    "cmd": "log/register_device"
  },
  "data": "{\"deviceName\":\"iPhone-13-Pro\"}"
}
```

### Request Data Schema
```json
{
  "deviceName": "string" // Tên device, không được để trống
}
```

### Parameters

| Field | Type | Required | Mô tả |
|-------|------|----------|-------|
| `deviceName` | string | ✅ | Tên định danh của device. Sẽ được sử dụng làm `deviceId` |

### Validation Rules

- `deviceName` không được null, empty hoặc chỉ chứa whitespace
- Device name nên sử dụng format có ý nghĩa (ví dụ: "iPhone-13-Pro", "Android-Galaxy-S21")

## Response

### Success Response
```json
{
  "rid": {
    "id": 123,
    "cmd": "log/register_device"
  },
  "data": "{\"success\":true,\"deviceId\":\"iPhone-13-Pro\"}",
  "errCode": 0,
  "errMsg": null
}
```

### Response Data Schema
```json
{
  "success": true,
  "deviceId": "string" // ID được assign cho device
}
```