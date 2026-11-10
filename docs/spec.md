# Functional & Business Specification - OmniLife Personal OS

## 1. Đặc Tả Yêu Cầu Chức Năng (Functional Requirements)

### 1.1. Phân hệ Xác thực & Quản lý Tài khoản (Auth & User Module)
- **FR-AUTH-01 (Đăng ký Local):** Cho phép người dùng tạo tài khoản bằng Họ tên, Email và Mật khẩu. Mật khẩu phải dài tối thiểu 8 ký tự, chứa ít nhất 1 chữ hoa, 1 chữ thường và 1 chữ số. Mật khẩu được băm bằng thuật toán `BCrypt` (strength = 12) trước khi lưu vào MySQL.
- **FR-AUTH-02 (Đăng nhập Local):** Xác thực Email/Password, cấp phát `accessToken` (JWT, hạn 15 phút) và `refreshToken` (lưu trong DB và HttpOnly Cookie, hạn 7 ngày).
- **FR-AUTH-03 (Đăng nhập Google OAuth2):** Xác thực qua Google OpenID Connect. Nếu Email trả về từ Google đã tồn tại trong bảng `users` (từng đăng ký bằng mật khẩu), hệ thống tự động liên kết bản ghi `oauth_accounts` vào `user_id` hiện có mà không tạo trùng người dùng.
- **FR-AUTH-04 (Làm mới & Thu hồi Token):** Khi `accessToken` hết hạn, Client gọi endpoint `/api/v1/auth/refresh` để nhận cặp token mới (Refresh Token Rotation). Nếu phát hiện một Refresh Token đã thu hồi được gửi lại, hệ thống lập tức hủy toàn bộ phiên đăng nhập của người dùng đó.
- **FR-AUTH-05 (Cấu hình cá nhân):** Cho phép chỉnh sửa thông tin cá nhân, múi giờ (mặc định `Asia/Ho_Chi_Minh`), mục tiêu số giờ tập trung mỗi ngày và hệ số trọng số tính điểm khẩn cấp.

### 1.2. Phân hệ Lịch Biểu & Động Cơ Công Việc (Calendar & Dynamic Task Engine)
- **FR-TASK-01 (Quản lý Công việc - CRUD):** Tạo, xem, sửa, xóa mềm (Soft Delete) công việc với các thuộc tính: Tiêu đề (`title`), Mô tả chi tiết Markdown (`description`), Độ ưu tiên gốc (`base_priority`: `LOW`, `MEDIUM`, `HIGH`, `CRITICAL`), Thời lượng ước tính (`estimated_minutes`), Hạn chót (`due_at`), Khung giờ dự kiến thực hiện trên lịch (`scheduled_start_at`, `scheduled_end_at`), Trạng thái (`TODO`, `IN_PROGRESS`, `DONE`, `ARCHIVED`).
- **FR-TASK-02 (Chia nhỏ công việc - Subtasks):** Mỗi công việc chính có thể chứa danh sách các bước nhỏ (`subtasks`). Khi tick hoàn thành các subtask, thanh tiến độ của task chính tự động cập nhật tỷ lệ phần trăm.
- **FR-TASK-03 (Chi tiết công việc theo ngày):** Khi bấm vào bất kỳ ngày nào trên Lịch hoặc bấm vào một Task, một ngăn kéo chi tiết (Slide-over Drawer) mở ra hiển thị toàn bộ thông tin: mô tả, danh sách subtasks, lịch sử các phiên Pomodoro đã chạy cho task đó, các ghi chú đính kèm và phân tích chi tiết điểm khẩn cấp hiện tại.
- **FR-TASK-04 (Tính điểm Khẩn cấp Động - Dynamic Urgency Score):** Hệ thống tự động tính toán và cập nhật chỉ số `urgency_score` của mọi công việc chưa hoàn thành. Danh sách công việc mặc định sắp xếp giảm dần theo `urgency_score`.
- **FR-CAL-01 (Chế độ xem Lịch Tuần/Tháng/Ngày):** Hiển thị đồng thời các Sự kiện cố định (Calendar Events), Lịch học trên trường (Class Schedules) và các Task đã được gán khung giờ thực hiện (Time-blocked Tasks).
- **FR-CAL-02 (Kéo thả Time-blocking):** Người dùng có thể kéo một Task từ danh sách bên phải thả trực tiếp vào một khung giờ trống trên Lịch tuần để ấn định giờ làm việc.
- **FR-CAL-03 (Đồng bộ Google Calendar):** Cho phép nhập (Import) và đồng bộ các sự kiện từ Google Calendar của người dùng vào bảng `calendar_events` thông qua `google_event_id`.

### 1.3. Phân hệ Cửa Sổ Nổi Nhắc Việc (Always-on-Top Mini HUD)
- **FR-HUD-01 (Khởi chạy Cửa sổ Nổi):** Khi người dùng nhấn nút "Detach Mini HUD", trình duyệt gọi `window.documentPictureInPicture.requestWindow({ width: 340, height: 460 })` để mở một cửa sổ luôn nổi trên màn hình máy tính.
- **FR-HUD-02 (Hiển thị Top 3 Việc Khẩn Cấp):** Cửa sổ nổi luôn hiển thị tối đa 3 công việc có `urgency_score` cao nhất hiện tại kèm bộ đếm thời gian còn lại tới Deadline (ví dụ: `Còn 2h 15p`).
- **FR-HUD-03 (Tương tác Hai chiều Thời gian thực):**
  - Tick hoàn thành một Task ngay trên cửa sổ nổi sẽ lập tức gọi API cập nhật trạng thái `DONE`, phát hiệu ứng hoàn thành và tự động đẩy Task khẩn cấp thứ 4 lên thay thế.
  - Bấm bắt đầu/tạm dừng đồng hồ Pomodoro trên cửa sổ nổi sẽ đồng bộ tức thì với trạng thái trên giao diện web chính và ghi nhận vào bảng `focus_sessions`.

### 1.4. Phân hệ Không Gian Học Tập Chuyên Sâu (Dedicated Study Workspace)
- **FR-STUDY-01 (Quản lý Học kỳ & Môn học):** Tạo học kỳ (`semesters`) và danh sách các môn học (`courses`) với mã môn, số tín chỉ, giảng viên, phòng học, mục tiêu điểm tổng kết và màu sắc nhận diện.
- **FR-STUDY-02 (Thời khóa biểu & Thông báo lịch học):** Thiết lập lịch học hàng tuần theo thứ trong tuần (`day_of_week`), giờ bắt đầu, giờ kết thúc. Hệ thống tự động gửi thông báo trên trình duyệt (Web Notification) và Email trước giờ vào lớp 30 phút.
- **FR-STUDY-03 (Import Tài liệu & Bóc tách Danh mục Học tập):**
  - Cho phép tải lên tài liệu học tập (file `.pdf`, `.docx`, `.txt`, `.md`) hoặc dán trực tiếp đề cương môn học.
  - Backend sử dụng Apache PDFBox / POI để trích xuất văn bản, nhận diện các tiêu đề chương/mục (Headings) và tự động sinh ra danh sách các đơn vị kiến thức cần học (`study_topics`) để người dùng duyệt và phân bổ vào các ngày trong tuần.
- **FR-STUDY-04 (Kiểm soát Tiến độ & Ôn bài cũ bằng Spaced Repetition):**
  - Mỗi `study_topic` có trạng thái nắm vững (`mastery_level`: `NEW`, `LEARNING`, `REVIEWING`, `MASTERED`).
  - Khi người dùng hoàn thành một buổi học hoặc ôn lại bài cũ, người dùng tự đánh giá mức độ nhớ trên thang điểm từ `0` (Quên hoàn toàn) đến `5` (Nhớ rất rõ).
  - Hệ thống áp dụng thuật toán SM-2 để tự động tính toán ngày phải ôn lại tiếp theo (`next_review_date`) và đưa vào danh sách "Bài cũ cần ôn hôm nay".
- **FR-STUDY-05 (Đếm ngược Kỳ thi & Checklist Ôn thi):**
  - Tạo lịch thi (`exams`) cho từng môn học (Giữa kỳ, Cuối kỳ, Vấn đáp) kèm danh sách các `study_topics` thuộc phạm vi thi.
  - Tính toán chỉ số sẵn sàng (`readiness_percentage`) dựa trên tỷ lệ các chủ đề đã đạt trạng thái `REVIEWING` hoặc `MASTERED` và cảnh báo nếu tốc độ học hiện tại không kịp ngày thi.

### 1.5. Phân hệ Quản Lý Chi Tiêu Cá Nhân (Personal Finance Ledger)
- **FR-FIN-01 (Quản lý Ví & Danh mục):** Tạo nhiều nguồn tiền (`wallets`: Tiền mặt, Ngân hàng, Ví điện tử) và các danh mục thu/chi (`finance_categories`).
- **FR-FIN-02 (Ghi nhận Giao dịch):** Thêm mới giao dịch (`INCOME`, `EXPENSE`, `TRANSFER` giữa 2 ví). Khi giao dịch được lưu, số dư của ví liên quan được cập nhật nguyên tử (Atomic) trong cùng một Database Transaction.
- **FR-FIN-03 (Quản lý Ngân sách Tháng):** Đặt hạn mức chi tiêu tối đa hàng tháng cho từng danh mục hoặc tổng chi tiêu toàn tháng. Hiển thị thanh tiến trình màu Xanh (< 70%), Vàng (70% - 90%), Đỏ (> 90%).
- **FR-FIN-04 (Báo cáo & Biểu đồ Dòng tiền):** Thống kê tổng thu, tổng chi, chênh lệch ròng theo tháng và biểu đồ cơ cấu chi tiêu theo danh mục.

### 1.6. Phân hệ Thói Quen & Thể Thao (Habit & Fitness Tracker)
- **FR-HABIT-01 (Thiết lập Thói quen & Bài tập):** Tạo thói quen với loại đo lường: Đánh dấu hoàn thành (`BOOLEAN` - ví dụ: Ngủ trước 23h) hoặc Định lượng (`NUMERIC` - ví dụ: Chạy bộ 5 km, Uống 2000 ml nước, Tập Gym 45 phút).
- **FR-HABIT-02 (Check-in Hàng ngày & Tính Streak):** Ghi nhận kết quả thực hiện theo ngày. Tự động tính chuỗi ngày liên tiếp (`current_streak`) và kỷ lục chuỗi dài nhất (`longest_streak`).
- **FR-HABIT-03 (Biểu đồ Nhiệt 365 Ngày):** Trích xuất dữ liệu hoàn thành thói quen trong 52 tuần gần nhất để hiển thị ma trận nhiệt (Contribution Heatmap).

### 1.7. Phân hệ Nhật Ký & Cảm Xúc (Daily Journal & Mood)
- **FR-JOUR-01 (Soạn thảo Nhật ký theo Ngày):** Mỗi ngày có một trang nhật ký chính hỗ trợ cú pháp Markdown, gắn thẻ (`tags`) và đánh dấu ngày đáng nhớ (`is_bookmarked`).
- **FR-JOUR-02 (Theo dõi Tâm trạng - Mood Tracker):** Ghi nhận điểm tâm trạng (`mood_score` từ 1 đến 5: Rất tệ, Căng thẳng, Bình thường, Tốt, Tuyệt vời) và mức năng lượng (`energy_level` từ 1 đến 5).
- **FR-JOUR-03 (Bản chụp Hoạt động Tự động - Daily Activity Snapshot):** Khi mở trang nhật ký của một ngày, hệ thống tự động truy vấn và đính kèm khung tóm tắt: Các task đã hoàn thành, số phút Pomodoro đã học, các chủ đề môn học đã ôn, các bài tập thể thao đã thực hiện và tổng số tiền đã chi tiêu trong ngày đó.

### 1.8. Phân hệ Âm Thanh Tập Trung (Ambient Focus Player)
- **FR-AUDIO-01 (Phát Nhạc Lo-Fi & Trộn Âm Thanh Trắng):** Cung cấp trình phát nhạc cố định dưới đáy màn hình, cho phép phát luồng nhạc Lo-Fi kết hợp bật/tắt đồng thời nhiều lớp âm thanh môi trường (Tiếng mưa, Tiếng sấm nhẹ, Tiếng sóng biển, Tiếng quán cà phê, Tiếng bàn phím cơ) với thanh điều chỉnh âm lượng riêng cho từng lớp.
- **FR-AUDIO-02 (Lưu Preset Âm thanh):** Người dùng có thể lưu tổ hợp âm lượng yêu thích thành các Preset cá nhân (ví dụ: "Đêm ôn thi", "Viết code buổi sáng").

---

## 2. Đặc Tả Yêu Cầu Phi Chức Năng (Non-Functional Requirements)

1. **NFR-SEC (Bảo mật):**
   - Toàn bộ mật khẩu băm bằng BCrypt.
   - Các API nghiệp vụ bắt buộc xác thực qua `Bearer <JWT>` và kiểm tra quyền sở hữu dữ liệu (`user_id` trong câu truy vấn luôn lấy từ `SecurityContextHolder`, tuyệt đối không tin tưởng `user_id` truyền từ Client để chống lỗ hổng IDOR).
   - Cấu hình CORS chặt chẽ chỉ cho phép domain của Frontend.
2. **NFR-CONC (Xử lý Đồng thời & Toàn vẹn Dữ liệu):**
   - Áp dụng **Optimistic Locking (`@Version`)** trên bảng `tasks` để chống ghi đè khi người dùng vừa sửa task trên giao diện chính vừa bấm cập nhật trên cửa sổ Mini HUD.
   - Áp dụng **Pessimistic Write Locking (`SELECT ... FOR UPDATE`)** trên bảng `wallets` khi thực hiện giao dịch tài chính để đảm bảo số dư không bao giờ bị sai lệch khi có nhiều request đồng thời.
3. **NFR-PERF (Hiệu năng):**
   - Thời gian phản hồi trung bình của các API đọc dữ liệu (Dashboard, Calendar, Task List) dưới `150ms` ở mức phân vị P95.
   - Nghiêm cấm lỗi N+1 Query trong Hibernate; toàn bộ các truy vấn danh sách có quan hệ `ManyToOne` hoặc `OneToMany` phải sử dụng `@EntityGraph` hoặc DTO Projection.
4. **NFR-COMPAT (Tương thích):**
   - Giao diện đáp ứng (Responsive) trên màn hình Laptop/Desktop (1280px - 2560px) và Tablet/Mobile.
   - Nếu trình duyệt không hỗ trợ `documentPictureInPicture` (ví dụ Firefox hoặc Safari cũ), hệ thống tự động chuyển đổi dự phòng (Fallback) sang chế độ Floating Mini Panel ghim ở góc phải dưới màn hình web.

---

## 3. Luồng Người Dùng Chi Tiết (Step-by-Step User Flows)

### Luồng 1: Tạo Công Việc, Tính Điểm Khẩn Cấp & Sử Dụng Cửa Sổ Nổi Mini HUD
1. Người dùng đăng nhập vào OmniLife, tại Dashboard chính nhấn phím tắt `C` (hoặc nút "Thêm công việc").
2. Người dùng nhập:
   - Tiêu đề: "Hoàn thành báo cáo Cơ sở dữ liệu"
   - Độ ưu tiên gốc: `HIGH`
   - Thời lượng ước tính: `120 phút`
   - Hạn chót: Hôm nay lúc `23:00`
3. Frontend gửi `POST /api/v1/tasks`. Backend lưu bản ghi, kích hoạt `UrgencyCalculationService` tính ra điểm khẩn cấp là `86.5 / 100` (mức `CRITICAL_URGENCY` do hạn chót còn dưới 8 giờ).
4. Công việc lập tức xuất hiện ở vị trí số 1 trên bảng Task, viền thẻ chuyển sang màu Đỏ Hổ Phách.
5. Người dùng chuẩn bị mở phần mềm MySQL Workbench và Word để làm báo cáo. Trước khi thu nhỏ trình duyệt, người dùng bấm nút **"Mở Mini HUD"** trên thanh Header.
6. Cửa sổ nhỏ nổi lên ở góc phải màn hình máy tính, luôn nằm đè lên trên cửa sổ Word và MySQL Workbench.
7. Người dùng bấm nút "Play Pomodoro (25:00)" ngay trên Mini HUD để bắt đầu làm việc.
8. Sau khi hoàn thành từng mục nhỏ, người dùng tick thẳng vào các Subtask trên Mini HUD mà không cần mở lại trình duyệt.

### Luồng 2: Thiết Lập Môn Học, Import Đề Cương & Ôn Tập Spaced Repetition
1. Người dùng mở phân hệ **Study Workspace**, tạo môn học "Lập trình Web với Spring Boot" và thêm kỳ thi "Thi cuối kỳ" vào ngày 20 tháng sau.
2. Người dùng tải lên file `De-cuong-Spring-Boot.pdf` (hoặc dán danh sách 15 chuyên đề).
3. Hệ thống bóc tách thành 15 `study_topics` (ví dụ: Chuyên đề 1: IoC Container & Bean Lifecycle, Chuyên đề 2: Spring Data JPA & Hibernate...).
4. Hôm nay người dùng học xong "Chuyên đề 1", bấm nút **"Đánh dấu đã học"** và chọn mức độ hiểu bài là `4 - Nhớ tốt`.
5. Hệ thống cập nhật trạng thái chuyên đề thành `REVIEWING`, thiết lập `interval_days = 1`, `next_review_date = Ngày mai`.
6. Sáng ngày mai, khi người dùng mở ứng dụng, khu vực **"Bài cũ cần ôn hôm nay"** tự động hiển thị "Chuyên đề 1: IoC Container & Bean Lifecycle". Nếu người dùng ôn lại và tiếp tục đánh giá mức `4`, chu kỳ ôn tiếp theo tự động nhảy lên `3 ngày`, rồi `7 ngày`, `16 ngày`... đảm bảo kiến thức được khắc sâu trước ngày thi.

---

## 4. Quy Tắc Nghiệp Vụ & Công Thức Toán Học (Business Logic Rules)

### 4.1. Công thức Tính Điểm Khẩn Cấp Động (Dynamic Urgency Scoring Formula)

Điểm khẩn cấp $U \in [0, 100]$ của một công việc chưa hoàn thành tại thời điểm hiện tại $t_{\text{now}}$ được xác định bởi công thức:

$$U = \min\left(100, \; B + D(t_{\text{now}}) + E + S\right)$$

Trong đó:
1. **Điểm ưu tiên gốc ($B$ - Base Priority Score):**
   - `LOW`: $B = 10$
   - `MEDIUM`: $B = 25$
   - `HIGH`: $B = 45$
   - `CRITICAL`: $B = 65$

2. **Điểm áp lực thời gian ($D$ - Time Decay Urgency Factor):**
   Gọi $h$ là số giờ còn lại từ thời điểm hiện tại $t_{\text{now}}$ đến hạn chót `due_at` ($h = \frac{\text{due\_at} - t_{\text{now}}}{3600\text{s}}$):
   - Nếu công việc không có hạn chót (`due_at == null`): $D = 0$.
   - Nếu đã quá hạn ($h \le 0$): $D = 45 + \min(15, |h| \times 0.5)$ (Tối đa cộng thêm 60 điểm để luôn đứng đầu bảng cảnh báo quá hạn).
   - Nếu chưa quá hạn ($h > 0$):
     $$D = \frac{40}{1 + 0.08 \times h^{1.3}}$$
     *(Ý nghĩa: Khi còn 7 ngày ($h = 168$), $D \approx 0.6$ điểm. Khi còn 24 giờ ($h = 24$), $D \approx 6.7$ điểm. Khi còn 4 giờ ($h = 4$), $D \approx 26.9$ điểm. Khi còn 1 giờ ($h = 1$), $D \approx 37.0$ điểm. Đường cong tăng tốc cực mạnh khi tiến sát deadline).*

3. **Điểm áp lực khối lượng công việc ($E$ - Effort Ratio Pressure):**
   Gọi $m_{\text{rem}}$ là thời lượng ước tính còn lại để làm xong công việc (tính bằng giờ). Nếu tỷ lệ $\frac{m_{\text{rem}}}{\max(0.5, h)}$ càng lớn (thời gian làm việc ước tính gần bằng hoặc vượt quá số giờ còn lại trước hạn chót):
   $$E = \min\left(15, \; 10 \times \frac{m_{\text{rem}}}{\max(0.5, h)}\right)$$

4. **Điểm cộng lịch trình trong ngày ($S$ - Scheduled Today Bonus):**
   - Nếu công việc được lên lịch thực hiện trong ngày hôm nay: $S = 10$, ngược lại $S = 0$.

5. **Phân ngưỡng Cảnh báo Màu sắc (Urgency Tier):**
   - `OVERDUE` ($h \le 0$): Đỏ Đậm Chớp Nháy (Critical Overdue).
   - `EXTREME` ($U \ge 80$): Đỏ Hổ Phách (Immediate Action Required).
   - `HIGH` ($60 \le U < 80$): Cam Cảnh Báo (High Focus).
   - `MODERATE` ($35 \le U < 60$): Xanh Lam (Planned).
   - `LOW` ($U < 35$): Xám Trung Tính (Backlog).

### 4.2. Thuật toán Lặp Lại Ngắt Quãng (Spaced Repetition SM-2 cho Study Workspace)

Mỗi chủ đề học tập (`study_topic`) lưu trữ 3 tham số:
- $n$: Số lần đã ôn tập thành công liên tiếp (`repetition_count`, khởi tạo $= 0$).
- $EF$: Hệ số dễ nhớ (`easiness_factor`, khởi tạo $= 2.5$, giới hạn tối thiểu $= 1.3$).
- $I$: Khoảng cách số ngày đến lần ôn tiếp theo (`interval_days`, khởi tạo $= 0$).

Khi người dùng ôn tập và chấm điểm mức độ nhớ $q \in \{0, 1, 2, 3, 4, 5\}$:
1. **Nếu $q < 3$ (Không nhớ hoặc nhớ mang máng):**
   - Reset số lần nhớ liên tiếp: $n = 0$
   - Đặt lại khoảng cách ôn tập ngay ngày hôm sau: $I = 1$
   - Trạng thái chuyển về: `LEARNING`
2. **Nếu $q \ge 3$ (Nhớ đạt yêu cầu):**
   - Nếu $n = 0 \Rightarrow I = 1$
   - Nếu $n = 1 \Rightarrow I = 3$
   - Nếu $n \ge 2 \Rightarrow I = \text{round}(I \times EF)$
   - Tăng $n = n + 1$
   - Nếu $I \ge 21$ ngày, trạng thái nâng lên `MASTERED`, ngược lại là `REVIEWING`.
3. **Cập nhật hệ số $EF$:**
   $$EF' = \max\left(1.3, \; EF + \left(0.1 - (5 - q) \times (0.08 + (5 - q) \times 0.02)\right)\right)$$
4. **Ngày ôn tập tiếp theo:**
   $$\text{next\_review\_date} = \text{ current\_date } + I \text{ (ngày)}$$

### 4.3. Quy tắc Xử lý Ngoại lệ (Exception Handling Rules)
- **Xung đột cập nhật đồng thời (Optimistic Lock Exception):** Khi cùng một Task bị sửa từ 2 nơi (Web chính và Mini HUD) làm lệch cột `version`, Backend trả về mã lỗi HTTP `409 Conflict` kèm dữ liệu mới nhất để Frontend tự động đồng bộ lại trạng thái.
- **Giao dịch vượt số dư hoặc tiền âm:** Số tiền giao dịch `amount` bắt buộc sử dụng kiểu `BigDecimal` (`> 0.00`). Nếu xóa một giao dịch cũ, hệ thống phải thực hiện bút toán đảo ngược số dư vào đúng ví tương ứng trong cùng một Transaction.
- **Tài liệu học tập tải lên không hợp lệ:** Chỉ chấp nhận file dưới `15MB`. Nếu file PDF bị khóa mật khẩu hoặc chỉ chứa ảnh scan không có lớp văn bản (text layer), hệ thống trả về mã lỗi `422 Unprocessable Entity` với thông báo hướng dẫn người dùng nhập mục lục thủ công.
