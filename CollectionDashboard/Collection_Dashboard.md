# Collection Dashboard — Hướng dẫn cho Admin

Trang **Collections** là nơi admin quản lý toàn bộ dữ liệu sưu tập (Collection Items) và các mùa chơi (Seasons) trong hệ thống GM10.

---

## 1. Truy cập Collection Dashboard

1. Đăng nhập vào hệ thống tại `/login`.
2. Sau khi đăng nhập thành công, chọn **Collections** từ trang Home.
3. Trang Collections hiển thị hai tab chính:

[![Collection Dashboard Tabs](Screenshot%202026-03-04%20at%2010.51.33.png)]()

| Tab                  | Chức năng                                              |
|----------------------|--------------------------------------------------------|
| **Seasons**          | Tạo / chỉnh sửa / xoá mùa chơi và gán collections     |
| **Collection Items** | Tạo / chỉnh sửa / xoá từng collection item            |

---

## 2. Tab — Seasons
[![Seasons Tab](Screenshot%202026-03-04%20at%2010.21.07.png)]()

- Danh sách tất cả seasons được hiển thị ở cột bên trái.
- Season đang **diễn ra** được đánh dấu badge **LIVE** màu xanh.
- Mỗi season hiển thị: tên, khoảng ngày (Start – End) và tổng số ngày.
- Nhấn vào một season để xem / chỉnh sửa chi tiết ở cột phải.

> ⚠️ Thao tác **Delete** không thể hoàn tác. Xoá season không xoá các Collection Items — chúng vẫn tồn tại và có thể gán vào season khác.

## 3. Tab — Collection Items
[![Collection Items Tab](Screenshot%202026-03-04%20at%2010.21.23.png)]()
- Danh sách tất cả Collection Items được hiển thị ở cột bên trái.
- Mỗi item hiển thị: tên và số sao (⭐).
- Nhấn vào một item để xem / chỉnh sửa chi tiết ở cột phải.

### Basic Info

| Trường           | Bắt buộc | Mô tả                                    |
|------------------|----------|------------------------------------------|
| **Name**         | ✅        | Tên collection (hiển thị với người dùng) |
| **Weight**       | ✅        | Độ nặng collection trong game            |
| **Score / Item** | ✅        | Điểm nhận được khi gắp được mỗi vật phẩm |
| **Total Items**  | ✅        | Tổng số mảnh ghép của collection         |
| **Star**         | ✅        | Độ hiếm (1–5 sao)                        |

### Rewards

| Trường                  | Mô tả                                                                       |
|-------------------------|-----------------------------------------------------------------------------|
| **In-game Rewards**     | Phần thưởng nhận được **mỗi lần** gắp đủ collection trong game              |
| **First-time Rewards**  | Phần thưởng **chỉ nhận 1 lần** khi sưu tập đủ collection lần đầu tiên       |

Mỗi loại reward gồm các dòng **Token + Value**. Các loại token hỗ trợ:

`Ruby` · `Gold` · `LuckyTicket` · `Dynamite` · `RemoveAdsToken` · `Strength` · `Diamond` · `LuckyClover` · `PocketWatch` · `Rock` · `ExtraDiamond` · `ExtraGold` · `Star` · `Score` · `StoneItem` · `RockItem` · `DiamondItem` · `GoldItem` · `MouseItem` · `BoneItem`

### Item Sprites
- Item Sprite là id của icon để truy vấn hình ảnh vật phẩm trong game.
- Sprite hiện tại lưu ở local. Vì vậy admin cần đảm bảo rằng sprite URL hợp lệ và đã tồn tại sprite ở client để tránh lỗi hiển thị trong game.
### Note

> 💡 Số lượng sprite URL nên khớp với giá trị **Total Items** để tránh lỗi hiển thị trong game.

> ⚠️ Trước khi xoá, hãy đảm bảo item **không còn được gán vào bất kỳ Season đang LIVE nào** để tránh ảnh hưởng đến người dùng.
