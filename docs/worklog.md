# Project Execution Worklog - OmniLife Personal OS

Bảng theo dõi tiến độ thực thi dự án **OmniLife** khớp 100% với danh sách `TASK-01` đến `TASK-21` trong [roadmap.md](./roadmap.md).

## 1. Quy Ước Trạng Thái (Status Legend)
- `TODO`: Chưa bắt đầu thực hiện.
- `IN_PROGRESS`: Đang trong quá trình viết code và kiểm thử.
- `DONE`: Đã hoàn thành, vượt qua toàn bộ Acceptance Criteria và đã commit vào nhánh `develop`.

---

## 2. Bảng Theo Dõi Tiến Độ Thực Thi Chi Tiết (Master Task Tracker)

| Mã Task | Tên Task | Phase | Trạng Thái | Ngày Cập Nhật | Ghi Chú Kỹ Thuật / Trọng Tâm Spring Boot & MySQL / Lỗi Phát Sinh |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `TASK-01` | Khởi tạo dự án Spring Boot 3.3, Cấu hình chung & Global Exception Handling | Phase 1 | `TODO` | 2026-11-10 | Trọng tâm: Cấu hình Maven Java 21, `@EnableJpaAuditing`, `BaseAuditEntity`, `@RestControllerAdvice` chuẩn hóa `ApiErrorResponse`. |
| `TASK-02` | Thiết lập Flyway Migration 24 bảng, Seed Data & Sơ đồ ERD trên MySQL Workbench | Phase 1 | `TODO` | 2026-11-10 | Trọng tâm: Chạy `V1__init_omnilife_schema.sql` và `V2__seed_initial_data.sql`; thực hiện `Reverse Engineer` trên MySQL Workbench lưu file `.mwb`. |
| `TASK-03` | Hiện thực hóa Spring Security 6, Local Auth (BCrypt) & JWT Refresh Token Rotation | Phase 1 | `TODO` | 2026-11-10 | Trọng tâm: `SecurityFilterChain` Stateless, `BCryptPasswordEncoder(12)`, `OncePerRequestFilter`, cơ chế phát hiện đánh cắp Refresh Token. |
| `TASK-04` | Tích hợp Google OAuth2 Login, Tự động Liên kết Tài khoản & Quản lý User Preferences | Phase 1 | `TODO` | 2026-11-10 | Trọng tâm: `CustomOAuth2UserService`, Account Linking khi trùng Email giữa Local và Google, ánh xạ cột `JSON` trong MySQL 8. |
| `TASK-05` | Xây dựng CRUD Công việc, Subtasks, chống N+1 Query & Optimistic Locking (`@Version`) | Phase 2 | `TODO` | 2026-11-10 | Trọng tâm: Kiểm soát xung đột bằng `@Version` (`ObjectOptimisticLockingFailureException`), triệt tiêu N+1 Query bằng `@EntityGraph` và `Specification`. |
| `TASK-06` | Hiện thực hóa Động cơ Tính Điểm Khẩn Cấp Động (`UrgencyScoringEngine`) & `@Scheduled` Job | Phase 2 | `TODO` | 2026-11-10 | Trọng tâm: Công thức suy giảm thời gian phi tuyến tính, `@Scheduled` cập nhật điểm mỗi 15 phút, API `/api/v1/hud/summary` cho Mini HUD. |
| `TASK-07` | Xây dựng Phân hệ Lịch Biểu Hợp Nhất, Đồng bộ Google Calendar & Phiên Pomodoro | Phase 2 | `TODO` | 2026-11-10 | Trọng tâm: Truy vấn gộp 3 luồng lịch biểu với Index Range Scan `idx_cal_events_range`, cộng dồn thời gian tập trung trong `@Transactional`. |
| `TASK-08` | Quản lý Học kỳ, Môn học, Thời khóa biểu & Tiến trình Nhắc lịch học tự động | Phase 3 | `TODO` | 2026-11-10 | Trọng tâm: Thiết lập lịch học hàng tuần theo chuẩn `ISO-8601 DayOfWeek`, gửi thông báo nhắc lớp học trước 30 phút. |
| `TASK-09` | Bóc tách Tài liệu Học tập (PDF/DOCX/MD) & Thuật toán Ôn bài cũ Spaced Repetition SM-2 | Phase 3 | `TODO` | 2026-11-10 | Trọng tâm: Trích xuất mục lục giáo trình bằng Apache PDFBox; cài đặt thuật toán SM-2 tính `easiness_factor`, `interval_days` và `next_review_date`. |
| `TASK-10` | Xây dựng Hệ thống Đếm ngược Kỳ thi & Phân tích Độ Sẵn sàng (`ExamReadinessService`) | Phase 3 | `TODO` | 2026-11-10 | Trọng tâm: Tính toán phần trăm kiến thức đã sẵn sàng trước ngày thi và cảnh báo tốc độ ôn tập cần thiết mỗi ngày. |
| `TASK-11` | Xây dựng Sổ cái Tài chính với Pessimistic Write Lock, Ngân sách & Thống kê Dòng tiền | Phase 4 | `TODO` | 2026-11-10 | Trọng tâm: Khóa dòng `LockModeType.PESSIMISTIC_WRITE` (`SELECT ... FOR UPDATE`) trên bảng `wallets`, tính toán `BigDecimal` và cảnh báo ngân sách tháng. |
| `TASK-12` | Xây dựng Phân hệ Thói quen, Thể thao, Tính chuỗi Streak & Heatmap 365 ngày | Phase 4 | `TODO` | 2026-11-10 | Trọng tâm: Thuật toán tính chuỗi ngày liên tục (`current_streak`, `longest_streak`) và Covering Index `idx_habit_logs_heatmap`. |
| `TASK-13` | Xây dựng Nhật ký Cảm xúc, Spring AOP Audit Log & Trình quản lý Audio Presets | Phase 4 | `TODO` | 2026-11-10 | Trọng tâm: Custom Annotation `@TrackActivity` kết hợp Spring AOP `@AfterReturning` và `@TransactionalEventListener(phase = AFTER_COMMIT)`. |
| `TASK-14` | Khởi tạo Next.js 14 App Router, Design System Tokens, Auth Store & Axios Interceptor | Phase 5 | `TODO` | 2026-11-10 | Trọng tâm: Thiết lập Dark/Light Bento Theme, font `Inter` & `JetBrains Mono` (`tabular-nums`), tự động làm mới JWT ngầm trên Client. |
| `TASK-15` | Xây dựng Application Shell, Command Palette (`Ctrl + K`) & Thanh Nhạc Nền Đa Luồng | Phase 5 | `TODO` | 2026-11-10 | Trọng tâm: Điều khiển ứng dụng bằng phím tắt `Ctrl + K` và trộn nhiều luồng âm thanh Lo-Fi + White Noise qua Web Audio API. |
| `TASK-16` | Xây dựng Bảng Công Việc Điểm Khẩn Cấp Động, Slide-over Drawer & Lịch Tuần Kéo Thả | Phase 5 | `TODO` | 2026-11-10 | Trọng tâm: Hiển thị thẻ Task theo 5 cấp màu `UrgencyTier`, ngăn kéo chi tiết công việc theo ngày và kéo thả Time-blocking trên Lịch tuần. |
| `TASK-17` | Hiện thực hóa Cửa sổ nổi Always-on-Top Mini HUD bằng Document Picture-in-Picture API | Phase 5 | `TODO` | 2026-11-10 | Trọng tâm: Sử dụng `window.documentPictureInPicture` và `ReactDOM.createPortal` đồng bộ hai chiều thời gian thực giữa cửa sổ nổi và Web chính. |
| `TASK-18` | Xây dựng Giao diện Không Gian Học Tập (Study Workspace), Import Tài Liệu & Ôn Thi SM-2 | Phase 5 | `TODO` | 2026-11-10 | Trọng tâm: Giao diện tải đề cương PDF, thẻ đánh giá mức độ nhớ bài cũ (0-5) và thanh đếm ngược ngày thi trực quan. |
| `TASK-19` | Xây dựng Giao diện Quản Lý Tài Chính, Thói Quen Heatmap 365 Ngày & Nhật Ký Cảm Xúc | Phase 5 | `TODO` | 2026-11-10 | Trọng tâm: Biểu đồ dòng tiền Recharts, thanh cảnh báo ngân sách, ma trận nhiệt 52 tuần và trình soạn thảo Nhật ký Markdown kèm Daily Snapshot. |
| `TASK-20` | Tối ưu hóa Hiệu năng với Caffeine Cache, SQL Slow Query Aspect & Kiểm toán EXPLAIN | Phase 6 | `TODO` | 2026-11-10 | Trọng tâm: Cấu hình `@Cacheable` với Caffeine, tự động cảnh báo truy vấn chậm > 100ms và kiểm nghiệm kế hoạch thực thi trên MySQL Workbench. |
| `TASK-21` | Hoàn thiện Bộ Kiểm thử Tự động, Docker Compose & CI/CD Pipeline | Phase 6 | `TODO` | 2026-11-10 | Trọng tâm: Đóng gói Multi-stage Docker dưới 350MB RAM, chạy GitHub Actions CI với MySQL 8 Service Container và sẵn sàng triển khai lên Render & Vercel. |

---

## 3. Nhật Ký Quyết Định Kỹ Thuật & Xử Lý Vấn Đề (Architecture & Debugging Log)

| Ngày | Hạng Mục | Quyết Định Kỹ Thuật / Nguyên Nhân Gốc & Giải Pháp |
| :--- | :--- | :--- |
| 2026-11-10 | Khởi tạo bộ 14 file Spec | Chốt kiến trúc **Spring Boot 3.3 Modular Monolith + MySQL 8.0 + Next.js 14 + Document Picture-in-Picture API**. Đã loại bỏ tính năng theo dõi MXH bên thứ ba và thời tiết để tập trung tối đa vào chất lượng UI/UX và chiều sâu kỹ thuật của 8 phân hệ cốt lõi. |
