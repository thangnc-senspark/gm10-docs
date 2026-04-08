# WebSocket Claim Bonus Reward Handler

## Mô tả
Handler `CLAIM_BONUS_REWARD` cho phép người chơi nhận phần thưởng đặc biệt dành cho **top 3 contributors** trong team khi sự kiện Team Treasure kết thúc. Chỉ những người chơi có thứ hạng (rank) 1, 2, hoặc 3 mới được nhận thưởng này. Mỗi người chỉ có thể claim một lần.

## Thông tin Handler
- **Command**: `team_treasure/claim_bonus`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "team_treasure/claim_bonus",
    "rid": -1
  },
  "data": null
}
```

### Chi tiết các trường dữ liệu
Handler này **không cần dữ liệu đầu vào**.

Server sẽ tự động xác định:
- Sự kiện đã kết thúc của team
- Thứ hạng của người chơi trong team
- Phần thưởng tương ứng với thứ hạng

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "team_treasure/claim_bonus",
    "rid": -1
  },
  "data": "{
    \"rewards\": {
      \"Diamond\": 50,
      \"Gold\": 10000
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

Phần thưởng phụ thuộc vào thứ hạng và config của sự kiện.

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

### 2. Không thể claim bonus reward (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Cannot claim bonus reward"
}
```

Lỗi này xảy ra khi:
- Team không có sự kiện đã kết thúc (`status = ENDED`)
- Người chơi không phải participant của sự kiện
- Người chơi đã claim bonus reward rồi (`bonusClaimed = true`)
- Thứ hạng của người chơi không thuộc top 3 (`rank > 3`)

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

1. **Xác thực**: Handler yêu cầu người dùng phải đăng nhập hợp lệ
2. **Team membership**: Người dùng phải là thành viên của một team
3. **Event status**: Chỉ có thể claim khi sự kiện đã **kết thúc** (`status = ENDED`)
4. **Top 3 only**: Chỉ top 3 contributors (theo `shipWheelsEarned`) mới được nhận thưởng
5. **Claim một lần**: Mỗi người chỉ có thể claim bonus reward một lần duy nhất
6. **Ranking**: Thứ hạng được tính dựa trên `shipWheelsEarned` của participant (sắp xếp giảm dần)
7. **Không giới hạn thời gian**: Sau khi event kết thúc, người chơi có thể claim bất cứ lúc nào (không có deadline)

