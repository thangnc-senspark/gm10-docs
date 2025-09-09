# WebSocket Join Team Handler

## Mô tả
Handler `JOIN_TEAM` cho phép người chơi tham gia vào một nhóm (team) đã tồn tại trong game. Người dùng cần cung cấp ID của team muốn tham gia và điểm số của mình.

## Thông tin Handler
- **Command**: `teams/join`
- **Yêu cầu xác thực**: Có (BaseAuthHandler)
- **Method**: WebSocket

## Request Data

### Cấu trúc JSON
```json
{
  "rid": {
    "cmd": "teams/join",
    "rid": -1
  },
  "data": "{
    \"teamId\": \"string\",
    \"userScore\": number
  }"
}
```

### Chi tiết các trường dữ liệu

| Trường | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
|--------|------|----------|-------|-----------|
| `teamId` | String | Có | Hash ID của team muốn tham gia | ID đã được encode |
| `userScore` | Int | Có | Điểm số hiện tại của người dùng | Dùng để kiểm tra điều kiện tham gia |

## Response Data

### Thành công (errCode: 0)
```json
{
  "rid": {
    "cmd": "teams/join",
    "rid": -1
  },
  "data": "{
    \"requestStatus\": \"APPROVED\"
  }",
  "errCode": 0,
  "errMsg": null
}
```

### Chi tiết JoinTeamResponse

| Trường | Kiểu | Mô tả |
|--------|------|-------|
| `requestStatus` | JoinRequestStatus | Trạng thái của yêu cầu tham gia team |

#### Các giá trị JoinRequestStatus

| Giá trị | Mô tả |
|---------|-------|
| `APPROVED` | Yêu cầu được chấp nhận, đã tham gia team thành công |
| `PENDING` | Yêu cầu đang chờ leader phê duyệt (team CLOSED) |
| `REJECTED` | Yêu cầu bị từ chối |
| `CANCELLED` | Yêu cầu bị hủy (do các điều kiện không đáp ứng) |
| `EXPIRED` | Yêu cầu đã hết hạn |
| `BANNED` | User bị cấm tham gia team này |

## Lỗi có thể gặp

### Lỗi validation dữ liệu đầu vào

#### 1. Thiếu team ID (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Missing team ID"
}
```

#### 2. Team không tồn tại (errCode: 3)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 3,
  "errMsg": "Team not found"
}
```


#### 3. Điểm số không đủ điều kiện (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "User score does not meet minimum requirements"
}
```

#### 4. Team đã đầy (errCode: 2)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 2,
  "errMsg": "Team is full"
}
```

#### 5. User bị ban khỏi team (errCode: 6)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 6,
  "errMsg": "User is banned from this team"
}
```

### Lỗi hệ thống

#### 7. Lỗi tham gia team (errCode: 1)
```json
{
  "rid": {...},
  "data": null,
  "errCode": 1,
  "errMsg": "Failed to join team"
}
```

#### 8. Lỗi xác thực/phiên (errCode: 7)
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
2. **Điều kiện tham gia**: Điểm số của người dùng phải >= `minimumScoreRequired` của team
3. **Team OPEN**: Tự động tham gia nếu đủ điều kiện (trả về `APPROVED`)
4. **Team CLOSED**: Gửi yêu cầu tham gia (trả về `PENDING`), cần leader approve
5. **Giới hạn thành viên**: Mỗi team có giới hạn số lượng thành viên

## Flow tham gia team

### Team OPEN:
1. Kiểm tra điều kiện (score, not banned, not in team)
2. Tự động thêm vào team
3. Trả về status `APPROVED`

### Team CLOSED:
1. Kiểm tra điều kiện cơ bản
2. Tạo join request với status `PENDING`
3. Gửi notification cho leader
4. Trả về status `PENDING`
5. Leader cần approve/reject request


