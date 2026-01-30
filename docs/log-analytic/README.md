# Log Analytics WebSocket API Documentation

## Tổng quan

Hệ thống Log Analytics của GM10 Server cung cấp các WebSocket API để:
- Đăng ký và quản lý devices gửi log
- Gửi và nhận log events real-time
- Theo dõi hoạt động của devices
- Lấy dữ liệu log cho analytics

## Kiến trúc hệ thống

```
Client Device ←→ WebSocket ←→ GM10 Server ←→ Log Event Manager
                                      ↓
                              Log Analytics Dashboard
```

## API Commands


### 1. Log Event APIs

#### [2.1 Send Log Event (WebSocket)](Send-Log.md)
**Command:** `log/event`

Gửi log event từ device đã đăng ký qua WebSocket connection.

#### [2.2 Send Log Event (HTTP)](Send-Log-Event-HTTP.md)
**Endpoint:** `POST /api/log/send-event`

Gửi log event trực tiếp qua HTTP API - phù hợp cho mobile apps và batch logging.

#### 2.3 Get Log Data
**Command:** `log/get_data`

Lấy tất cả dữ liệu log hiện có.

### 3. Real-time Monitoring APIs

#### 3.1 Register for Log Broadcasting
**Command:** `log/register_show_log`

Đăng ký để nhận real-time notifications về log events và device changes.

#### 3.2 Real-time Notifications

Sau khi đăng ký `log/register_show_log`, client sẽ nhận được các push notifications:

## Device Lifecycle

1. **Registration**: Device kết nối và đăng ký với server
2. **Active**: Device gửi log events, server cập nhật `lastActivityAt`
3. **Inactive**: Device ngừng gửi events
4. **Cleanup**: Sau 1 giờ không hoạt động, device bị xóa tự động
