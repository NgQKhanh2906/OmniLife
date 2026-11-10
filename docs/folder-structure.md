# Project Folder Structure (Skeleton Specification) - OmniLife Personal OS

Dự án được tổ chức theo mô hình **Monorepo** gồm 4 khu vực tách biệt rõ ràng: `backend/` (Java 21 + Spring Boot 3 theo kiến trúc Modular Monolith), `frontend/` (Next.js 14 App Router + TypeScript), `database/` (File mô hình MySQL Workbench `.mwb` và các script kiểm thử hiệu năng SQL), và `docs/` (14 file đặc tả hệ thống).

---

## 1. Sơ Đồ Cây Thư Mục Toàn Dự Án (Full File Tree)

```text
omnilife/
├── docker-compose.yml
├── .gitignore
├── README.md
├── docs/
│   ├── README.md
│   ├── product.md
│   ├── spec.md
│   ├── design-system.md
│   ├── assets.md
│   ├── folder-structure.md
│   ├── architecture.md
│   ├── database.md
│   ├── api.md
│   ├── git-workflow.md
│   ├── github-setup.md
│   ├── deploy.md
│   ├── roadmap.md
│   └── worklog.md
├── database/
│   ├── omnilife_erd_model.mwb
│   ├── explain_queries_benchmark.sql
│   └── index_tuning_verification.sql
├── backend/
│   ├── pom.xml
│   ├── Dockerfile
│   ├── .env.example
│   ├── mvnw
│   ├── mvnw.cmd
│   └── src/
│       ├── main/
│       │   ├── java/com/omnilife/
│       │   │   ├── OmniLifeApplication.java
│       │   │   ├── common/
│       │   │   │   ├── annotation/
│       │   │   │   │   └── TrackActivity.java
│       │   │   │   ├── aspect/
│       │   │   │   │   ├── ActivityAuditAspect.java
│       │   │   │   │   └── SqlPerformanceAspect.java
│       │   │   │   ├── config/
│       │   │   │   │   ├── AsyncConfig.java
│       │   │   │   │   ├── CacheConfig.java
│       │   │   │   │   ├── CorsConfig.java
│       │   │   │   │   ├── JacksonConfig.java
│       │   │   │   │   ├── OpenApiConfig.java
│       │   │   │   │   └── SchedulingConfig.java
│       │   │   │   ├── entity/
│       │   │   │   │   └── BaseAuditEntity.java
│       │   │   │   ├── exception/
│       │   │   │   │   ├── ApiErrorResponse.java
│       │   │   │   │   ├── BusinessException.java
│       │   │   │   │   ├── ErrorCode.java
│       │   │   │   │   ├── GlobalExceptionHandler.java
│       │   │   │   │   └── ResourceNotFoundException.java
│       │   │   │   └── response/
│       │   │   │       ├── ApiResponse.java
│       │   │   │       └── PageResponse.java
│       │   │   ├── security/
│       │   │   │   ├── SecurityConfig.java
│       │   │   │   ├── CustomUserDetails.java
│       │   │   │   ├── CustomUserDetailsService.java
│       │   │   │   ├── CurrentUser.java
│       │   │   │   ├── jwt/
│       │   │   │   │   ├── JwtAuthenticationEntryPoint.java
│       │   │   │   │   ├── JwtAuthenticationFilter.java
│       │   │   │   │   └── JwtTokenProvider.java
│       │   │   │   └── oauth2/
│       │   │   │       ├── CustomOAuth2UserService.java
│       │   │   │       ├── GoogleOAuth2UserInfo.java
│       │   │   │       ├── OAuth2AuthenticationFailureHandler.java
│       │   │   │       └── OAuth2AuthenticationSuccessHandler.java
│       │   │   ├── user/
│       │   │   │   ├── controller/
│       │   │   │   │   ├── AuthController.java
│       │   │   │   │   └── UserProfileController.java
│       │   │   │   ├── dto/
│       │   │   │   │   ├── AuthResponse.java
│       │   │   │   │   ├── LoginRequest.java
│       │   │   │   │   ├── RegisterRequest.java
│       │   │   │   │   ├── TokenRefreshRequest.java
│       │   │   │   │   ├── UserPreferenceDto.java
│       │   │   │   │   └── UserProfileResponse.java
│       │   │   │   ├── entity/
│       │   │   │   │   ├── OAuthAccount.java
│       │   │   │   │   ├── RefreshToken.java
│       │   │   │   │   ├── User.java
│       │   │   │   │   └── UserPreference.java
│       │   │   │   ├── repository/
│       │   │   │   │   ├── OAuthAccountRepository.java
│       │   │   │   │   ├── RefreshTokenRepository.java
│       │   │   │   │   └── UserRepository.java
│       │   │   │   └── service/
│       │   │   │       ├── AuthService.java
│       │   │   │       ├── RefreshTokenService.java
│       │   │   │       └── UserProfileService.java
│       │   │   ├── productivity/
│       │   │   │   ├── controller/
│       │   │   │   │   ├── CalendarEventController.java
│       │   │   │   │   ├── FocusSessionController.java
│       │   │   │   │   └── TaskController.java
│       │   │   │   ├── dto/
│       │   │   │   │   ├── CalendarEventDto.java
│       │   │   │   │   ├── FocusSessionDto.java
│       │   │   │   │   ├── MiniHudPayloadDto.java
│       │   │   │   │   ├── SubtaskDto.java
│       │   │   │   │   ├── TaskCreateRequest.java
│       │   │   │   │   ├── TaskDetailResponse.java
│       │   │   │   │   ├── TaskFilterRequest.java
│       │   │   │   │   └── TaskUpdateRequest.java
│       │   │   │   ├── entity/
│       │   │   │   │   ├── BasePriority.java
│       │   │   │   │   ├── CalendarEvent.java
│       │   │   │   │   ├── FocusSession.java
│       │   │   │   │   ├── Subtask.java
│       │   │   │   │   ├── Task.java
│       │   │   │   │   ├── TaskStatus.java
│       │   │   │   │   └── UrgencyTier.java
│       │   │   │   ├── event/
│       │   │   │   │   ├── TaskCompletedEvent.java
│       │   │   │   │   └── TaskCompletedEventListener.java
│       │   │   │   ├── repository/
│       │   │   │   │   ├── CalendarEventRepository.java
│       │   │   │   │   ├── FocusSessionRepository.java
│       │   │   │   │   ├── SubtaskRepository.java
│       │   │   │   │   ├── TaskRepository.java
│       │   │   │   │   └── TaskSpecification.java
│       │   │   │   ├── scheduler/
│       │   │   │   │   └── TaskUrgencyRecalculationScheduler.java
│       │   │   │   └── service/
│       │   │   │       ├── CalendarSyncService.java
│       │   │   │       ├── FocusSessionService.java
│       │   │   │       ├── TaskService.java
│       │   │   │       └── UrgencyScoringEngine.java
│       │   │   ├── study/
│       │   │   │   ├── controller/
│       │   │   │   │   ├── CourseController.java
│       │   │   │   │   ├── ExamController.java
│       │   │   │   │   ├── StudyMaterialController.java
│       │   │   │   │   └── StudyTopicReviewController.java
│       │   │   │   ├── dto/
│       │   │   │   │   ├── ClassScheduleDto.java
│       │   │   │   │   ├── CourseDto.java
│       │   │   │   │   ├── ExamReadinessResponse.java
│       │   │   │   │   ├── SpacedRepetitionSubmitRequest.java
│       │   │   │   │   ├── StudyMaterialUploadResponse.java
│       │   │   │   │   └── StudyTopicDto.java
│       │   │   │   ├── entity/
│       │   │   │   │   ├── ClassSchedule.java
│       │   │   │   │   ├── Course.java
│       │   │   │   │   ├── Exam.java
│       │   │   │   │   ├── MasteryLevel.java
│       │   │   │   │   ├── Semester.java
│       │   │   │   │   ├── StudyMaterial.java
│       │   │   │   │   ├── StudyReviewLog.java
│       │   │   │   │   └── StudyTopic.java
│       │   │   │   ├── repository/
│       │   │   │   │   ├── ClassScheduleRepository.java
│       │   │   │   │   ├── CourseRepository.java
│       │   │   │   │   ├── ExamRepository.java
│       │   │   │   │   ├── SemesterRepository.java
│       │   │   │   │   ├── StudyMaterialRepository.java
│       │   │   │   │   └── StudyTopicRepository.java
│       │   │   │   ├── scheduler/
│       │   │   │   │   └── ClassReminderNotificationScheduler.java
│       │   │   │   └── service/
│       │   │   │       ├── CourseService.java
│       │   │   │       ├── DocumentSyllabusExtractorService.java
│       │   │   │       ├── ExamReadinessService.java
│       │   │   │       └── SpacedRepetitionSm2Service.java
│       │   │   ├── finance/
│       │   │   │   ├── controller/
│       │   │   │   │   ├── BudgetController.java
│       │   │   │   │   ├── FinanceAnalyticsController.java
│       │   │   │   │   ├── TransactionController.java
│       │   │   │   │   └── WalletController.java
│       │   │   │   ├── dto/
│       │   │   │   │   ├── BudgetStatusResponse.java
│       │   │   │   │   ├── MonthlyCashflowSummaryDto.java
│       │   │   │   │   ├── TransactionCreateRequest.java
│       │   │   │   │   ├── TransactionResponse.java
│       │   │   │   │   └── WalletDto.java
│       │   │   │   ├── entity/
│       │   │   │   │   ├── Budget.java
│       │   │   │   │   ├── FinanceCategory.java
│       │   │   │   │   ├── Transaction.java
│       │   │   │   │   ├── TransactionType.java
│       │   │   │   │   └── Wallet.java
│       │   │   │   ├── repository/
│       │   │   │   │   ├── BudgetRepository.java
│       │   │   │   │   ├── FinanceCategoryRepository.java
│       │   │   │   │   ├── TransactionRepository.java
│       │   │   │   │   └── WalletRepository.java
│       │   │   │   └── service/
│       │   │   │       ├── BudgetService.java
│       │   │   │       ├── FinanceLedgerService.java
│       │   │   │       └── WalletService.java
│       │   │   ├── habit/
│       │   │   │   ├── controller/
│       │   │   │   │   └── HabitController.java
│       │   │   │   ├── dto/
│       │   │   │   │   ├── HabitCheckInRequest.java
│       │   │   │   │   ├── HabitDto.java
│       │   │   │   │   └── HabitHeatmapResponse.java
│       │   │   │   ├── entity/
│       │   │   │   │   ├── Habit.java
│       │   │   │   │   ├── HabitLog.java
│       │   │   │   │   └── HabitMetricType.java
│       │   │   │   ├── repository/
│       │   │   │   │   ├── HabitLogRepository.java
│       │   │   │   │   └── HabitRepository.java
│       │   │   │   └── service/
│       │   │   │       ├── HabitService.java
│       │   │   │       └── StreakCalculationService.java
│       │   │   ├── journal/
│       │   │   │   ├── controller/
│       │   │   │   │   └── JournalController.java
│       │   │   │   ├── dto/
│       │   │   │   │   ├── DailyActivitySnapshotDto.java
│       │   │   │   │   ├── JournalEntryRequest.java
│       │   │   │   │   ├── JournalEntryResponse.java
│       │   │   │   │   └── MoodTrendResponse.java
│       │   │   │   ├── entity/
│       │   │   │   │   └── JournalEntry.java
│       │   │   │   ├── repository/
│       │   │   │   │   └── JournalEntryRepository.java
│       │   │   │   └── service/
│       │   │   │       ├── DailySnapshotAggregatorService.java
│       │   │   │       └── JournalService.java
│       │   │   └── audio/
│       │   │       ├── controller/
│       │   │       │   └── AudioPresetController.java
│       │   │       ├── dto/
│       │   │       │   └── AudioPresetDto.java
│       │   │       ├── entity/
│       │   │       │   └── AudioPreset.java
│       │   │       ├── repository/
│       │   │       │   └── AudioPresetRepository.java
│       │   │       └── service/
│       │   │           └── AudioPresetService.java
│       │   └── resources/
│       │       ├── application.yml
│       │       ├── application-local.yml
│       │       ├── application-prod.yml
│       │       └── db/migration/
│       │           ├── V1__init_omnilife_schema.sql
│       │           └── V2__seed_initial_data.sql
│       └── test/
│           └── java/com/omnilife/
│               ├── productivity/
│               │   ├── UrgencyScoringEngineTest.java
│               │   └── TaskOptimisticLockingIntegrationTest.java
│               ├── study/
│               │   └── SpacedRepetitionSm2ServiceTest.java
│               └── finance/
│                   └── FinanceLedgerConcurrencyTest.java
└── frontend/
    ├── package.json
    ├── tsconfig.json
    ├── tailwind.config.ts
    ├── next.config.mjs
    ├── .env.example
    ├── public/
    │   ├── audio/
    │   │   ├── lofi/
    │   │   ├── ambient/
    │   │   └── sfx/
    │   └── images/
    └── src/
        ├── app/
        │   ├── layout.tsx
        │   ├── page.tsx
        │   ├── (auth)/
        │   │   ├── login/page.tsx
        │   │   ├── register/page.tsx
        │   │   └── oauth2/callback/page.tsx
        │   └── (workspace)/
        │       ├── layout.tsx
        │       ├── dashboard/page.tsx
        │       ├── calendar/page.tsx
        │       ├── study/
        │       │   ├── page.tsx
        │       │   ├── courses/[courseId]/page.tsx
        │       │   └── review/page.tsx
        │       ├── finance/page.tsx
        │       ├── habits/page.tsx
        │       ├── journal/page.tsx
        │       └── settings/page.tsx
        ├── components/
        │   ├── layout/
        │   │   ├── AppSidebar.tsx
        │   │   ├── TopCommandHeader.tsx
        │   │   ├── CommandPaletteModal.tsx
        │   │   └── AmbientAudioPlayerBar.tsx
        │   ├── hud/
        │   │   ├── DocumentPipProvider.tsx
        │   │   ├── StickyMiniHudWindow.tsx
        │   │   └── PomodoroMiniTimer.tsx
        │   ├── productivity/
        │   │   ├── WeeklyCalendarGrid.tsx
        │   │   ├── DynamicUrgencyTaskList.tsx
        │   │   ├── UrgencyTaskCard.tsx
        │   │   └── TaskDetailSlideDrawer.tsx
        │   ├── study/
        │   │   ├── CourseBentoCard.tsx
        │   │   ├── SyllabusUploaderModal.tsx
        │   │   ├── SpacedRepetitionReviewDeck.tsx
        │   │   ├── ExamCountdownBanner.tsx
        │   │   └── WeeklyClassTimetable.tsx
        │   ├── finance/
        │   │   ├── WalletBalanceCards.tsx
        │   │   ├── BudgetCeilingProgressList.tsx
        │   │   ├── TransactionQuickForm.tsx
        │   │   └── CashflowAnalyticsCharts.tsx
        │   ├── habit/
        │   │   ├── DailyHabitCheckInGrid.tsx
        │   │   ├── StreakCounterBadge.tsx
        │   │   └── HabitContributionHeatmap.tsx
        │   └── journal/
        │       ├── MarkdownJournalEditor.tsx
        │       ├── MoodScoreSelector.tsx
        │       └── DailyActivitySnapshotCard.tsx
        ├── hooks/
        │   ├── useDocumentPip.ts
        │   ├── usePomodoroTimer.ts
        │   ├── useAmbientAudioMixer.ts
        │   └── useUrgencyLiveTick.ts
        ├── lib/
        │   ├── api-client.ts
        │   ├── urgency-calculator.ts
        │   └── utils.ts
        ├── stores/
        │   ├── authStore.ts
        │   ├── hudStore.ts
        │   └── audioMixerStore.ts
        └── types/
            ├── productivity.ts
            ├── study.ts
            ├── finance.ts
            ├── habit.ts
            └── journal.ts
```

---

## 2. Giải Thích Trách Nhiệm Từng Khu Vực & Tệp Cốt Lõi

### 2.1. Khu vực `database/` (MySQL Workbench Artifacts)
- `omnilife_erd_model.mwb`: Tệp thiết kế sơ đồ thực thể quan hệ (Enhanced Entity-Relationship Diagram) mở trực tiếp bằng MySQL Workbench.
- `explain_queries_benchmark.sql`: Tập hợp các câu lệnh `EXPLAIN FORMAT=JSON` để bạn thực hành kiểm tra hiệu năng truy vấn bảng xếp hạng công việc, lịch tuần và sổ cái tài chính trên MySQL Workbench.
- `index_tuning_verification.sql`: Kịch bản tạo dữ liệu lớn và đối chiếu tốc độ truy vấn trước/sau khi đánh Composite Index.

### 2.2. Khu vực `backend/src/main/java/com/omnilife/` (Spring Boot 3 Modular Monolith)
- **`common/`**: Chứa các thành phần hạ tầng dùng chung toàn hệ thống:
  - `ActivityAuditAspect.java`: Sử dụng Spring AOP (`@Around`, `@AfterReturning`) tự động ghi nhận hoạt động của người dùng để tổng hợp vào trang Nhật ký cuối ngày.
  - `SqlPerformanceAspect.java`: Đo thời gian thực thi của các phương thức Repository và cảnh báo ra log nếu truy vấn chạy quá `100ms`.
  - `GlobalExceptionHandler.java`: Bắt toàn bộ ngoại lệ (`MethodArgumentNotValidException`, `OptimisticLockingFailureException`, `BusinessException`) và chuẩn hóa thành cấu trúc JSON thống nhất.
- **`security/`**: Quản lý chuỗi bộ lọc bảo mật (`SecurityFilterChain`):
  - `JwtAuthenticationFilter.java`: Kiểm tra chữ ký JWT trên mỗi Request.
  - `CustomOAuth2UserService.java` & `OAuth2AuthenticationSuccessHandler.java`: Xử lý đăng nhập Google OAuth2, tự động liên kết tài khoản trùng Email và cấp phát JWT.
- **`productivity/`**: Phân hệ Lịch và Công việc ưu tiên động:
  - `UrgencyScoringEngine.java`: Cài đặt công thức toán học tính điểm khẩn cấp động $U \in [0, 100]$.
  - `TaskUrgencyRecalculationScheduler.java`: Tiến trình ngầm `@Scheduled` chạy định kỳ cập nhật lại mức độ khẩn cấp và phát cảnh báo các công việc sắp quá hạn.
- **`study/`**: Phân hệ Không gian học tập:
  - `DocumentSyllabusExtractorService.java`: Đọc file PDF/DOCX đề cương môn học để bóc tách thành danh sách `StudyTopic`.
  - `SpacedRepetitionSm2Service.java`: Cài đặt thuật toán lặp lại ngắt quãng SM-2 tính toán `easiness_factor`, `interval_days` và `next_review_date`.
  - `ExamReadinessService.java`: Tính toán phần trăm kiến thức đã sẵn sàng trước kỳ thi.
- **`finance/`**: Phân hệ Tài chính cá nhân:
  - `FinanceLedgerService.java`: Thực thi giao dịch thu/chi với `@Transactional` và khóa bi quan `PessimisticLockType.PESSIMISTIC_WRITE` trên bản ghi `Wallet`.
- **`habit/`**: Phân hệ Thói quen & Thể thao:
  - `StreakCalculationService.java`: Thuật toán tính chuỗi ngày liên tục (`current_streak`, `longest_streak`) và xuất dữ liệu ma trận nhiệt 365 ngày.
- **`journal/`**: Phân hệ Nhật ký & Cảm xúc:
  - `DailySnapshotAggregatorService.java`: Thu thập dữ liệu chéo từ các phân hệ Task, Study, Finance, Habit trong ngày để đính kèm vào trang nhật ký.

### 2.3. Khu vực `frontend/src/` (Next.js 14 App Router)
- **`components/hud/DocumentPipProvider.tsx` & `StickyMiniHudWindow.tsx`**: Trái tim của tính năng cửa sổ nổi. Sử dụng `ReactDOM.createPortal` để render giao diện React kèm toàn bộ stylesheet TailwindCSS sang cửa sổ `window.documentPictureInPicture` độc lập, giữ nguyên kết nối State với cửa sổ chính.
- **`hooks/useAmbientAudioMixer.ts`**: Quản lý nhiều luồng `HTMLAudioElement` đồng thời để phát nhạc Lo-Fi và trộn các hiệu ứng âm thanh mưa, sóng biển, quán cà phê mượt mà không bị ngắt quãng khi người dùng chuyển giữa các trang.
