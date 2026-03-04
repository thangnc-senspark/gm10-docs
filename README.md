# GM10 Server API Documentation

## Tổng quan

Tài liệu API cho GM10 Server - Game backend sử dụng WebSocket cho real-time communication.

## Công nghệ

- **Backend**: Kotlin với Ktor framework
- **Communication**: WebSocket
- **Database**: PostgreSQL
- **Authentication**: Token-based authentication

## Cấu trúc tài liệu

```
docs/
├── accounts/             # APIs quản lý tài khoản
├── cheat/                # APIs cho testing (chỉ server test)
├── leaderboard/          # APIs bảng xếp hạng
├── log-analytic/         # APIs logging và analytics
├── request-collection/   # APIs yêu cầu xin vật phẩm collection
└── teams/                # APIs quản lý team
```

## Danh sách APIs

### 📊 Accounts

| API                                                         | Command                | Mô tả                      |
|-------------------------------------------------------------|------------------------|----------------------------|
| [Get Account Profile](docs/accounts/Get-Account-Profile.md) | `accounts/get_profile` | Lấy thông tin profile user |

### 🏆 Leaderboard

| API                                                                        | Command                          | Mô tả                       |
|----------------------------------------------------------------------------|----------------------------------|-----------------------------|
| [Get Leaderboard](docs/leaderboard/Get-Leaderboard.md)                     | `leaderboard/get_leaderboard`    | Lấy bảng xếp hạng           |
| [Get Last Week Leaderboard](docs/leaderboard/Get-Last-Week-Leaderboard.md) | `leaderboard/get_last_week`      | Lấy BXH tuần trước          |
| [Get Remaining Time](docs/leaderboard/Get-Remaining-Time.md)               | `leaderboard/get_remaining_time` | Thời gian còn lại đến reset |
| [Get Rewards](docs/leaderboard/Get-Rewards.md)                             | `leaderboard/get_rewards`        | Kiểm tra phần thưởng        |
| [Claim Rewards](docs/leaderboard/Claim-Rewards.md)                         | `leaderboard/claim_rewards`      | Nhận phần thưởng tuần       |
| [Ban Leaderboard User](docs/leaderboard/Ban-Leaderboard-User.md)           | `leaderboard/ban_user`           | Ban user khỏi BXH           |
| [Is Leaderboard Banned](docs/leaderboard/Is-Leaderboard-Banned.md)         | `leaderboard/is_banned`          | Kiểm tra trạng thái ban     |
| [Remove Leaderboard Ban](docs/leaderboard/Remove-Leaderboard-Ban.md)       | `leaderboard/remove_ban`         | Gỡ ban user                 |

### 👥 Teams

| API                                                        | Command                 | Mô tả                       |
|------------------------------------------------------------|-------------------------|-----------------------------|
| [Create Team](docs/teams/Create-team.md)                   | `teams/create`          | Tạo team mới                |
| [Get My Team](docs/teams/Get-my-team.md)                   | `teams/get_my_team`     | Lấy thông tin team của mình |
| [Get My Role](docs/teams/Get-my-role.md)                   | `teams/get_my_role`     | Lấy role trong team         |
| [Get Team Profile](docs/teams/Get-team-profile.md)         | `teams/get_profile`     | Lấy profile team            |
| [Get Team Members](docs/teams/Get-team-members.md)         | `teams/get_members`     | Lấy danh sách members       |
| [Join Team](docs/teams/Join-team.md)                       | `teams/join`            | Tham gia team               |
| [Leave Team](docs/teams/Leave-team.md)                     | `teams/leave`           | Rời khỏi team               |
| [Kick Member](docs/teams/Kick-member.md)                   | `teams/kick`            | Kick member khỏi team       |
| [Update Team](docs/teams/Update-team.md)                   | `teams/update`          | Cập nhật thông tin team     |
| [Update Member Role](docs/teams/Update-member-role.md)     | `teams/update_role`     | Cập nhật role member        |
| [Search Team](docs/teams/Search-team.md)                   | `teams/search`          | Tìm kiếm team               |
| [Get Suitable Teams](docs/teams/Get-suitable-teams.md)     | `teams/get_suitable`    | Lấy teams phù hợp           |
| [Get Suggestion Teams](docs/teams/Get-suggestion-teams.md) | `teams/get_suggestions` | Lấy gợi ý teams             |
| [Team Schemas](docs/teams/Team-schemas.md)                 | -                       | Data schemas cho teams      |

#### Team Close

| API                                                                            | Command                         | Mô tả                                   |
|--------------------------------------------------------------------------------|---------------------------------|-----------------------------------------|
| [Get Team Pending Requests](docs/teams/TeamClose/Get-team-pending-requests.md) | `teams/get_pending_requests`    | Lấy danh sách yêu cầu tham gia team     |
| [Get My Pending Requests](docs/teams/TeamClose/Get-my-pending-requests.md)     | `teams/get_my_pending_requests` | Lấy danh sách yêu cầu đang chờ của mình |
| [Accept Join Request](docs/teams/TeamClose/Accept-join-requests.md)            | `teams/accept_request`          | Chấp nhận yêu cầu tham gia              |
| [Reject Join Request](docs/teams/TeamClose/Reject-join-request.md)             | `teams/reject_request`          | Từ chối yêu cầu tham gia                |
| [Cancel Join Request](docs/teams/TeamClose/Cancel-join-request.md)             | `teams/cancel_request`          | Hủy yêu cầu tham gia                    |

### 📈 Log & Analytics

| API                                                             | Command               | Mô tả                     |
|-----------------------------------------------------------------|-----------------------|---------------------------|
| [Register Device](docs/log-analytic/Register-Device.md)         | `log/register_device` | Đăng ký device            |
| [Send Log](docs/log-analytic/Send-Log.md)                       | `log/send`            | Gửi log event (WebSocket) |
| [Send Log Event HTTP](docs/log-analytic/Send-Log-Event-HTTP.md) | `POST /log`           | Gửi log event (HTTP)      |

### 📦 Request Collection

| API                                                                               | Command                               | Mô tả                                        |
|-----------------------------------------------------------------------------------|---------------------------------------|----------------------------------------------|
| [Send Request Collection](docs/request-collection/Send-Request-Collection.md)     | `chat/send_request_collection`        | Gửi yêu cầu xin vật phẩm collection          |
| [Get Time For Next Request](docs/request-collection/Get-Time-For-Next-Request.md) | `chat/helps/time_for_next_collection` | Thời gian cooldown trước khi gửi request mới |
| [Get List Helpers](docs/request-collection/Get-List-Helpers.md)                   | `chat/helps/user_help_collections`    | Lấy danh sách người đã giúp cho các request  |
| [Help Collection Request](docs/request-collection/Help-Collection-Request.md)     | `chat/helps/help_collection_request`  | Giúp một Collection Request                  |

### 🔧 Cheat (Testing Only)

⚠️ **Các API này chỉ hoạt động trên server test**

| API                                                                                  | Command                                   | Mô tả                                      |
|--------------------------------------------------------------------------------------|-------------------------------------------|--------------------------------------------|
| [Add Team](docs/cheat/Add-Team.md)                                                   | `cheat/add_team`                          | Tạo team với bot leader                    |
| [Add Members](docs/cheat/Add-Members.md)                                             | `cheat/add_members`                       | Thêm bot members vào team                  |
| [Delete User Data](docs/cheat/Delete-User-Data.md)                                   | `cheat/delete_user_data`                  | Xóa dữ liệu user                           |
| [Reset Leaderboard](docs/cheat/Reset-Leaderboard.md)                                 | `cheat/reset_leaderboard`                 | Reset BXH tuần                             |
| [Reset Request Collection Cooldown](docs/cheat/Reset-Request-Collection-Cooldown.md) | `cheat/reset_request_collection_cooldown` | Reset cooldown Collection Request của user |

## WebSocket Protocol

### Request Format

```json
{
  "rid": {
    "id": 123,
    "cmd": "command/path"
  },
  "data": "{\"key\":\"value\"}"
}
```

### Response Format

```json
{
  "rid": {
    "id": 123,
    "cmd": "command/path"
  },
  "data": "{\"result\":\"value\"}",
  "errCode": 0,
  "errMsg": null
}
```

### Error Codes

| Code | Mô tả                                               |
|------|-----------------------------------------------------|
| 0    | Thành công (NO_ERROR)                               |
| 1    | Lỗi chung (GENERAL_ERROR)                           |
| 2    | Dữ liệu request không hợp lệ (INVALID_REQUEST_DATA) |
| 3    | Không tìm thấy (NOT_FOUND)                          |
| 4    | Command không hợp lệ (INVALID_REQUEST_CMD)          |
| 5    | Lỗi đăng nhập (LOGIN_ERROR)                         |
| 6    | Lỗi đăng ký (REGISTER_ERROR)                        |
| 7    | Session không hợp lệ (INVALID_SESSION)              |
| 8    | Không tìm thấy thông tin user (USER_INFO_NOT_FOUND) |
| 9    | Không thể vào phòng (CANNOT_JOIN_ROOM)              |
| 10   | Lỗi lấy chat (GET_CHAT)                             |
| 11   | Lỗi tạo chat room (ERROR_CREATE_CHAT_ROOM)          |
| 12   | Lỗi thêm tin nhắn (ERROR_ADD_CHAT_MESSAGE)          |
| 13   | Đã đạt giới hạn (MAX_CAPACITY)                      |
| 14   | Request đã tồn tại (EXISTING_REQUEST)               |
| 15   | Không có quyền thực hiện (PERMISSION_DENIED)        |
| 16   | Chưa xác thực (UNAUTHORIZED)                        |
| 17   | Bị cấm truy cập (FORBIDDEN)                         |

## Authentication

Hầu hết các API yêu cầu user phải đăng nhập (authenticated). Quá trình xác thực:

1. Client kết nối WebSocket
2. Client gửi request login với credentials
3. Server trả về token
4. Các request tiếp theo sẽ được xác thực qua session

## Broadcast (Server Push)

Server có thể push messages đến clients mà không cần request. Các broadcast messages có `rid.id < 0` để phân biệt với
response thông thường.

### Broadcast Format

```json
{
  "rid": {
    "id": -1,
    "cmd": "broadcast/command"
  },
  "data": "{\"event\":\"data\"}",
  "errCode": 0,
  "errMsg": null
}
```

### Broadcast Commands

| Command                 | Mô tả                                        |
|-------------------------|----------------------------------------------|
| `chat/broadcast`        | Batch broadcast nhiều messages cùng lúc      |
| `chat/new`              | Tin nhắn mới trong chat room                 |
| `chat/update`           | Tin nhắn được cập nhật                       |
| `chat/join_team`        | Có người join vào team chat room             |
| `join_request/new`      | Yêu cầu tham gia team mới (cho admin/leader) |
| `join_request/update`   | Cập nhật trạng thái yêu cầu tham gia         |
| `log/event`             | Log event mới (cho analytics dashboard)      |
| `log/register_device`   | Device mới được đăng ký                      |
| `log/unregister_device` | Device bị hủy đăng ký                        |

### Chat Broadcast Data Schema

#### New Message (`chat/new`)

#### Join Request (`join_request/new`)

### Batch Broadcast (`chat/broadcast`)

Server sử dụng batch broadcast để tối ưu performance. Thay vì gửi từng message riêng lẻ, server gom nhiều messages lại
và gửi mỗi 1 giây.

```json
{
  "rid": {
    "id": -1,
    "cmd": "chat/broadcast"
  },
  "data": "[{\"rid\":{\"id\":-1,\"cmd\":\"chat/new\"},\"data\":\"{...}\",\"errCode\":0},{\"rid\":{\"id\":-1,\"cmd\":\"chat/new\"},\"data\":\"{...}\",\"errCode\":0}]",
  "errCode": 0
}
```

Client cần parse `data` như một array và xử lý từng message trong đó.


