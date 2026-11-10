# Execution Roadmap & Agent Task Breakdown - OmniLife Personal OS

Lộ trình phát triển dự án **OmniLife** được chia thành **6 Giai đoạn (Phases)** tuần tự gồm **24 Task độc lập (`TASK-01` đến `TASK-24`)**. Mỗi Task được thiết kế để bạn hoặc Agent có thể thực thi trọn vẹn trong 1 đến 3 buổi làm việc, vừa hoàn thiện sản phẩm vừa nắm chắc từng cơ chế cốt lõi của Java Spring Boot 3, MySQL Workbench và Next.js 14.

---

## Phase 1: Khởi Tạo Hạ Tầng, CSDL MySQL Workbench & Bảo Mật Kép (Foundation & Auth)

### TASK-01: Khởi tạo dự án Spring Boot 3.3, Cấu hình chung & Global Exception Handling
- **File cần tạo/sửa:**
  - `backend/pom.xml`
  - `backend/src/main/resources/application.yml`
  - `backend/src/main/resources/application-local.yml`
  - `backend/src/main/java/com/omnilife/OmniLifeApplication.java`
  - `backend/src/main/java/com/omnilife/common/entity/BaseAuditEntity.java`
  - `backend/src/main/java/com/omnilife/common/response/ApiResponse.java`
  - `backend/src/main/java/com/omnilife/common/response/PageResponse.java`
  - `backend/src/main/java/com/omnilife/common/exception/ErrorCode.java`
  - `backend/src/main/java/com/omnilife/common/exception/BusinessException.java`
  - `backend/src/main/java/com/omnilife/common/exception/ResourceNotFoundException.java`
  - `backend/src/main/java/com/omnilife/common/exception/ApiErrorResponse.java`
  - `backend/src/main/java/com/omnilife/common/exception/GlobalExceptionHandler.java`
  - `backend/src/main/java/com/omnilife/common/config/OpenApiConfig.java`
  - `backend/src/main/java/com/omnilife/common/config/JacksonConfig.java`
- **Yêu cầu kỹ thuật:**
  - Khởi tạo Maven project với Java 21, Spring Boot 3.3.x, khai báo đủ dependencies: `spring-boot-starter-web`, `spring-boot-starter-data-jpa`, `spring-boot-starter-security`, `spring-boot-starter-oauth2-client`, `spring-boot-starter-validation`, `spring-boot-starter-aop`, `spring-boot-starter-mail`, `spring-boot-starter-cache`, `flyway-core`, `flyway-mysql`, `mysql-connector-j`, `springdoc-openapi-starter-webmvc-ui`, `pdfbox`, `lombok`.
  - Bật `@EnableJpaAuditing` để tự động điền `created_at` và `updated_at` trên `BaseAuditEntity`.
  - Trong `GlobalExceptionHandler` (`@RestControllerAdvice`), bắt và chuẩn hóa các lỗi `MethodArgumentNotValidException`, `ObjectOptimisticLockingFailureException`, `BusinessException`, `ResourceNotFoundException`.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Chạy lệnh `./mvnw clean compile` thành công với `0 errors`.
  - Truy cập `/swagger-ui.html` hiển thị trang tài liệu OpenAPI 3 của OmniLife.

### TASK-02: Thiết lập Flyway Migration 24 bảng, Seed Data & Sơ đồ ERD trên MySQL Workbench
- **File cần tạo/sửa:**
  - `backend/src/main/resources/db/migration/V1__init_omnilife_schema.sql`
  - `backend/src/main/resources/db/migration/V2__seed_initial_data.sql`
  - `database/explain_queries_benchmark.sql`
  - `database/index_tuning_verification.sql`
- **Yêu cầu kỹ thuật:**
  - Đưa toàn bộ DDL tạo 24 bảng và 8 Composite Indexes từ `docs/database.md` vào `V1__init_omnilife_schema.sql`.
  - Viết script nạp bộ dữ liệu mẫu từ `docs/assets.md` vào `V2__seed_initial_data.sql`.
  - Mở MySQL Workbench, chạy `Database -> Reverse Engineer` trên `omnilife_db` và lưu sơ đồ thực thể quan hệ vào `database/omnilife_erd_model.mwb`.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Khởi chạy Spring Boot, Flyway tự động thực thi `V1` và `V2` thành công, bảng `flyway_schema_history` ghi nhận 2 bản ghi `success = 1`.
  - Chạy `EXPLAIN` trong `database/explain_queries_benchmark.sql` trên MySQL Workbench xác nhận truy vấn bảng `tasks` sử dụng đúng index `idx_tasks_urgency_board` (`type: ref`, không có `Using filesort`).

### TASK-03: Hiện thực hóa Spring Security 6, Local Auth (BCrypt) & JWT Refresh Token Rotation
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/user/entity/User.java`
  - `backend/src/main/java/com/omnilife/user/entity/RefreshToken.java`
  - `backend/src/main/java/com/omnilife/user/entity/UserPreference.java`
  - `backend/src/main/java/com/omnilife/user/repository/UserRepository.java`
  - `backend/src/main/java/com/omnilife/user/repository/RefreshTokenRepository.java`
  - `backend/src/main/java/com/omnilife/security/CustomUserDetails.java`
  - `backend/src/main/java/com/omnilife/security/CustomUserDetailsService.java`
  - `backend/src/main/java/com/omnilife/security/CurrentUser.java`
  - `backend/src/main/java/com/omnilife/security/jwt/JwtTokenProvider.java`
  - `backend/src/main/java/com/omnilife/security/jwt/JwtAuthenticationFilter.java`
  - `backend/src/main/java/com/omnilife/security/jwt/JwtAuthenticationEntryPoint.java`
  - `backend/src/main/java/com/omnilife/user/service/RefreshTokenService.java`
  - `backend/src/main/java/com/omnilife/user/service/AuthService.java`
  - `backend/src/main/java/com/omnilife/user/controller/AuthController.java`
- **Yêu cầu kỹ thuật:**
  - Cấu hình `BCryptPasswordEncoder(12)`.
  - Hiện thực hóa các API `POST /api/v1/auth/register`, `POST /api/v1/auth/login`, `POST /api/v1/auth/refresh`, `POST /api/v1/auth/logout`.
  - Áp dụng cơ chế **Refresh Token Rotation**: Mỗi lần làm mới token, thu hồi token cũ (`is_revoked = true`) và cấp token mới. Nếu phát hiện một token đã bị thu hồi được gửi lại, lập tức thu hồi toàn bộ token của `user_id` đó.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Đăng nhập bằng tài khoản seed (`khanh.student@omnilife.local` / `OmniLife@2026`) nhận về `accessToken` hợp lệ và HttpOnly Cookie `omnilife_rt`.
  - Gọi API cần xác thực mà không truyền token trả về đúng `401 Unauthorized` với cấu trúc `ApiErrorResponse`.

### TASK-04: Tích hợp Google OAuth2 Login, Tự động Liên kết Tài khoản & Quản lý User Preferences
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/user/entity/OAuthAccount.java`
  - `backend/src/main/java/com/omnilife/user/repository/OAuthAccountRepository.java`
  - `backend/src/main/java/com/omnilife/security/oauth2/GoogleOAuth2UserInfo.java`
  - `backend/src/main/java/com/omnilife/security/oauth2/CustomOAuth2UserService.java`
  - `backend/src/main/java/com/omnilife/security/oauth2/OAuth2AuthenticationSuccessHandler.java`
  - `backend/src/main/java/com/omnilife/security/oauth2/OAuth2AuthenticationFailureHandler.java`
  - `backend/src/main/java/com/omnilife/security/SecurityConfig.java`
  - `backend/src/main/java/com/omnilife/user/service/UserProfileService.java`
  - `backend/src/main/java/com/omnilife/user/controller/UserProfileController.java`
- **Yêu cầu kỹ thuật:**
  - Trong `CustomOAuth2UserService`, kiểm tra email trả về từ Google: nếu đã tồn tại trong bảng `users`, tự động tạo bản ghi `OAuthAccount` gắn với `user.id` hiện có và chuyển `auth_provider = 'LINKED'`.
  - Hoàn thiện API `GET / PUT /api/v1/users/me/preferences` đọc và lưu cột `urgency_weights_json` kiểu `JSON` trong MySQL.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Luồng `/oauth2/authorization/google` chuyển hướng sang Google và callback về cấp phát JWT thành công mà không gây lỗi trùng Unique Email.

---

## Phase 2: Động Cơ Công Việc Ưu Tiên Động, Lịch Biểu & Kiểm Soát Đồng Thời (Core Productivity)

### TASK-05: Xây dựng CRUD Công việc, Subtasks, chống N+1 Query & Optimistic Locking (`@Version`)
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/productivity/entity/Task.java`
  - `backend/src/main/java/com/omnilife/productivity/entity/Subtask.java`
  - `backend/src/main/java/com/omnilife/productivity/entity/BasePriority.java`
  - `backend/src/main/java/com/omnilife/productivity/entity/TaskStatus.java`
  - `backend/src/main/java/com/omnilife/productivity/entity/UrgencyTier.java`
  - `backend/src/main/java/com/omnilife/productivity/repository/TaskRepository.java`
  - `backend/src/main/java/com/omnilife/productivity/repository/SubtaskRepository.java`
  - `backend/src/main/java/com/omnilife/productivity/repository/TaskSpecification.java`
  - `backend/src/main/java/com/omnilife/productivity/service/TaskService.java`
  - `backend/src/main/java/com/omnilife/productivity/controller/TaskController.java`
  - `backend/src/test/java/com/omnilife/productivity/TaskOptimisticLockingIntegrationTest.java`
- **Yêu cầu kỹ thuật:**
  - Gắn `@Version private Long version;` trong `Task.java`.
  - Sử dụng `@EntityGraph(attributePaths = {"subtasks", "course"})` khi lấy chi tiết Task để chỉ phát sinh đúng 1 câu lệnh SQL JOIN.
  - Viết Integration Test giả lập 2 luồng cùng cập nhật 1 Task với cùng `version = 0`: luồng thứ 2 bắt buộc ném `ObjectOptimisticLockingFailureException` và trả về HTTP `409 Conflict`.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Vượt qua 100% test cases trong `TaskOptimisticLockingIntegrationTest`.
  - Bật `spring.jpa.show-sql=true` xác nhận khi lấy danh sách 20 tasks không phát sinh 20 câu lệnh SELECT con (0 N+1 Query).

### TASK-06: Hiện thực hóa Động cơ Tính Điểm Khẩn Cấp Động (`UrgencyScoringEngine`) & `@Scheduled` Job
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/productivity/service/UrgencyScoringEngine.java`
  - `backend/src/main/java/com/omnilife/productivity/scheduler/TaskUrgencyRecalculationScheduler.java`
  - `backend/src/main/java/com/omnilife/common/config/SchedulingConfig.java`
  - `backend/src/main/java/com/omnilife/common/config/AsyncConfig.java`
  - `backend/src/test/java/com/omnilife/productivity/UrgencyScoringEngineTest.java`
- **Yêu cầu kỹ thuật:**
  - Cài đặt chính xác công thức toán học trong `docs/spec.md` mục 4.1:
    $$U = \min\left(100, \; B + D(t_{\text{now}}) + E + S\right)$$
  - Tự động tính lại `urgencyScore` và `urgencyTier` (`OVERDUE`, `EXTREME`, `HIGH`, `MODERATE`, `LOW`) ngay khi tạo/sửa Task và định kỳ mỗi 15 phút qua `@Scheduled(cron = "0 */15 * * * *")`.
  - Tạo API `GET /api/v1/hud/summary` trả về Top 3 công việc khẩn cấp nhất phục vụ cửa sổ nổi Mini HUD.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Viết Unit Test kiểm증 5 kịch bản biên (Task không có deadline, Task còn 7 ngày, Task còn 3 giờ, Task quá hạn 2 giờ, Task có khối lượng lớn hơn thời gian còn lại) trong `UrgencyScoringEngineTest` đạt `PASS 100%`.

### TASK-07: Xây dựng Phân hệ Lịch Biểu Hợp Nhất, Đồng bộ Google Calendar & Phiên Pomodoro
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/productivity/entity/CalendarEvent.java`
  - `backend/src/main/java/com/omnilife/productivity/entity/FocusSession.java`
  - `backend/src/main/java/com/omnilife/productivity/repository/CalendarEventRepository.java`
  - `backend/src/main/java/com/omnilife/productivity/repository/FocusSessionRepository.java`
  - `backend/src/main/java/com/omnilife/productivity/service/CalendarSyncService.java`
  - `backend/src/main/java/com/omnilife/productivity/service/FocusSessionService.java`
  - `backend/src/main/java/com/omnilife/productivity/controller/CalendarEventController.java`
  - `backend/src/main/java/com/omnilife/productivity/controller/FocusSessionController.java`
- **Yêu cầu kỹ thuật:**
  - Xây dựng API `GET /api/v1/calendar/unified-view` gộp chung Sự kiện lịch cá nhân, Sự kiện Google Calendar, Thời khóa biểu môn học và các Task đã được Time-block trong khoảng `startAt` -> `endAt`.
  - Khi hoàn thành 1 phiên Pomodoro (`POST /api/v1/focus-sessions`), tự động cộng dồn số phút vào `Task.actualFocusedMinutes` trong cùng một `@Transactional`.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Truy vấn lịch tuần trả về đầy đủ 3 loại sự kiện dưới `50ms` nhờ sử dụng Composite Index `idx_cal_events_range` và `idx_tasks_calendar_range`.

---

## Phase 3: Không Gian Học Tập Chuyên Sâu & Thuật Toán Spaced Repetition SM-2 (Study Workspace)

### TASK-08: Quản lý Học kỳ, Môn học, Thời khóa biểu & Tiến trình Nhắc lịch học tự động
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/study/entity/Semester.java`
  - `backend/src/main/java/com/omnilife/study/entity/Course.java`
  - `backend/src/main/java/com/omnilife/study/entity/ClassSchedule.java`
  - `backend/src/main/java/com/omnilife/study/repository/SemesterRepository.java`
  - `backend/src/main/java/com/omnilife/study/repository/CourseRepository.java`
  - `backend/src/main/java/com/omnilife/study/repository/ClassScheduleRepository.java`
  - `backend/src/main/java/com/omnilife/study/service/CourseService.java`
  - `backend/src/main/java/com/omnilife/study/scheduler/ClassReminderNotificationScheduler.java`
  - `backend/src/main/java/com/omnilife/study/controller/CourseController.java`
- **Yêu cầu kỹ thuật:**
  - CRUD Học kỳ, Môn học và Thời khóa biểu cố định trong tuần.
  - Cấu hình `ClassReminderNotificationScheduler` kiểm tra các lớp học sắp bắt đầu trong 30 phút tới để phát thông báo nhắc nhở.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Tạo mới môn học kèm 2 ca học trong tuần, dữ liệu lưu chính xác vào `courses` và `class_schedules` và hiển thị ngay trên Unified Calendar.

### TASK-09: Bóc tách Tài liệu Học tập (PDF/DOCX/MD) & Thuật toán Ôn bài cũ Spaced Repetition SM-2
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/study/entity/StudyMaterial.java`
  - `backend/src/main/java/com/omnilife/study/entity/StudyTopic.java`
  - `backend/src/main/java/com/omnilife/study/entity/StudyReviewLog.java`
  - `backend/src/main/java/com/omnilife/study/entity/MasteryLevel.java`
  - `backend/src/main/java/com/omnilife/study/repository/StudyMaterialRepository.java`
  - `backend/src/main/java/com/omnilife/study/repository/StudyTopicRepository.java`
  - `backend/src/main/java/com/omnilife/study/service/DocumentSyllabusExtractorService.java`
  - `backend/src/main/java/com/omnilife/study/service/SpacedRepetitionSm2Service.java`
  - `backend/src/main/java/com/omnilife/study/controller/StudyMaterialController.java`
  - `backend/src/main/java/com/omnilife/study/controller/StudyTopicReviewController.java`
  - `backend/src/test/java/com/omnilife/study/SpacedRepetitionSm2ServiceTest.java`
- **Yêu cầu kỹ thuật:**
  - Dùng Apache PDFBox trong `DocumentSyllabusExtractorService` để đọc văn bản từ file PDF tải lên, tách các dòng tiêu đề chương/bài (`Chương`, `Bài`, `Chapter`, `Topic`, `1.`, `2.`) thành danh sách `StudyTopic`.
  - Hiện thực hóa thuật toán SM-2 trong `SpacedRepetitionSm2Service`: tính toán `repetitionCount`, `easinessFactor` ($\ge 1.30$), `intervalDays` và `nextReviewDate` dựa trên điểm tự đánh giá $q \in [0, 5]$.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Unit Test `SpacedRepetitionSm2ServiceTest` kiểm chứng chuỗi đánh giá $q = 4 \rightarrow 4 \rightarrow 5 \rightarrow 2$ (quên bài) tính ra đúng chu kỳ $I = 1 \rightarrow 3 \rightarrow 8 \rightarrow 1$ ngày.

### TASK-10: Xây dựng Hệ thống Đếm ngược Kỳ thi & Phân tích Độ Sẵn sàng (`ExamReadinessService`)
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/study/entity/Exam.java`
  - `backend/src/main/java/com/omnilife/study/repository/ExamRepository.java`
  - `backend/src/main/java/com/omnilife/study/service/ExamReadinessService.java`
  - `backend/src/main/java/com/omnilife/study/controller/ExamController.java`
- **Yêu cầu kỹ thuật:**
  - Tính toán tỷ lệ `readinessPercentage` của từng kỳ thi dựa trên số lượng `StudyTopic` có `isExamScope = true` đã đạt mức `REVIEWING` hoặc `MASTERED`.
  - Tính tốc độ học khuyến nghị mỗi ngày (`recommendedTopicsPerDay = remainingTopics / daysRemaining`) và cảnh báo `AT_RISK` nếu khối lượng bài chưa học quá lớn so với số ngày còn lại.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - API `GET /api/v1/study/exams/countdown-readiness` trả về chính xác số ngày còn lại và tỷ lệ % sẵn sàng của các kỳ thi sắp tới.

---

## Phase 4: Sổ Cái Tài Chính, Thói Quen Thể Thao, Nhật Ký & Âm Thanh (Personal Balance Backend)

### TASK-11: Xây dựng Sổ cái Tài chính với Pessimistic Write Lock, Ngân sách & Thống kê Dòng tiền
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/finance/entity/Wallet.java`
  - `backend/src/main/java/com/omnilife/finance/entity/FinanceCategory.java`
  - `backend/src/main/java/com/omnilife/finance/entity/Transaction.java`
  - `backend/src/main/java/com/omnilife/finance/entity/Budget.java`
  - `backend/src/main/java/com/omnilife/finance/repository/WalletRepository.java`
  - `backend/src/main/java/com/omnilife/finance/repository/FinanceCategoryRepository.java`
  - `backend/src/main/java/com/omnilife/finance/repository/TransactionRepository.java`
  - `backend/src/main/java/com/omnilife/finance/repository/BudgetRepository.java`
  - `backend/src/main/java/com/omnilife/finance/service/FinanceLedgerService.java`
  - `backend/src/main/java/com/omnilife/finance/service/BudgetService.java`
  - `backend/src/main/java/com/omnilife/finance/controller/WalletController.java`
  - `backend/src/main/java/com/omnilife/finance/controller/TransactionController.java`
  - `backend/src/main/java/com/omnilife/finance/controller/BudgetController.java`
  - `backend/src/main/java/com/omnilife/finance/controller/FinanceAnalyticsController.java`
  - `backend/src/test/java/com/omnilife/finance/FinanceLedgerConcurrencyTest.java`
- **Yêu cầu kỹ thuật:**
  - Trong `WalletRepository`, định nghĩa phương thức `@Lock(LockModeType.PESSIMISTIC_WRITE) Optional<Wallet> findByIdAndUserIdForUpdate(Long id, Long userId)`.
  - Mọi phép tính tiền tệ sử dụng `BigDecimal` với `RoundingMode.HALF_UP`.
  - Tự động cập nhật `budgets.spent_amount` và trả về cảnh báo nếu vượt ngưỡng `alert_threshold_pct` (`80%` hoặc `100%`).
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Chạy `FinanceLedgerConcurrencyTest` giả lập 10 luồng đồng thời cùng trừ `50,000 VNĐ` trên một ví có số dư `1,000,000 VNĐ`: kết quả cuối cùng số dư ví chính xác tuyệt đối là `500,000.00 VNĐ` (không bị Lost Update).

### TASK-12: Xây dựng Phân hệ Thói quen, Thể thao, Tính chuỗi Streak & Heatmap 365 ngày
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/habit/entity/Habit.java`
  - `backend/src/main/java/com/omnilife/habit/entity/HabitLog.java`
  - `backend/src/main/java/com/omnilife/habit/repository/HabitRepository.java`
  - `backend/src/main/java/com/omnilife/habit/repository/HabitLogRepository.java`
  - `backend/src/main/java/com/omnilife/habit/service/StreakCalculationService.java`
  - `backend/src/main/java/com/omnilife/habit/service/HabitService.java`
  - `backend/src/main/java/com/omnilife/habit/controller/HabitController.java`
- **Yêu cầu kỹ thuật:**
  - Hỗ trợ cả thói quen kiểu `BOOLEAN` và `NUMERIC` (so sánh `achieved_value >= target_value`).
  - Thuật toán tính `currentStreak` duyệt ngược các ngày liên tiếp tính từ hôm nay (hoặc hôm qua) và cập nhật `longestStreak` nếu phá kỷ lục.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - API `POST /api/v1/habits/{id}/check-in` cập nhật đúng chuỗi Streak và API `/api/v1/habits/heatmap` trả về dữ liệu 365 ngày.

### TASK-13: Xây dựng Nhật ký Cảm xúc, Spring AOP Audit Log & Trình quản lý Audio Presets
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/common/annotation/TrackActivity.java`
  - `backend/src/main/java/com/omnilife/common/aspect/ActivityAuditAspect.java`
  - `backend/src/main/java/com/omnilife/journal/entity/JournalEntry.java`
  - `backend/src/main/java/com/omnilife/journal/repository/JournalEntryRepository.java`
  - `backend/src/main/java/com/omnilife/journal/service/DailySnapshotAggregatorService.java`
  - `backend/src/main/java/com/omnilife/journal/service/JournalService.java`
  - `backend/src/main/java/com/omnilife/journal/controller/JournalController.java`
  - `backend/src/main/java/com/omnilife/audio/entity/AudioPreset.java`
  - `backend/src/main/java/com/omnilife/audio/repository/AudioPresetRepository.java`
  - `backend/src/main/java/com/omnilife/audio/service/AudioPresetService.java`
  - `backend/src/main/java/com/omnilife/audio/controller/AudioPresetController.java`
- **Yêu cầu kỹ thuật:**
  - Dùng Spring AOP `@AfterReturning` trên annotation `@TrackActivity`结合 `ApplicationEventPublisher` để tự động ghi log mỗi khi người dùng hoàn thành Task, ôn xong Topic, ghi nhận Chi tiêu hoặc Check-in Thói quen.
  - Tổng hợp tự động `DailyActivitySnapshotDto` khi người dùng mở hoặc lưu trang Nhật ký của ngày đó.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Hoàn thành 1 Task ở module `productivity` -> gọi `GET /api/v1/journals/by-date` thấy ngay tên Task đó nằm trong mảng `dailySnapshot.completedTaskTitles`.

---

## Phase 5: Xây Dựng Giao Diện Next.js 14 Bento Grid, Lịch, Học Tập & Cửa Sổ Nổi Mini HUD (Frontend Experience)

### TASK-14: Khởi tạo Next.js 14 App Router, Design System Tokens, Auth Store & Axios Interceptor
- **File cần tạo/sửa:**
  - `frontend/package.json`
  - `frontend/tailwind.config.ts`
  - `frontend/src/app/globals.css`
  - `frontend/src/lib/api-client.ts`
  - `frontend/src/stores/authStore.ts`
  - `frontend/src/app/(auth)/login/page.tsx`
  - `frontend/src/app/(auth)/register/page.tsx`
  - `frontend/src/app/(auth)/oauth2/callback/page.tsx`
- **Yêu cầu kỹ thuật:**
  - Cấu hình bảng màu Dark/Light Mode và font chữ `Inter` + `JetBrains Mono` (`tabular-nums`) từ `docs/design-system.md`.
  - Cấu hình Axios Response Interceptor tự động bắt lỗi `401 AUTH_TOKEN_EXPIRED`, gọi `/api/v1/auth/refresh` ngầm và thực thi lại các request đang chờ (Request Queueing).
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Đăng nhập bằng Email hoặc Google OAuth2 chuyển hướng mượt mà vào `/dashboard` và tự động làm mới token khi hết hạn.

### TASK-15: Xây dựng Application Shell, Command Palette (`Ctrl + K`) & Thanh Nhạc Nền Đa Luồng (`AmbientAudioPlayerBar`)
- **File cần tạo/sửa:**
  - `frontend/src/app/(workspace)/layout.tsx`
  - `frontend/src/components/layout/AppSidebar.tsx`
  - `frontend/src/components/layout/TopCommandHeader.tsx`
  - `frontend/src/components/layout/CommandPaletteModal.tsx`
  - `frontend/src/components/layout/AmbientAudioPlayerBar.tsx`
  - `frontend/src/hooks/useAmbientAudioMixer.ts`
  - `frontend/src/stores/audioMixerStore.ts`
- **Yêu cầu kỹ thuật:**
  - Xây dựng Sidebar thu phóng, Header điều khiển và hộp thoại lệnh nhanh `Ctrl + K` (cho phép tạo nhanh Task, ghi nhanh Chi tiêu, bật Pomodoro, đổi bài nhạc bằng bàn phím).
  - Xây dựng bộ trộn âm thanh `useAmbientAudioMixer.ts` điều khiển độc lập 1 luồng nhạc Lo-Fi và 5 luồng âm thanh trắng chạy xuyên suốt ứng dụng không bị ngắt khi chuyển trang.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Bật nhạc Lo-Fi kết hợp tiếng mưa và chuyển qua lại giữa các trang `/dashboard`, `/study`, `/finance` mà âm thanh không bị khựng hay khởi động lại.

### TASK-16: Xây dựng Bảng Công Việc Điểm Khẩn Cấp Động, Slide-over Drawer & Lịch Tuần Kéo Thả
- **File cần tạo/sửa:**
  - `frontend/src/app/(workspace)/dashboard/page.tsx`
  - `frontend/src/app/(workspace)/calendar/page.tsx`
  - `frontend/src/components/productivity/DynamicUrgencyTaskList.tsx`
  - `frontend/src/components/productivity/UrgencyTaskCard.tsx`
  - `frontend/src/components/productivity/TaskDetailSlideDrawer.tsx`
  - `frontend/src/components/productivity/WeeklyCalendarGrid.tsx`
  - `frontend/src/hooks/useUrgencyLiveTick.ts`
  - `frontend/src/lib/urgency-calculator.ts`
- **Yêu cầu kỹ thuật:**
  - Hiển thị danh sách công việc với viền màu theo 5 cấp `UrgencyTier`, huy hiệu điểm khẩn cấp và đồng hồ đếm ngược thời gian còn lại tới Deadline.
  - Khi bấm vào ngày trên Lịch hoặc bấm vào Task, mở `TaskDetailSlideDrawer` hiển thị đầy đủ mô tả Markdown, Subtasks và lịch sử Pomodoro.
  - Cho phép kéo thả Task vào khung giờ trên `WeeklyCalendarGrid` để ấn định lịch làm việc.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Khi tạo Task có hạn chót dưới 4 giờ, thẻ Task tự động nhảy lên đầu danh sách với viền màu Cam/Đỏ và hiển thị tooltip giải thích chi tiết điểm số.

### TASK-17: Hiện thực hóa Cửa sổ nổi Always-on-Top Mini HUD bằng Document Picture-in-Picture API
- **File cần tạo/sửa:**
  - `frontend/src/components/hud/DocumentPipProvider.tsx`
  - `frontend/src/components/hud/StickyMiniHudWindow.tsx`
  - `frontend/src/components/hud/PomodoroMiniTimer.tsx`
  - `frontend/src/hooks/useDocumentPip.ts`
  - `frontend/src/hooks/usePomodoroTimer.ts`
  - `frontend/src/stores/hudStore.ts`
- **Yêu cầu kỹ thuật:**
  - Sử dụng `window.documentPictureInPicture.requestWindow({ width: 340, height: 460 })` và `ReactDOM.createPortal` để mở cửa sổ nổi luôn ghim trên màn hình máy tính.
  - Sao chép toàn bộ CSS Stylesheets sang cửa sổ PiP để giữ nguyên giao diện Dark Bento sắc nét.
  - Tích hợp cơ chế Fallback: nếu trình duyệt không hỗ trợ Document PiP API, hiển thị khung nổi thu nhỏ ở góc phải màn hình.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Bấm "Detach Mini HUD", thu nhỏ trình duyệt Chrome xuống Taskbar và mở Word/VS Code toàn màn hình: cửa sổ Mini HUD vẫn nổi trên góc màn hình, tick xong Task trên Mini HUD thì trạng thái cập nhật ngay xuống MySQL.

### TASK-18: Xây dựng Giao diện Không Gian Học Tập (Study Workspace), Import Tài Liệu & Ôn Thi SM-2
- **File cần tạo/sửa:**
  - `frontend/src/app/(workspace)/study/page.tsx`
  - `frontend/src/app/(workspace)/study/courses/[courseId]/page.tsx`
  - `frontend/src/app/(workspace)/study/review/page.tsx`
  - `frontend/src/components/study/CourseBentoCard.tsx`
  - `frontend/src/components/study/SyllabusUploaderModal.tsx`
  - `frontend/src/components/study/SpacedRepetitionReviewDeck.tsx`
  - `frontend/src/components/study/ExamCountdownBanner.tsx`
  - `frontend/src/components/study/WeeklyClassTimetable.tsx`
- **Yêu cầu kỹ thuật:**
  - Giao diện quản lý môn học, thời khóa biểu tuần và thanh đếm ngược ngày thi kèm thanh phần trăm sẵn sàng (`Readiness %`).
  - Modal tải lên file PDF đề cương, cho phép xem trước và chỉnh sửa danh sách các chủ đề (`StudyTopic`) vừa bóc tách trước khi lưu.
  - Màn hình ôn bài cũ (`SpacedRepetitionReviewDeck`) với 4 nút tự đánh giá mức độ nhớ kèm số ngày hẹn ôn lại tiếp theo.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Tải lên file PDF mẫu, hệ thống trích xuất danh sách chương mục, bấm ôn tập và chọn mức độ nhớ `4 - Nhớ tốt`, chủ đề tự động cập nhật ngày ôn tiếp theo.

### TASK-19: Xây dựng Giao diện Quản Lý Tài Chính, Thói Quen Heatmap 365 Ngày & Nhật Ký Cảm Xúc
- **File cần tạo/sửa:**
  - `frontend/src/app/(workspace)/finance/page.tsx`
  - `frontend/src/app/(workspace)/habits/page.tsx`
  - `frontend/src/app/(workspace)/journal/page.tsx`
  - `frontend/src/components/finance/WalletBalanceCards.tsx`
  - `frontend/src/components/finance/BudgetCeilingProgressList.tsx`
  - `frontend/src/components/finance/TransactionQuickForm.tsx`
  - `frontend/src/components/finance/CashflowAnalyticsCharts.tsx`
  - `frontend/src/components/habit/DailyHabitCheckInGrid.tsx`
  - `frontend/src/components/habit/HabitContributionHeatmap.tsx`
  - `frontend/src/components/journal/MarkdownJournalEditor.tsx`
  - `frontend/src/components/journal/MoodScoreSelector.tsx`
  - `frontend/src/components/journal/DailyActivitySnapshotCard.tsx`
- **Yêu cầu kỹ thuật:**
  - Trang Tài chính: Hiển thị số dư các ví, biểu đồ tròn cơ cấu chi tiêu bằng Recharts và thanh cảnh báo ngân sách tháng đổi màu Xanh/Vàng/Đỏ.
  - Trang Thói quen: Bảng điểm danh hàng ngày, huy hiệu chuỗi Streak và biểu đồ nhiệt 52 tuần x 7 ngày.
  - Trang Nhật ký: Trình soạn thảo Markdown kết hợp thẻ chọn chỉ số tâm trạng 1-5 và khung `DailyActivitySnapshotCard` tự động liệt kê các việc đã làm trong ngày.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Ghi nhận khoản chi vượt 80% ngân sách làm thanh tiến trình chuyển sang màu Vàng cảnh báo ngay lập tức; trang Nhật ký hiển thị đúng số tiền đã chi và số task đã xong trong ngày.

---

## Phase 6: Kiểm Thử Toàn Diện, Tối Ưu Hóa Hiệu Năng MySQL & Triển Khai (QA & Production)

### TASK-20: Tối ưu hóa Hiệu năngด้วย Caffeine Cache, SQL Slow Query Aspect & Kiểm toán EXPLAIN
- **File cần tạo/sửa:**
  - `backend/src/main/java/com/omnilife/common/config/CacheConfig.java`
  - `backend/src/main/java/com/omnilife/common/aspect/SqlPerformanceAspect.java`
  - `database/index_tuning_verification.sql`
- **Yêu cầu kỹ thuật:**
  - Tích hợp Caffeine Cache (`@Cacheable`, `@CacheEvict`) cho cấu hình `user_preferences`, danh sách môn học và danh mục tài chính.
  - Viết `SqlPerformanceAspect` đo thời gian chạy của toàn bộ Repository methods, ghi log `WARN` nếu vượt quá `100ms`.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Thời gian phản hồi của API Dashboard và Mini HUD đạt dưới `50ms` ở lần gọi thứ hai nhờ Cache và Covering Index.

### TASK-21: Hoàn thiện Bộ Kiểm thử Tự động, Docker Compose & CI/CD Pipeline
- **File cần tạo/sửa:**
  - `docker-compose.yml`
  - `backend/Dockerfile`
  - `.github/workflows/ci-omnilife.yml`
- **Yêu cầu kỹ thuật:**
  - Kiểm tra toàn bộ Unit Tests & Integration Tests của Backend (`./mvnw clean verify`) và quá trình đóng gói Production của Frontend (`npm run build`).
  - Khởi chạy thử toàn bộ hệ thống bằng `docker compose up --build`.
- **Tiêu chí nghiệm thu (Acceptance Criteria):**
  - Toàn bộ test suites đạt `100% PASS`, container Backend tiêu thụ dưới `350MB RAM` (nằm gọn trong giới hạn 512MB của Render Free-tier).
