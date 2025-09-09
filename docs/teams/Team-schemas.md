# Tài liệu Schema Team

## Tổng quan
Tài liệu này mô tả các schema (cấu trúc dữ liệu) chính được sử dụng trong hệ thống team của GM10 Server, bao gồm models, request/response objects và enums.

## Models Cốt lõi

### TeamData
```kotlin
@Serializable
data class TeamData(
    val teamId: Int = -1,                    // ID nội bộ (không hiển thị)
    val hashTeamId: String,                  // ID đã mã hóa để sử dụng công khai
    val teamName: String,                    // Tên team (3-16 ký tự)
    val avatar: String,                      // Avatar/badge của team
    val description: String,                 // Mô tả team (tối đa 200 ký tự)
    val teamType: TeamType,                  // OPEN hoặc CLOSED
    val minimumScoreRequired: Int = 0,       // Điểm tối thiểu để tham gia (≥ 0)
    val createdAt: Long,                     // Timestamp tạo team
    val lastModifiedAt: Long                 // Timestamp cập nhật cuối
)
```

### TeamProfile
```kotlin
@Serializable
data class TeamProfile(
    val teamData: TeamData,                  // Thông tin cơ bản của team
    val memberCount: Int,                    // Số lượng thành viên hiện tại
    val teamScore: Long,                     // Tổng điểm của team
    val activity: ActivityStatus             // Mức độ hoạt động
)
```

### TeamMember
```kotlin
@Serializable
data class TeamMember(
    val teamId: Int = -1,                   // ID team nội bộ (không hiển thị)
    val userId: Int = -1,                   // ID user nội bộ (không hiển thị)
    val memberId: String,                   // ID user đã mã hóa để công khai
    val role: MemberRole = MemberRole.MEMBER,  // Vai trò trong team
    val joinAt: Long                        // Timestamp tham gia
)
```

### RequestJoinTeam
```kotlin
@Serializable
data class RequestJoinTeam(
    val requestId: Int = -1,                // ID yêu cầu nội bộ (không hiển thị)
    val hashRequestId: String,              // ID yêu cầu đã mã hóa
    val teamId: Int = -1,                   // ID team nội bộ (không hiển thị)  
    val hashTeamId: String,                 // ID team đã mã hóa
    val userId: UserId,                     // User tạo yêu cầu
    val status: JoinRequestStatus,          // Trạng thái hiện tại của yêu cầu
    val createdAt: EpochMilli,             // Thời gian tạo yêu cầu
    val updatedAt: EpochMilli              // Thời gian cập nhật cuối
)
```

## Enums

### TeamType
```kotlin
@Serializable
enum class TeamType {
    OPEN,     // Ai cũng có thể tham gia nếu đủ yêu cầu
    CLOSED    // Cần sự phê duyệt của leader để tham gia
}
```

### MemberRole
```kotlin
@Serializable
enum class MemberRole {
    MEMBER,    // Thành viên thường
    COLEADER,  // Đồng đội trưởng có quyền quản lý
    LEADER     // Đội trưởng có toàn quyền
}
```

### ActivityStatus
```kotlin
@Serializable
enum class ActivityStatus {
    LOW,       // Hoạt động thấp - ít thành viên online
    MEDIUM,    // Hoạt động trung bình
    HIGH       // Hoạt động cao - nhiều thành viên online thường xuyên
}
```

### JoinRequestStatus
```kotlin
@Serializable
enum class JoinRequestStatus {
    PENDING,   // Đang chờ leader phê duyệt
    APPROVED,  // Yêu cầu được chấp nhận, user đã tham gia team
    REJECTED,  // Yêu cầu bị từ chối bởi leader
    CANCELLED, // Yêu cầu bị hủy (không đáp ứng điều kiện)
    EXPIRED,   // Yêu cầu hết hạn do timeout
    BANNED     // User bị cấm tham gia team này
}
```

## Cân nhắc Bảo mật

### Hiển thị Dữ liệu
- Không bao giờ hiển thị ID nội bộ trong API response
- Luôn sử dụng hash ID để tham chiếu công khai
- Trường nhạy cảm đánh dấu với `@Transient`

### Validation
- Tất cả input được validate ở server
- Client-side validation chỉ để cải thiện UX
- Ngăn SQL injection thông qua ORM

