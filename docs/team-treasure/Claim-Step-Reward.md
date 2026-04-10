# WebSocket Claim Treasure Reward Handler

## Mô tả
Handler `CLAIM_TREASURE_REWARD` cho phép người chơi nhận phần thưởng trong sự kiện Team Treasure. Có hai chế độ hoạt động:

- **Claim một step cụ thể**: Truyền `eventId` + `step` (1, 2, hoặc 3) để nhận phần thưởng của mốc đó.
- **Claim tất cả cùng lúc**: Truyền chỉ `eventId` (không truyền `step`) để nhận toàn bộ phần thưởng còn lại (bao gồm cả 3 step và bonus top player).

Mỗi step và bonus chỉ có thể claim một lần.

## Thông tin Handler
- **Command**: `team_treasure/claim_reward`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON — Claim một step cụ thể
```json
{
  "rid": {
    "cmd": "team_treasure/claim_reward",
    "id": 1
  },
  "data": "{\"eventId\": 42, \"step\": 1}"
}
```

### Cấu trúc JSON — Claim tất cả (step 1–3 + bonus)
```json
{
  "rid": {
    "cmd": "team_treasure/claim_reward",
    "id": 1
  },
  "data": "{\"eventId\": 42}"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `eventId` | Int | **Có** | ID sự kiện cần claim phần thưởng | Phải là ID sự kiện hợp lệ |
| `step` | Int | Không | Step muốn claim (1, 2, hoặc 3). Nếu bỏ qua → claim tất cả. | 1, 2, hoặc 3 |

### Step Thresholds

| Step | Ngưỡng | Mô tả |
|------|--------|-------|
| 1 | 60% of `totalShipWheels` | Step đầu tiên |
| 2 | 90% of `totalShipWheels` | Step trung gian |
| 3 | 180% of `totalShipWheels` | Step cuối (vượt mục tiêu) |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "team_treasure/claim_reward",
    "id": 1
  },
  "data": "{\"rewards\": {\"Ruby\": 100, \"Gold\": 5000}}",
  "errCode": 0,
  "errMsg": null
}
```

Nếu không có phần thưởng nào được nhận (đã claim hết, chưa đạt ngưỡng, ...):
```json
{
  "rid": {...},
  "data": "{\"rewards\": {}}",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết Response Fields

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `rewards` | Map\<String, Int\> | Map các loại resource và số lượng nhận được. Trống nếu không có gì để claim. |

## Lỗi có thể gặp

### 1. Thiếu dữ liệu request (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing data"
}
```

### 2. Sự kiện không tồn tại (errCode: 5)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 5,
  "errMsg": "Event not found"
}
```

### 3. Lỗi xác thực/phiên (errCode: 7)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 7,
  "errMsg": "Invalid session"
}
```

## Lưu ý quan trọng

1. **`eventId` bắt buộc**: Khác với các handler khác, bạn phải luôn truyền `eventId` — server không tự tìm sự kiện active của team.
2. **`step` tuỳ chọn**: Nếu bỏ qua `step`, server gọi `claimAllRewards` để nhận toàn bộ phần thưởng còn lại (step 1, 2, 3 và bonus) trong một lần.
3. **Participant có đóng góp**: Để claim step, người chơi phải có ít nhất 1 ship wheel (`shipWheelsEarned >= 1`).
4. **Claim một lần**: Mỗi step và bonus chỉ có thể claim một lần duy nhất. Các phần thưởng đã claim trước đó sẽ bị bỏ qua (không lỗi).
5. **Team progress**: Ngưỡng step được tính theo `teamCurrentWheels` (tổng cả team), không phải cá nhân.
6. **Bonus reward**: Chỉ dành cho top 3 contributors (theo `shipWheelsEarned`). Được bao gồm tự động khi dùng chế độ claim-all (không truyền `step`).
7. **Không kiểm tra trạng thái sự kiện**: Rewards có thể được claim cho cả sự kiện `ACTIVE` lẫn `ENDED`.
8. **Thứ tự claim**: Có thể claim step 2 trước step 1 nếu team đã đạt ngưỡng (không bắt buộc claim theo thứ tự).
9. **Rewards config**: Phần thưởng của từng step được cấu hình trong `stepRewards`; phần thưởng top bonus trong `topBonusRewards` (lấy từ response `team_treasure/info`).

## Điều kiện để claim step thành công

Tất cả các điều kiện sau phải thỏa mãn:

1. ✅ User đã login
2. ✅ `eventId` tồn tại trong DB
3. ✅ User là participant của sự kiện đó
4. ✅ User đã kiếm được ít nhất 1 ship wheel (`shipWheelsEarned >= 1`)
5. ✅ Step chưa được claim (`stepXClaimed = false`)
6. ✅ Team đã đạt ngưỡng của step (`teamCurrentWheels >= threshold`)
7. ✅ Step hợp lệ (1, 2, hoặc 3)
