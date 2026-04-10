# WebSocket Get Team Treasure Info Handler

## Mô tả
Handler `GET_TEAM_TREASURE_INFO` cho phép người chơi lấy thông tin chi tiết về sự kiện Team Treasure hiện tại của team mình, bao gồm trạng thái sự kiện, tiến độ, các mốc phần thưởng, và bảng xếp hạng của đội.

## Thông tin Handler
- **Command**: `team_treasure/info`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON

**Không truyền `eventId`** (lấy sự kiện active/gần nhất của team):
```json
{
  "rid": {
    "cmd": "team_treasure/info",
    "rid": -1
  },
  "data": null
}
```

**Truyền `eventId`** (lấy thông tin sự kiện cụ thể theo ID):
```json
{
  "rid": {
    "cmd": "team_treasure/info",
    "rid": 1
  },
  "data": "{\"eventId\": 42}"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| `eventId` | Int | Không | ID sự kiện cụ thể cần lấy thông tin. Nếu bỏ qua hoặc `null`, server sẽ trả về sự kiện ACTIVE (hoặc ENDED gần nhất) của team người chơi. |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "team_treasure/info",
    "rid": -1
  },
  "data": "{
    \"eventId\": 42,
    \"status\": \"ACTIVE\",
    \"startTime\": 1709500000000,
    \"endTime\": 1709586400000,
    \"totalShipWheels\": 1000,
    \"teamCurrentWheels\": 750,
    \"stepThresholds\": {
      \"step1\": 600,
      \"step2\": 900,
      \"step3\": 1800
    },
    \"step1Claimed\": true,
    \"step2Claimed\": false,
    \"step3Claimed\": false,
    \"bonusClaimed\": false,
    \"topPlayers\": [
      {
        \"memberId\": \"abc123\",
        \"shipWheelsEarned\": 250,
        \"rank\": 1
      },
      {
        \"memberId\": \"def456\",
        \"shipWheelsEarned\": 200,
        \"rank\": 2
      },
      {
        \"memberId\": \"ghi789\",
        \"shipWheelsEarned\": 150,
        \"rank\": 3
      }
    ],
    \"stepRewards\": {
      \"step1\": {\"Ruby\": 100},
      \"step2\": {\"Ruby\": 200},
      \"step3\": {\"Ruby\": 500}
    },
    \"topBonusRewards\": {
      \"top1\": {\"Diamond\": 50},
      \"top2\": {\"Diamond\": 30},
      \"top3\": {\"Diamond\": 20}
    }
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết Response Fields

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `eventId` | Int | ID của sự kiện Team Treasure |
| `status` | TeamTreasureStatus | Trạng thái sự kiện: `ACTIVE`, `ENDED`, hoặc `CANCELLED` |
| `startTime` | Long | Timestamp bắt đầu sự kiện (milliseconds) |
| `endTime` | Long | Timestamp kết thúc sự kiện (milliseconds) |
| `totalShipWheels` | Int | Tổng số ship wheels mục tiêu (100% = totalShipWheels) |
| `teamCurrentWheels` | Int | Số ship wheels hiện tại mà team đã thu thập được |
| `stepThresholds` | StepThresholds | Ngưỡng ship wheels cần đạt để unlock từng step |
| `stepThresholds.step1` | Int | Ngưỡng step 1 (60% of totalShipWheels) |
| `stepThresholds.step2` | Int | Ngưỡng step 2 (90% of totalShipWheels) |
| `stepThresholds.step3` | Int | Ngưỡng step 3 (180% of totalShipWheels) |
| `step1Claimed` | Boolean | Người chơi đã claim phần thưởng step 1 chưa |
| `step2Claimed` | Boolean | Người chơi đã claim phần thưởng step 2 chưa |
| `step3Claimed` | Boolean | Người chơi đã claim phần thưởng step 3 chưa |
| `bonusClaimed` | Boolean | Người chơi đã claim phần thưởng top bonus chưa |
| `topPlayers` | List\<TreasureLeaderboardEntry\> | Danh sách top contributors trong team |
| `topPlayers[].memberId` | String | Hash ID của thành viên |
| `topPlayers[].shipWheelsEarned` | Int | Số ship wheels thành viên đã kiếm được |
| `topPlayers[].rank` | Int | Thứ hạng của thành viên trong team (1-based) |
| `stepRewards` | StepRewards | Cấu hình phần thưởng cho các step |
| `topBonusRewards` | TopBonusRewards | Cấu hình phần thưởng cho top 1/2/3 |

### Response khi không có sự kiện active hoặc `eventId` không hợp lệ
```json
{
  "rid": {
    "cmd": "team_treasure/info",
    "id": 1
  },
  "data": null,
  "errCode": 0,
  "errMsg": null
}
```

## Lỗi có thể gặp

### 1. Người dùng không thuộc team nào (errCode: 5)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 5,
  "errMsg": "User not in a team"
}
```

### 2. Lỗi xác thực/phiên (errCode: 7)
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
3. **`eventId` tuỳ chọn**: Nếu không truyền (hoặc `null`), server trả về sự kiện ACTIVE của team; nếu không có sự kiện ACTIVE, server trả về sự kiện ENDED gần nhất. Nếu truyền `eventId`, server lấy trực tiếp sự kiện đó — hữu ích cho admin/debug hoặc xem lại kết quả sự kiện đã kết thúc.
4. **Sự kiện không tồn tại**: Nếu `eventId` được truyền nhưng không tìm thấy sự kiện tương ứng (hoặc người chơi không tham gia sự kiện đó), `data` sẽ trả về `null` nhưng `errCode` vẫn là `0` (success)
5. **Leaderboard**: `topPlayers` chứa tất cả participants được sắp xếp theo số ship wheels kiếm được (giảm dần)
6. **Step thresholds**: 
   - Step 1: 60% of `totalShipWheels`
   - Step 2: 90% of `totalShipWheels`
   - Step 3: 180% of `totalShipWheels` (vượt mục tiêu)
7. **Claimed flags**: Các flag `stepXClaimed` và `bonusClaimed` chỉ áp dụng cho người chơi hiện tại, không phải toàn team
8. **Bonus reward**: Chỉ có thể claim sau khi sự kiện kết thúc (`status: ENDED`) và chỉ dành cho top 3 contributors

