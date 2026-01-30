# Send Log Event HTTP API

## Tổng quan

API HTTP này cho phép client devices gửi log events trực tiếp đến server thông qua HTTP request. Đây là phương thức thay thế cho WebSocket API, phù hợp cho các ứng dụng không cần kết nối real-time.

## Endpoint

**HTTP Method:** `POST`  
**URL:** `/api/log/send-event`  
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

### Validation Rules

- `deviceId` không được null, empty hoặc chỉ chứa whitespace
- `eventName` không được null, empty hoặc chỉ chứa whitespace
- `parameters` có thể là object rỗng hoặc không cần truyền

### Example Request

```bash
curl -X POST http://localhost:8080/api/log/send-event \
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

### Response Schema
```json
{
  "success": "boolean",                     // Trạng thái thành công
  "message": "string"                       // Thông báo kết quả
}
```

## Error Responses

### 1. Validation Error (HTTP 400)
Khi dữ liệu đầu vào không hợp lệ:

```json
{
  "error": "Device ID cannot be empty",
  "errorCode": "400"
}
```

**Các lỗi validation phổ biến:**
- `"Request body is required"` - Không có request body
- `"Device ID cannot be empty"` - deviceId trống
- `"Event name cannot be empty"` - eventName trống
- `"Invalid JSON format: <details>"` - Lỗi định dạng JSON

### 2. Server Error (HTTP 400)
Khi server không thể xử lý log event:

```json
{
  "error": "Failed to send log event",
  "errorCode": "1"
}
```

### 3. Internal Server Error (HTTP 500)
Khi có lỗi hệ thống nghiêm trọng - response sẽ không có body.

## Tích hợp với WebSocket System

Log events được gửi qua HTTP API này sẽ:

1. **Lưu trữ**: Được lưu trong hệ thống log event manager
2. **Broadcasting**: Tự động broadcast đến các WebSocket clients đang lắng nghe
3. **Analytics**: Có thể được truy xuất qua các WebSocket API khác để analytics

## So sánh với WebSocket API

| Aspect | HTTP API | WebSocket API |
|--------|----------|---------------|
| **Connection** | Stateless, mỗi request độc lập | Persistent connection |
| **Real-time** | Không | Có |
| **Overhead** | Cao hơn (HTTP headers) | Thấp hơn |
| **Complexity** | Đơn giản | Phức tạp hơn |
| **Use Case** | Batch logging, mobile apps | Real-time monitoring, dashboards |

## Best Practices

1. **Batch Requests**: Đối với mobile apps, nên batch nhiều events trong một request để tiết kiệm battery
2. **Error Handling**: Luôn kiểm tra response status và xử lý retry logic
3. **Device ID**: Sử dụng consistent device ID xuyên suốt session
4. **Parameters**: Chỉ gửi các parameters cần thiết để giảm payload size

## Example Usage

### JavaScript/Frontend
```javascript
async function sendLogEvent(deviceId, eventName, parameters = {}) {
  try {
    const response = await fetch('/api/log/send-event', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        deviceId,
        eventName,
        parameters
      })
    });
    
    if (response.ok) {
      const result = await response.json();
      console.log('Log event sent:', result);
    } else {
      const error = await response.json();
      console.error('Failed to send log event:', error);
    }
  } catch (error) {
    console.error('Network error:', error);
  }
}

// Usage
sendLogEvent('web-client-123', 'page_view', {
  page: '/dashboard',
  referrer: document.referrer
});
```

### Python
```python
import requests
import json

def send_log_event(device_id, event_name, parameters=None):
    if parameters is None:
        parameters = {}
    
    url = 'http://localhost:8080/api/log/send-event'
    payload = {
        'deviceId': device_id,
        'eventName': event_name,
        'parameters': parameters
    }
    
    try:
        response = requests.post(url, json=payload)
        if response.status_code == 200:
            result = response.json()
            print(f"Log event sent: {result}")
        else:
            error = response.json()
            print(f"Failed to send log event: {error}")
    except requests.RequestException as e:
        print(f"Network error: {e}")

# Usage
send_log_event('python-client-456', 'user_login', {
    'user_id': '12345',
    'login_method': 'email'
})
```
