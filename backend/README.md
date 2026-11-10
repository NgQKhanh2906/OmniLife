# OmniLife Backend - Spring Boot 3.3 Modular Monolith

Thư mục này chứa toàn bộ mã nguồn máy chủ Backend của hệ thống **OmniLife**, được phát triển bằng **Java 21 LTS** và **Spring Boot 3.3.x** theo mô hình kiến trúc **Modular Monolith**.

## 1. Cấu Trúc Các Phân Hệ (Domain Modules)

- `common/`: Chứa cấu hình hạ tầng dùng chung (`AsyncConfig`, `CacheConfig`, `CorsConfig`, `JacksonConfig`, `OpenApiConfig`, `SchedulingConfig`), Spring AOP Aspects (`ActivityAuditAspect`, `SqlPerformanceAspect`), thực thể cơ sở `BaseAuditEntity`, cấu trúc phản hồi `ApiResponse`/`PageResponse` và xử lý ngoại lệ tập trung `GlobalExceptionHandler`.
- `security/`: Quản lý chuỗi bộ lọc `SecurityFilterChain`, xác thực JWT Stateless (`jwt/`) và đăng nhập một chạm qua Google OAuth2 kèm tự động liên kết tài khoản (`oauth2/`).
- `user/`: Quản lý tài khoản người dùng, xoay vòng Refresh Token và cấu hình cá nhân hóa (`user_preferences`).
- `productivity/`: Quản lý công việc (`Task`, `Subtask`), lịch biểu hợp nhất (`CalendarEvent`), phiên tập trung Pomodoro (`FocusSession`) và động cơ tính điểm khẩn cấp động (`UrgencyScoringEngine`).
- `study/`: Quản lý học kỳ, môn học, thời khóa biểu, bóc tách đề cương học tập từ file PDF/DOCX và thuật toán ôn tập lặp lại ngắt quãng Spaced Repetition SM-2 (`SpacedRepetitionSm2Service`).
- `finance/`: Quản lý sổ cái tài chính cá nhân (`Wallet`, `FinanceCategory`, `Transaction`, `Budget`) đảm bảo toàn vẹn số dư bằng khóa bi quan (`PESSIMISTIC_WRITE`).
- `habit/`: Quản lý mục tiêu thói quen, thể thao, tính chuỗi ngày liên tiếp (`StreakCalculationService`) và xuất dữ liệu Heatmap 365 ngày.
- `journal/`: Quản lý nhật ký Markdown hàng ngày, chỉ số tâm trạng và tổng hợp bản chụp hoạt động tự động (`DailySnapshotAggregatorService`).
- `audio/`: Quản lý danh sách cấu hình trộn âm thanh tập trung (`AudioPreset`).

## 2. Tài Liệu Tham Chiếu

- Đặc tả kiến trúc chi tiết: [../docs/architecture.md](../docs/architecture.md)
- Đặc tả cơ sở dữ liệu & Flyway: [../docs/database.md](../docs/database.md)
- Đặc tả RESTful API: [../docs/api.md](../docs/api.md)
