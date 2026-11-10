# Static Assets & Seed Data Specification - OmniLife Personal OS

## 1. Tài Nguyên Kiểu Chữ (Font Assets)

Dự án sử dụng `next/font/google` để tự động tải và tối ưu hóa font chữ tại thời điểm build (Self-hosted, không phát sinh request ngoài khi chạy):
1. **Inter** (`subsets: ['latin', 'vietnamese']`, weights: `400`, `500`, `600`, `700`) - Lưu tại biến CSS `--font-sans`.
2. **JetBrains Mono** (`subsets: ['latin', 'vietnamese']`, weights: `500`, `600`, `700`) - Lưu tại biến CSS `--font-mono`.

---

## 2. Danh Mục Biểu Tượng (Lucide Icons Mapping)

Sử dụng thư viện `lucide-react` với độ dày nét chuẩn `strokeWidth={1.75}` và kích thước mặc định `18px` (`w-4.5 h-4.5`):

| Phân hệ / Chức năng | Tên Component Icon (`lucide-react`) | Mục đích hiển thị |
| :--- | :--- | :--- |
| **Điều hướng chính (Sidebar)** | `LayoutDashboard`, `CalendarDays`, `GraduationCap`, `Wallet`, `Activity`, `BookOpen`, `Settings` | Icon đại diện cho 7 mục điều hướng trên Sidebar |
| **Cửa sổ nổi Mini HUD** | `PictureInPicture2`, `Pin`, `Maximize2`, `Timer`, `CheckCircle2` | Nút bật/tắt cửa sổ Always-on-Top và điều khiển Pomodoro |
| **Độ khẩn cấp (Urgency Tiers)** | `Flame`, `AlertTriangle`, `Clock`, `ArrowUpCircle`, `MinusCircle` | Biểu thị 5 cấp độ khẩn cấp từ `OVERDUE` xuống `LOW` |
| **Không gian Học tập (Study)** | `FileText`, `FileUp`, `BrainCircuit`, `RotateCcw`, `Award`, `BellRing` | Tài liệu học, ôn tập Spaced Repetition, đếm ngược kỳ thi |
| **Tài chính (Finance Ledger)** | `TrendingUp`, `TrendingDown`, `ArrowRightLeft`, `PiggyBank`, `ShieldAlert` | Thu nhập, Chi tiêu, Chuyển khoản ví, Ngân sách, Cảnh báo |
| **Thói quen & Thể thao (Habits)** | `Dumbbell`, `Footprints`, `Droplets`, `Moon`, `Zap` | Tập gym, Chạy bộ, Uống nước, Giấc ngủ, Chuỗi Streak |
| **Nhật ký & Tâm trạng (Mood)** | `SmilePlus`, `Smile`, `Meh`, `Frown`, `CloudLightning` | 5 mức độ tâm trạng từ Tuyệt vời (5) xuống Rất tệ (1) |
| **Trình phát Âm thanh (Audio)** | `Headphones`, `Play`, `Pause`, `Volume2`, `VolumeX`, `CloudRain`, `Waves`, `Coffee`, `FlameKindling`, `Keyboard` | Điều khiển nhạc Lo-Fi và 5 kênh trộn âm thanh trắng |

---

## 3. Tài Nguyên Âm Thanh Tĩnh (Audio & Soundscape Assets)

Toàn bộ tệp âm thanh được nén ở định dạng `.mp3` (hoặc `.ogg`, bitrate `128kbps`, chuẩn hóa âm lượng `-14 LUFS`), đặt trong thư mục `frontend/public/audio/`:

### 3.1. Nhạc nền Tập trung (Lo-Fi Focus Tracks)
1. `frontend/public/audio/lofi/midnight-coding.mp3` - Nhịp độ 75 BPM, giai điệu Piano & Rhodes nhẹ nhàng (Thời lượng: 3:20, Loop liền mạch).
2. `frontend/public/audio/lofi/library-rain-study.mp3` - Nhịp độ 70 BPM, Guitar Acoustic trầm ấm (Thời lượng: 2:55, Loop liền mạch).
3. `frontend/public/audio/lofi/deep-focus-synth.mp3` - Nhạc Ambient không nhịp trống, tăng độ tập trung sâu khi đọc tài liệu khó (Thời lượng: 4:10, Loop liền mạch).

### 3.2. Các lớp Âm thanh Môi trường (Ambient White Noise Loops)
Các file này được cắt nối đầu-cuối (Seamless Loop) để phát lặp vô hạn qua Web Audio API:
1. `frontend/public/audio/ambient/soft-rain.mp3` - Tiếng mưa rơi đều trên mái hiên (Loop 60 giây).
2. `frontend/public/audio/ambient/ocean-waves.mp3` - Tiếng sóng biển vỗ bờ nhịp nhàng (Loop 60 giây).
3. `frontend/public/audio/ambient/coffee-shop.mp3` - Tiếng không gian quán cà phê yên tĩnh (Loop 90 giây).
4. `frontend/public/audio/ambient/campfire-crackle.mp3` - Tiếng củi cháy lách tách ấm áp (Loop 45 giây).
5. `frontend/public/audio/ambient/mech-keyboard.mp3` - Tiếng gõ bàn phím cơ Brown Switch trầm (Loop 45 giây).

### 3.3. Hiệu ứng Âm thanh Tương tác (UI Micro-interaction SFX)
1. `frontend/public/audio/sfx/task-complete.mp3` - Âm báo ngắn (`0.4s`) trong trẻo khi tick hoàn thành một Task.
2. `frontend/public/audio/sfx/pomodoro-bell.mp3` - Tiếng chuông thiền (`2.0s`) báo hiệu kết thúc phiên tập trung Pomodoro 25 phút.
3. `frontend/public/audio/sfx/class-reminder.mp3` - Âm báo nhắc lịch học trên trường trước 30 phút (`1.2s`).

---

## 4. Hình Ảnh Minh Họa & Nhận Diện (Brand & SVG Illustrations)

Đặt tại thư mục `frontend/public/images/`:
1. `logo-omnilife.svg` - Biểu tượng hình khối lục giác lồng tâm tối giản, màu Gradient Indigo (`#6366F1` -> `#8B5CF6`).
2. `empty-tasks.svg` - Minh họa vector nét mảnh (`stroke: #4B5563`) hiển thị khi hoàn thành hết công việc trong ngày.
3. `empty-study-review.svg` - Minh họa vector hiển thị khi hoàn thành xong toàn bộ bài cũ cần ôn tập trong ngày.
4. `empty-journal.svg` - Minh họa sổ tay mở khi người dùng chưa viết nhật ký cho ngày đang chọn.
5. `default-avatar.png` - Ảnh đại diện mặc định kích thước `128x128px` cho tài khoản đăng ký bằng Email.

---

## 5. Bộ Dữ Liệu Mẫu Chuẩn (Comprehensive Seed Data Specification)

Dữ liệu mẫu dưới đây được nạp tự động qua Flyway migration (`V2__seed_initial_data.sql`) để ngay sau khi khởi chạy ứng dụng, bạn có đầy đủ dữ liệu thực tế ở cả 8 phân hệ nhằm kiểm thử công thức tính điểm khẩn cấp, lịch ôn tập Spaced Repetition, sổ cái tài chính và cửa sổ nổi Mini HUD.

### 5.1. Tài khoản Người dùng Mẫu (`users`)
- **ID:** `1`
- **Email:** `khanh.student@omnilife.local`
- **Password (Plaintext để đăng nhập test):** `OmniLife@2026` (Băm BCrypt trong DB: `$2a$12$R9h/cIPz0gi.URNNX3kh2OPST9/PgBkqquzi.Ss7KIUgO2t0jWMUW`)
- **Họ và tên:** `Nguyễn Quốc Khánh`
- **Múi giờ:** `Asia/Ho_Chi_Minh`
- **Mục tiêu học tập mỗi ngày:** `240` (phút)

### 5.2. Danh sách Công việc Mẫu để Kiểm thử Động cơ Điểm Khẩn Cấp (`tasks` & `subtasks`)

| Mã | Tiêu đề công việc | Độ ưu tiên gốc (`base_priority`) | Thời lượng ước tính (`estimated_minutes`) | Hạn chót (`due_at` so với thời điểm chạy) | Điểm khẩn cấp dự kiến ($U$) | Danh sách Subtasks đi kèm |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `T-01` | Nộp bài tập lớn Thiết kế CSDL MySQL Workbench | `CRITICAL` | `120` | `+3 giờ nữa` | `98.4` (`EXTREME`) | 1. Chuẩn hóa bảng lên 3NF (Đã xong)<br>2. Đánh Composite Index (Chưa xong)<br>3. Xuất file báo cáo PDF (Chưa xong) |
| `T-02` | Fix lỗi N+1 Query trong module Study Workspace | `HIGH` | `90` | `+6 giờ nữa` | `82.1` (`EXTREME`) | 1. Bật log `show-sql`<br>2. Thêm `@EntityGraph` vào Repository |
| `T-03` | Giải 3 bài thuật toán Quy hoạch động | `MEDIUM` | `60` | `+20 giờ nữa` | `51.6` (`MODERATE`) | 1. Bài Knapsack 0/1<br>2. Bài Longest Increasing Subsequence |
| `T-04` | Đọc tài liệu Spring Security 6 OAuth2 Filter Chain | `HIGH` | `150` | `+72 giờ nữa` | `54.8` (`MODERATE`) | 1. Đọc luồng Authorization Code<br>2. Viết `CustomOAuth2UserService` |
| `T-05` | Dọn dẹp phòng học và sắp xếp lại bàn máy tính | `LOW` | `30` | `+120 giờ nữa` | `12.5` (`LOW`) | Không có subtask |

### 5.3. Dữ liệu Học kỳ, Môn học & Chủ đề Ôn tập Spaced Repetition (`semesters`, `courses`, `study_topics`, `exams`)

1. **Học kỳ hiện tại:** `Học kỳ 1 - Năm học 2026-2027` (`is_current = true`)
2. **Danh sách 3 Môn học mẫu:**
   - **Môn 1: Phát triển Ứng dụng Enterprise với Spring Boot (`IT4090`)** - 4 tín chỉ - Lịch học: Thứ Hai & Thứ Tư (`07:30 - 10:00`) tại phòng `B1-302`.
     - *Kỳ thi:* Thi cuối kỳ (Cách ngày hiện tại `+14 ngày`, trọng số `60%`).
     - *Các chủ đề học tập (`study_topics`):*
       1. `IoC Container, ApplicationContext và Bean Scopes`: Trạng thái `MASTERED`, `repetition_count = 4`, `interval_days = 25`, `easiness_factor = 2.6`, `next_review_date = +15 ngày`.
       2. `Cơ chế hoạt động của Spring AOP Proxy và @Transactional`: Trạng thái `REVIEWING`, `repetition_count = 2`, `interval_days = 3`, `easiness_factor = 2.36`, `next_review_date = Hôm nay` *(Hiển thị ngay ở mục Bài cũ cần ôn hôm nay)*.
       3. `Optimistic Locking vs Pessimistic Locking trong JPA/Hibernate`: Trạng thái `LEARNING`, `repetition_count = 1`, `interval_days = 1`, `easiness_factor = 2.18`, `next_review_date = Hôm nay` *(Hiển thị ngay ở mục Bài cũ cần ôn hôm nay)*.
       4. `Spring Security 6 Filter Chain và JWT Stateless Auth`: Trạng thái `NEW`, chưa học.
   - **Môn 2: Hệ Quản trị Cơ sở dữ liệu Nâng cao (`IT3080`)** - 3 tín chỉ - Lịch học: Thứ Ba (`13:00 - 15:30`) tại phòng `D3-201`.
     - *Kỳ thi:* Thi giữa kỳ (Cách ngày hiện tại `+5 ngày`, trọng số `30%`).
     - *Các chủ đề học tập:*
       1. `Cấu trúc B+Tree Index và Clustering Index trong InnoDB`: Trạng thái `REVIEWING`, `next_review_date = Hôm nay`.
       2. `Phân tích Query Execution Plan bằng lệnh EXPLAIN`: Trạng thái `NEW`, chưa học.
   - **Môn 3: Cấu trúc Dữ liệu và Giải thuật (`IT3011`)** - 3 tín chỉ - Lịch học: Thứ Sáu (`09:30 - 12:00`) tại phòng `D5-104`.

### 5.4. Dữ liệu Ví, Ngân sách & Giao dịch Tài chính Mẫu (`wallets`, `finance_categories`, `transactions`, `budgets`)

1. **Danh sách Ví (`wallets`):**
   - `Ví Tiền mặt`: Số dư khởi tạo `1,250,000 VNĐ`
   - `Tài khoản Vietcombank`: Số dư khởi tạo `6,800,000 VNĐ`
   - `Ví MoMo`: Số dư khởi tạo `450,000 VNĐ`
2. **Danh mục & Ngân sách tháng hiện tại (`budgets`):**
   - `Ăn uống hàng ngày`: Hạn mức `3,000,000 VNĐ` | Đã chi `2,550,000 VNĐ` (Đạt `85%` - kích hoạt cảnh báo màu Vàng).
   - `Sách vở & Tài liệu học tập`: Hạn mức `800,000 VNĐ` | Đã chi `320,000 VNĐ` (Đạt `40%` - mức an toàn Xanh).
   - `Giải trí & Cà phê`: Hạn mức `500,000 VNĐ` | Đã chi `520,000 VNĐ` (Đạt `104%` - kích hoạt cảnh báo vượt ngưỡng Đỏ).

### 5.5. Dữ liệu Thói quen & Thể thao Mẫu (`habits` & `habit_logs`)
1. `Chạy bộ buổi chiều`: Loại `NUMERIC`, mục tiêu `5.0 km/ngày`, chuỗi hiện tại `current_streak = 7 ngày`, chuỗi kỷ lục `longest_streak = 14 ngày`.
2. `Uống đủ 2 lít nước`: Loại `NUMERIC`, mục tiêu `2000 ml/ngày`, chuỗi hiện tại `current_streak = 12 ngày`.
3. `Đọc sách chuyên ngành 30 phút`: Loại `BOOLEAN`, mục tiêu `1 lần/ngày`, chuỗi hiện tại `current_streak = 5 ngày`.
4. `Ngủ trước 23h30`: Loại `BOOLEAN`, mục tiêu `1 lần/ngày`, chuỗi hiện tại `current_streak = 3 ngày`.

### 5.6. Dữ liệu Nhật ký & Preset Âm thanh Mẫu (`journals`, `audio_presets`)
1. **Nhật ký hôm qua:** Điểm tâm trạng `mood_score = 4 (Tốt)`, mức năng lượng `energy_level = 4`, nội dung Markdown ghi lại việc hiểu rõ sự khác biệt giữa JDK Dynamic Proxy và CGLIB Proxy trong Spring Boot.
2. **Preset Âm thanh mẫu:**
   - Preset 1 (`Code Đêm Tập Trung`): Nhạc `midnight-coding.mp3` (âm lượng `60%`) + `soft-rain.mp3` (`45%`) + `campfire-crackle.mp3` (`25%`).
   - Preset 2 (`Đọc Giáo Trình Buổi Sáng`): Nhạc `library-rain-study.mp3` (`50%`) + `coffee-shop.mp3` (`30%`).
