# WebSocket Reset Request Collection Cooldown Handler

## Mô tả
Handler `RESET_REQUEST_COLLECTION_COOLDOWN` reset cooldown của Request Collection cho user hiện tại, cho phép user gửi một Collection Request mới ngay lập tức mà không cần chờ hết 24 giờ.

⚠️ **API này chỉ hoạt động trên server test.**

## Thông tin Handler
- **Command**: `cheat/reset_request_collection_cooldown`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data
```json
{
  "rid": {
    "cmd": "cheat/reset_request_collection_cooldown",
    "rid": -1
  },
  "data": null
}
```

## Response Data

Handler này **không trả về response**. Không có lỗi phản hồi — nếu user không có request nào thì không có gì xảy ra.

## Cách hoạt động

Handler dịch chuyển ngược `createAt` của request gần nhất về **24 giờ trước** trong cache, khiến server coi như cooldown đã kết thúc. Sau đó user có thể gửi Collection Request mới ngay lập tức.

## Ghi chú
- Chỉ dùng để **kiểm thử (testing)** — không có trên production.
- Thay đổi chỉ ảnh hưởng đến cache server, không ghi vào database.
