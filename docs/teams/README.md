# Team WebSocket APIs Documentation

## Tổng quan
Tài liệu này cung cấp thông tin chi tiết về các WebSocket API liên quan đến quản lý team trong hệ thống GM10 Server.

## Tài liệu liên quan
- **[Team Schemas](./Team-schemas.md)** - Chi tiết về các cấu trúc dữ liệu và schema được sử dụng

## Danh sách APIs

### 1. Basic Team Operations (Thao tác cơ bản)

#### [Create Team](./Create-team.md)
- **Command**: `teams/create`
- **Mô tả**: Tạo team mới với người tạo trở thành leader
- **Yêu cầu**: Tên team, avatar, mô tả, loại team, điểm tối thiểu

#### [Join Team](./Join-team.md)
- **Command**: `teams/join`
- **Mô tả**: Tham gia vào một team đã tồn tại
- **Yêu cầu**: Team ID, điểm số của user

#### [Leave Team](./Leave-team.md)
- **Command**: `teams/leave`
- **Mô tả**: Rời khỏi team hiện tại
- **Yêu cầu**: Không cần dữ liệu đầu vào

### 2. Team Information (Thông tin team)

#### [Get My Team](./Get-my-team.md)
- **Command**: `teams/mine`
- **Mô tả**: Lấy thông tin chi tiết về team hiện tại của user
- **Yêu cầu**: Không cần dữ liệu đầu vào

#### [Get Team Profile](./Get-team-profile.md)
- **Command**: `teams/profile`
- **Mô tả**: Lấy thông tin chi tiết về một team cụ thể
- **Yêu cầu**: Team ID

#### [Get Team Members](./Get-team-members.md)
- **Command**: `teams/get_members`
- **Mô tả**: Lấy danh sách thành viên của một team
- **Yêu cầu**: Team ID

#### [Search Team](./Search-team.md)
- **Command**: `teams/search_by_text`
- **Mô tả**: Tìm kiếm team theo tên hoặc từ khóa
- **Yêu cầu**: Từ khóa tìm kiếm

### 3. Team Recommendations (Gợi ý team)

#### [Get Suggestion Teams](./Get-suggestion-teams.md)
- **Command**: `teams/get_suggest_teams`
- **Mô tả**: Lấy danh sách team được gợi ý dựa trên recommendation algorithm
- **Yêu cầu**: Không cần dữ liệu đầu vào

#### [Get Suitable Teams](./Get-suitable-teams.md)
- **Command**: `teams/get_suitable_teams`
- **Mô tả**: Lấy danh sách team phù hợp với khả năng và yêu cầu của user
- **Yêu cầu**: Không cần dữ liệu đầu vào

### 4. Team Management (Quản lý team - Leader/Co-leader only)

#### [Update Team](./Update-team.md)
- **Command**: `teams/update_info`
- **Mô tả**: Cập nhật thông tin team (chỉ leader)
- **Yêu cầu**: Team ID, thông tin mới của team

#### [Update Member Role](./Update-member-role.md)
- **Command**: `teams/update_member_role`
- **Mô tả**: Thay đổi vai trò của thành viên (chỉ leader)
- **Yêu cầu**: User ID, role mới

#### [Kick Member](./Kick-member.md)
- **Command**: `teams/kick_member`
- **Mô tả**: Đuổi thành viên ra khỏi team (leader/co-leader)
- **Yêu cầu**: User ID của thành viên cần kick
