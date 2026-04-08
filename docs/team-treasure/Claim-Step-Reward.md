# WebSocket Claim Step Reward Handler

## Mô tả
Handler `CLAIM_STEP_REWARD` cho phép người chơi nhận phần thưởng khi team đạt được các mốc (step) trong sự kiện Team Treasure. Có 3 step tương ứng với 3 ngưỡng ship wheels khác nhau. Mỗi step chỉ có thể claim một lần.

## Thông tin Handler
- **Command**: `team_treasure/claim_step`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "team_treasure/claim_step",
    "rid": -1
  },
  "data": "{
    \"step\": 1
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `step` | Int | Có | Step muốn claim (1, 2, hoặc 3) | Phải là 1, 2, hoặc 3 |

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
    "cmd": "team_treasure/claim_step",
    "rid": -1
  },
  "data": "{
    \"rewards\": {
      \"Ruby\": 100,
      \"Gold\": 5000
    }
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết Response Fields

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `rewards` | Map<String, Int> | Map các loại resource và số lượng nhận được |


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

### 2. Người dùng không thuộc team nào (errCode: 5)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 5,
  "errMsg": "User not in a team"
}
```

### 3. Team không có sự kiện active (errCode: 5)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 5,
  "errMsg": "No active event for user's team"
}
```

### 4. Không thể claim step reward (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Cannot claim step rewards"
}
```

Lỗi này xảy ra khi:
- Người chơi không phải participant của sự kiện
- Người chơi chưa kiếm được ship wheels nào (`shipWheelsEarned < 1`)
- Step đã được claim rồi (`stepXClaimed = true`)
- Team chưa đạt ngưỡng của step đó (`teamCurrentWheels < threshold`)
- Step không hợp lệ (không phải 1, 2, hoặc 3)

### 5. Lỗi xác thực/phiên (errCode: 7)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 7,
  "errMsg": "Invalid session"
}
```

## Lưu ý quan trọng

1. **Xác thực**: Handler yêu cầu người dùng phải đăng nhập hợp lệ
2. **Team membership**: Người dùng phải là thành viên của một team
3. **Active event**: Team phải có sự kiện Team Treasure đang active
4. **Participant có đóng góp**: Người chơi phải có ít nhất 1 ship wheel (`shipWheelsEarned >= 1`)
5. **Claim một lần**: Mỗi step chỉ có thể claim một lần duy nhất
6. **Team progress**: Ngưỡng được tính dựa trên `teamCurrentWheels` (tổng của cả team), không phải số wheels cá nhân
7. **Thứ tự claim**: Có thể claim step 2 trước step 1 nếu team đã đạt ngưỡng (không bắt buộc claim theo thứ tự)
8. **Rewards config**: Phần thưởng của mỗi step được cấu hình trong config của sự kiện.

## Điều kiện để claim thành công

Tất cả các điều kiện sau phải thỏa mãn:

1. ✅ User đã login và thuộc một team
2. ✅ Team có sự kiện active (`status = ACTIVE`)
3. ✅ User là participant của sự kiện
4. ✅ User đã kiếm được ít nhất 1 ship wheel (`shipWheelsEarned >= 1`)
5. ✅ Step chưa được claim (`stepXClaimed = false`)
6. ✅ Team đã đạt ngưỡng của step (`teamCurrentWheels >= threshold`)
7. ✅ Step hợp lệ (1, 2, hoặc 3)

