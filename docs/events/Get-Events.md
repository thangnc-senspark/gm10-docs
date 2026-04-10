# WebSocket Get Events Handler

## Mô tả
Handler `GET_EVENTS` trả về danh sách tất cả các sự kiện đang active tại thời điểm hiện tại, kèm theo thời gian bắt đầu và kết thúc của từng sự kiện.

Kết quả là một map với key là tên loại sự kiện (ví dụ: `"TEAM_TREASURE"`) và value là thông tin thời gian cùng dữ liệu tiền xử lý (nếu có).

## Thông tin Handler
- **Command**: `events/get`
- **Yêu cầu xác thực**: Có
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "events/get",
    "id": 1
  },
  "data": null
}
```

### Chi tiết các trường dữ liệu
Handler này **không cần dữ liệu đầu vào**.

## Response Data

### Thành công — có sự kiện đang active (errCode: 0)
```json
{
  "rid": {
    "cmd": "events/get",
    "id": 1
  },
  "data": "{\"TEAM_TREASURE\":{\"startTime\":1744300800000,\"endTime\":1744387200000}}",
  "errCode": 0,
  "errMsg": null
}
```

### Thành công — không có sự kiện nào đang active (errCode: 0)
```json
{
  "rid": {
    "cmd": "events/get",
    "id": 1
  },
  "data": "{}",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết Response Fields

Response `data` là một JSON object (map) với:

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| *(key)* | String | Tên loại sự kiện (ví dụ: `"TEAM_TREASURE"`) |
| *(value)* | ActiveEventInfo | Thông tin sự kiện đang active |

#### Cấu trúc `ActiveEventInfo`

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `startTime` | Long | Timestamp bắt đầu sự kiện (milliseconds) |
| `endTime` | Long | Timestamp kết thúc sự kiện (milliseconds) |
| `preData` | String? | Dữ liệu bổ sung phụ thuộc vào loại sự kiện (tuỳ chọn, có thể `null`) |

#### Các loại sự kiện hiện tại

| Key | Mô tả |
|-----|-------|
| `TEAM_TREASURE` | Sự kiện Team Treasure (Ship Wheel) đang trong khung giờ active |

## Lưu ý quan trọng

1. **Kết quả rỗng**: Nếu không có sự kiện nào đang active, `data` trả về chuỗi JSON của một object rỗng (`"{}"`), **không phải** `null`.
2. **Cơ chế tổng hợp**: `EventsManager` tự động thu thập trạng thái từ tất cả các manager sự kiện (hiện tại: `ITeamTreasureManager.getActiveWindow()`). Khi có thêm loại sự kiện mới, chúng sẽ tự động xuất hiện trong response này.
3. **`preData`**: Hiện tại chưa được dùng bởi sự kiện nào (`null`); dành cho việc truyền thêm metadata cho client trong tương lai.

