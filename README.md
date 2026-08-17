# GeckApp: Ballon Storm

Game mẫu Unity 2D (dạng bắn súng cuộn ngang – *shoot 'em up*) được sử dụng trong lớp **Trải nghiệm Phát triển Game** thuộc chương trình hợp tác **[Algo BootCamp X GamApp]**, giúp học viên làm quen với quy trình phát triển game từ asset đến gameplay hoàn chỉnh.

- **Engine:** Unity `6000.2.12f1` (Unity 6)
- **Render Pipeline:** Universal Render Pipeline (URP)
- **Input:** Unity Input System (mới)
- **Camera:** Cinemachine 3 (rung màn hình khi mất máu)
- **UI Text:** TextMesh Pro

---

## Cách chơi

| Phím | Chức năng |
|---|---|
| `W` | Di chuyển lên |
| `A` | Di chuyển trái |
| `D` | Di chuyển phải |
| `Space` | Bắn |
| `1` / `2` / `3` | Đổi kiểu bắn (Shooting Type 1 / 2 / 3) |

Người chơi có 3 máu (tim). Va chạm với quái sẽ mất 1 máu, hết máu sẽ thua và hiện popup Lose. Tiêu diệt quái bằng đạn để cộng điểm.

---

## Hướng dẫn sử dụng các asset chính

### 1. Sprite nhân vật & animation (`Assets/Sprites/Main`)

<p align="center">
  <img src="Assets/Sprites/Main/SpaceShip.png" alt="Player SpaceShip" height="120">
  &nbsp;&nbsp;&nbsp;
  <img src="Assets/Sprites/Main/geckapp_idle/GeckApp-animation---Idle_0000_Layer-1.png" alt="GeckApp Idle" height="120">
  &nbsp;&nbsp;&nbsp;
  <img src="Assets/Sprites/Main/geckapp_attack/GeckApp-animation---Attack_0000_Layer-181.png" alt="GeckApp Attack" height="120">
</p>
<p align="center"><i>SpaceShip (Player) · GeckApp Idle · GeckApp Attack</i></p>

- `SpaceShip.png`: sprite tàu/nhân vật người chơi, gắn trên GameObject Player cùng `PlayerMove`, `PlayerShooting`, `PlayerHealth`.
- `geckapp_idle/`, `geckapp_attack/`: 2 bộ khung hình animation dạng sprite sheet rời (frame-by-frame) đã được build sẵn thành 2 file `.anim` (`GeckApp_Idle.anim`, `GeckApp_Attack.anim`) và 2 Animator Controller tương ứng (`..._0.controller`). Muốn dùng lại animation này cho nhân vật khác, chỉ cần kéo file `.anim` vào Animator Controller của GameObject đó, hoặc tái sử dụng trực tiếp `Assets/Animations/GeckApp_Controller.controller`.
- `red_circle_8.png`, `circle_8.png`, `purple_circle_8.png`: sprite chấm tròn nhỏ dùng làm đạn (bullet) hoặc hiệu ứng, đã gán sẵn trong các prefab `Bullet_1/2/3`.

### 2. Prefab đạn (`Assets/Prefabs/Bullets`)
- Mỗi prefab (`Bullet_1`, `Bullet_2`, `Bullet_3`) gắn script `BaseBullet` (di chuyển thẳng, tự huỷ sau 2s, huỷ quái khi trúng đạn qua `Collider2D` dạng Trigger).
- Muốn thêm kiểu bắn mới: tạo script kế thừa `BaseShootingType`, override hàm `Shoot()`, kéo prefab `Bullet` phù hợp và điểm bắn (`HeadGun`) vào Inspector.
- 3 kiểu bắn có sẵn:
  - `ShootingType1`: bắn 3 phát liên tiếp cách nhau 0.15s (kiểu súng máy).
  - `ShootingType2`: bắn 3 nòng cùng lúc (3 điểm `HeadGun`).
  - `ShootingType3`: bắn hình quạt 3 tia lệch góc ±15°.

### 3. Prefab quái (`Assets/Prefabs/Enemies`)

<p align="center">
  <img src="Assets/Sprites/Enemy/Enemy01.png" alt="Enemy01" height="90">
  <img src="Assets/Sprites/Enemy/Enemy02.png" alt="Enemy02" height="90">
  <img src="Assets/Sprites/Enemy/Enemy03.png" alt="Enemy03" height="90">
  <img src="Assets/Sprites/Enemy/Enemy04.png" alt="Enemy04" height="90">
  <img src="Assets/Sprites/Enemy/Enemy05.png" alt="Enemy05" height="90">
</p>

- Tất cả kế thừa `BaseEnemy` (có `EnemySpeed`, `EnemyScore`, hiệu ứng chết `deathEffectPrefab`, tự huỷ khi ra khỏi màn hình, gây damage cho Player khi va chạm).
- `Horse1/2`: bay thẳng theo một đường ngang.
- `Tanker1/2`: bay zíc-zắc lên xuống.
- `Assassin`: luôn xoay hướng và bay thẳng về phía Player (đuổi theo).
- `EnemyDeathEffect.prefab`: hiệu ứng mờ dần (`fade out`) khi quái chết, dùng sprite `Assets/Sprites/Enemy/Die.png`.
- Muốn tạo quái mới: kéo sprite trong `Assets/Sprites/Enemy` vào GameObject mới, gắn `Collider2D (Trigger)`, viết script kế thừa `BaseEnemy` và override `Move()` theo kiểu di chuyển mong muốn, sau đó lưu thành prefab và gán vào `EnemySpawner`.

### 4. Spawner quái
- `EnemySpawner` (đặt trong scene) spawn 1 loại `BaseEnemy` prefab theo chu kỳ `SpawnCooldown`, vị trí Y ngẫu nhiên. Muốn spawn nhiều loại quái, có thể đặt nhiều `EnemySpawner` (mỗi cái 1 prefab khác nhau) trong scene.

### 5. Nền cuộn (`Assets/Sprites/BackGround`)

<p align="center">
  <img src="Assets/Sprites/BackGround/Background.png" alt="Background" width="380">
</p>

- `Background.png`, `BackGround 1.png`: 2 ảnh nền được ghép nối liên tục (seamless) tạo hiệu ứng cuộn vô hạn.
- Script `BackgroundScrolling` nhận mảng các GameObject nền (`Backgrounds`), khi 1 ảnh cuộn hết ra ngoài (`ScrollingDistance`) sẽ được đưa về lại phía sau để tạo vòng lặp.

### 6. UI & hiệu ứng (`Assets/Sprites/UI`, `Assets/Scripts/Menu`)

<p align="center">
  <img src="Assets/Sprites/UI/pixelheart.png" alt="Heart Icon" height="60">
  <img src="Assets/Sprites/UI/PauseButton.png" alt="Pause Button" height="60">
  <img src="Assets/Sprites/UI/RestartButton.png" alt="Restart Button" height="60">
  <img src="Assets/Sprites/UI/QuitButton.png" alt="Quit Button" height="60">
</p>

- `pixelheart.png`: icon tim, dùng với `HealthUI` (mảng `Image[] heartImages`) để hiển thị số máu còn lại.
- `PauseButton.png`, `RestartButton.png`, `QuitButton.png`: icon nút bấm, gắn sự kiện `OnClick` gọi tương ứng `GameUI.Pause()`, `GameUI.Restart()`, `GameUI.Quit()`.
- `DamageFlash.png`: overlay đỏ toàn màn hình, dùng bởi `DamageFlashUI` để nhấp nháy khi Player bị mất máu.
- `GameScore`: cộng điểm và hiển thị lên `TextMeshProUGUI` mỗi khi quái bị tiêu diệt (`EnemyScore`).
- `CameraShakeUI` (dùng Cinemachine Impulse Source): rung camera khi Player mất máu — cần gắn `CinemachineImpulseSource` trên GameObject Camera/Player.

### 7. Animator Controller dùng chung
- `Assets/Animations/GeckApp_Controller.controller`: điều khiển chuyển đổi giữa animation Idle ↔ Attack cho nhân vật GeckApp; có thể tái sử dụng cho các nhân vật/skin khác có cùng cấu trúc animation.

---

## Người thực hiện

| Vai trò | Người thực hiện |
|---|---|
| Dev (Lập trình) | **Phạm Hoàng Nam** |
| Art (Thiết kế đồ hoạ) | **Phạm Nguyễn Xuân Trang** |

---

##  Ghi chú
- Thư mục `Assets/TextMesh Pro` là asset mặc định đi kèm plugin TMP, không nên chỉnh sửa trực tiếp.
- Đây là project mẫu phục vụ mục đích học tập trong chương trình Algo BootCamp X GamApp.
