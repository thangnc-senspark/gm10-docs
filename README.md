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
├── accounts/          # APIs quản lý tài khoản
├── cheat/             # APIs cho testing (chỉ server test)
├── leaderboard/       # APIs bảng xếp hạng
├── log-analytic/      # APIs logging và analytics
└── teams/             # APIs quản lý team
```

## Danh sách APIs

### 📊 Accounts

| API | Command | Mô tả |
|-----|---------|-------|
| [Get Account Profile](accounts/Get-Account-Profile.md) | `accounts/get_profile` | Lấy thông tin profile user |

### 🏆 Leaderboard

| API | Command | Mô tả |
|-----|---------|-------|
| [Get Leaderboard](leaderboard/Get-Leaderboard.md) | `leaderboard/get_leaderboard` | Lấy bảng xếp hạng |
| [Get Last Week Leaderboard](leaderboard/Get-Last-Week-Leaderboard.md) | `leaderboard/get_last_week` | Lấy BXH tuần trước |
| [Get Remaining Time](leaderboard/Get-Remaining-Time.md) | `leaderboard/get_remaining_time` | Thời gian còn lại đến reset |
| [Get Rewards](leaderboard/Get-Rewards.md) | `leaderboard/get_rewards` | Kiểm tra phần thưởng |
| [Claim Rewards](leaderboard/Claim-Rewards.md) | `leaderboard/claim_rewards` | Nhận phần thưởng tuần |
| [Ban Leaderboard User](leaderboard/Ban-Leaderboard-User.md) | `leaderboard/ban_user` | Ban user khỏi BXH |
| [Is Leaderboard Banned](leaderboard/Is-Leaderboard-Banned.md) | `leaderboard/is_banned` | Kiểm tra trạng thái ban |
| [Remove Leaderboard Ban](leaderboard/Remove-Leaderboard-Ban.md) | `leaderboard/remove_ban` | Gỡ ban user |

### 👥 Teams

| API | Command | Mô tả |
|-----|---------|-------|
| [Create Team](teams/Create-team.md) | `teams/create` | Tạo team mới |
| [Get My Team](teams/Get-my-team.md) | `teams/get_my_team` | Lấy thông tin team của mình |
| [Get My Role](teams/Get-my-role.md) | `teams/get_my_role` | Lấy role trong team |
| [Get Team Profile](teams/Get-team-profile.md) | `teams/get_profile` | Lấy profile team |
| [Get Team Members](teams/Get-team-members.md) | `teams/get_members` | Lấy danh sách members |
| [Join Team](teams/Join-team.md) | `teams/join` | Tham gia team |
| [Leave Team](teams/Leave-team.md) | `teams/leave` | Rời khỏi team |
| [Kick Member](teams/Kick-member.md) | `teams/kick` | Kick member khỏi team |
| [Update Team](teams/Update-team.md) | `teams/update` | Cập nhật thông tin team |
| [Update Member Role](teams/Update-member-role.md) | `teams/update_role` | Cập nhật role member |
| [Search Team](teams/Search-team.md) | `teams/search` | Tìm kiếm team |
| [Get Suitable Teams](teams/Get-suitable-teams.md) | `teams/get_suitable` | Lấy teams phù hợp |
| [Get Suggestion Teams](teams/Get-suggestion-teams.md) | `teams/get_suggestions` | Lấy gợi ý teams |
| [Team Schemas](teams/Team-schemas.md) | - | Data schemas cho teams |

### 📈 Log & Analytics

| API | Command | Mô tả |
|-----|---------|-------|
| [Register Device](log-analytic/Register-Device.md) | `log/register_device` | Đăng ký device |
| [Send Log](log-analytic/Send-Log.md) | `log/send` | Gửi log event (WebSocket) |
| [Send Log Event HTTP](log-analytic/Send-Log-Event-HTTP.md) | `POST /log` | Gửi log event (HTTP) |

### 🔧 Cheat (Testing Only)

⚠️ **Các API này chỉ hoạt động trên server test**

| API | Command | Mô tả |
|-----|---------|-------|
| [Add Team](cheat/Add-Team.md) | `cheat/add_team` | Tạo team với bot leader |
| [Add Members](cheat/Add-Members.md) | `cheat/add_members` | Thêm bot members vào team |
| [Delete User Data](cheat/Delete-User-Data.md) | `cheat/delete_user_data` | Xóa dữ liệu user |
| [Reset Leaderboard](cheat/Reset-Leaderboard.md) | `cheat/reset_leaderboard` | Reset BXH tuần |

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

| Code | Mô tả |
|------|-------|
| 0 | Thành công (NO_ERROR) |
| 1 | Lỗi chung (GENERAL_ERROR) |
| 2 | Dữ liệu request không hợp lệ (INVALID_REQUEST_DATA) |
| 3 | Không tìm thấy (NOT_FOUND) |
| 4 | Command không hợp lệ (INVALID_REQUEST_CMD) |
| 5 | Lỗi đăng nhập (LOGIN_ERROR) |
| 6 | Lỗi đăng ký (REGISTER_ERROR) |
| 7 | Session không hợp lệ (INVALID_SESSION) |
| 8 | Không tìm thấy thông tin user (USER_INFO_NOT_FOUND) |
| 9 | Không thể vào phòng (CANNOT_JOIN_ROOM) |
| 10 | Lỗi lấy chat (GET_CHAT) |
| 11 | Lỗi tạo chat room (ERROR_CREATE_CHAT_ROOM) |
| 12 | Lỗi thêm tin nhắn (ERROR_ADD_CHAT_MESSAGE) |
| 13 | Đã đạt giới hạn (MAX_CAPACITY) |
| 14 | Request đã tồn tại (EXISTING_REQUEST) |
| 15 | Không có quyền thực hiện (PERMISSION_DENIED) |
| 16 | Chưa xác thực (UNAUTHORIZED) |
| 17 | Bị cấm truy cập (FORBIDDEN) |

## Authentication

Hầu hết các API yêu cầu user phải đăng nhập (authenticated). Quá trình xác thực:

1. Client kết nối WebSocket
2. Client gửi request login với credentials
3. Server trả về token
4. Các request tiếp theo sẽ được xác thực qua session

