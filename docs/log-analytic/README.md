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

### 1. Device Registration APIs

#### [1.1 Register Device](./Register-Device.md)
**Command:** `log/register_device`

Đăng ký device để có thể gửi log events.


#### 1.2 Get Registered Devices
**Command:** `log/get_devices`

Lấy danh sách tất cả devices đã đăng ký.

### 2. Log Event APIs

#### [2.1 Send Log Event](Send-Log.md)
**Command:** `log/event`

Gửi log event từ device đã đăng ký.

#### 2.2 Get Log Data
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
