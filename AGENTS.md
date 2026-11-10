# Quy Tắc Kiến Trúc & Phát Triển Dành Cho AI Agent (AGENTS.md) - OmniLife Personal OS

Tài liệu này định nghĩa bộ quy tắc bắt buộc dành cho AI Agent (Antigravity) khi tham gia khảo sát, thiết kế, lập trình, kiểm thử và bảo trì mã nguồn trong dự án **OmniLife**.

---

## 1. Quy Tắc Bất Biến Bảo Vệ Tài Liệu Thiết Kế (`@docs`)

1. **Nguyên vẹn tuyệt đối thư mục `docs/`:** Thư mục `./docs/` chứa 14 file đặc tả gốc của hệ thống (`README.md`, `product.md`, `spec.md`, `design-system.md`, `assets.md`, `folder-structure.md`, `architecture.md`, `database.md`, `api.md`, `git-workflow.md`, `github-setup.md`, `deploy.md`, `roadmap.md`, `worklog.md`).
2. **Cấm xóa hoặc ghi đè trái phép:** Tuyệt đối không xóa, đổi tên, di chuyển hoặc tự ý thay đổi nội dung kiến trúc trong `./docs/` nếu không có yêu cầu trực tiếp từ người dùng.
3. **Tra cứu trước khi viết code:** Trước khi thực thi bất kỳ Task nào từ `TASK-01` đến `TASK-21`, Agent bắt buộc phải đọc kỹ yêu cầu trong `docs/roadmap.md`, cấu trúc bảng trong `docs/database.md`, hợp đồng giao tiếp trong `docs/api.md` và công thức nghiệp vụ trong `docs/spec.md`.

---

## 2. Tóm Tắt Kiến Trúc & Tech Stack Cốt Lõi

- **Mô hình kiến trúc:** Monorepo gồm **Backend Modular Monolith** (Spring Boot 3.3) + **Frontend Hybrid SPA** (Next.js 14 App Router) + **Always-on-Top Mini HUD** (Document Picture-in-Picture API qua Cross-Window React Portal) + **MySQL 8.0 InnoDB** (thiết kế và tối ưu trên MySQL Workbench).
- **Backend Stack:**
  - Java 21 LTS, Spring Boot 3.3.x, Maven.
  - Spring Web, Spring Data JPA (Hibernate 6), Spring Security 6 (JWT Stateless + Google OAuth2 Client), Jakarta Validation, Spring AOP, Spring Mail, Spring Cache (Caffeine).
  - Flyway Database Migration (`V1__init_omnilife_schema.sql`, `V2__seed_initial_data.sql`).
  - SpringDoc OpenAPI 3 (Swagger UI), Apache PDFBox / Apache POI (bóc tách đề cương học tập).
  - Kiểm thử: JUnit 5, Mockito, AssertJ.
- **Database Stack:**
  - MySQL 8.0+ (`InnoDB`, `utf8mb4_unicode_ci`), gồm 24 bảng dữ liệu và 8 Composite Indexes chiến lược.
- **Frontend Stack:**
  - Next.js 14 (App Router), React 18, TypeScript, Node.js 20 LTS.
  - TailwindCSS, shadcn/ui (Radix UI Primitives), Lucide Icons (`strokeWidth={1.75}`).
  - TanStack Query v5, Zustand, FullCalendar / dnd-kit, Recharts, TipTap / React-Markdown.
  - Web APIs: `window.documentPictureInPicture`, Web Audio API, Notification API.
- **DevOps & Hạ tầng:**
  - Docker (Multi-stage build `eclipse-temurin:21-jre-alpine` tối ưu dưới 350MB RAM), Docker Compose, GitHub Actions CI/CD, triển khai trên Render, Vercel và Aiven/TiDB Cloud.

---

## 3. Quy Định Vị Trí Đặt Mã Nguồn (Directory Placement Rules)

Mọi file mã nguồn mới bắt buộc phải đặt đúng vị trí đã định nghĩa trong `docs/folder-structure.md`:

### 3.1. Khu vực `backend/` (`backend/src/main/java/com/omnilife/`)
- `common/`:
  - `annotation/`: Chứa custom annotations như `@TrackActivity`.
  - `aspect/`: Chứa các Spring AOP Aspects (`ActivityAuditAspect`, `SqlPerformanceAspect`).
  - `config/`: Chứa các class cấu hình hạ tầng (`AsyncConfig`, `CacheConfig`, `CorsConfig`, `JacksonConfig`, `OpenApiConfig`, `SchedulingConfig`).
  - `entity/`: Chứa `BaseAuditEntity` (`created_at`, `updated_at`).
  - `exception/`: Chứa `ErrorCode`, `BusinessException`, `ResourceNotFoundException`, `ApiErrorResponse`, `GlobalExceptionHandler`.
  - `response/`: Chứa cấu trúc phản hồi chuẩn `ApiResponse<T>` và `PageResponse<T>`.
- `security/`:
  - Cấu hình `SecurityConfig`, `CustomUserDetails`, `CustomUserDetailsService`, `@CurrentUser`.
  - `jwt/`: Chứa `JwtTokenProvider`, `JwtAuthenticationFilter`, `JwtAuthenticationEntryPoint`.
  - `oauth2/`: Chứa `CustomOAuth2UserService`, `GoogleOAuth2UserInfo`, `OAuth2AuthenticationSuccessHandler`, `OAuth2AuthenticationFailureHandler`.
- **Các phân hệ nghiệp vụ (Domain Modules):** `user/`, `productivity/`, `study/`, `finance/`, `habit/`, `journal/`, `audio/`.
  - Mỗi domain module được chia thành các package con độc lập: `controller/`, `dto/`, `entity/`, `repository/`, `service/` (và `event/`, `scheduler/` đối với `productivity/`, `study/`).
  - **Ranh giới Module (Modular Monolith Boundary):** Một domain module không được phép gọi trực tiếp `Repository` của một domain module khác; mọi giao tiếp liên phân hệ phải đi qua Public `Service` hoặc phát sự kiện qua `ApplicationEventPublisher` (`@TransactionalEventListener`).
- `backend/src/main/resources/`:
  - Cấu hình `application.yml`, `application-local.yml`, `application-prod.yml`.
  - `db/migration/`: Chứa kịch bản Flyway `V1__init_omnilife_schema.sql` và `V2__seed_initial_data.sql`.
- `backend/src/test/java/com/omnilife/`:
  - Đặt các bài kiểm thử đơn vị và tích hợp theo đúng tên package của phân hệ tương ứng (`productivity/`, `study/`, `finance/`, `user/`, `habit/`, `journal/`, `audio/`).

### 3.2. Khu vực `frontend/` (`frontend/src/`)
- `app/`:
  - `(auth)/`: Chứa các trang xác thực `login/page.tsx`, `register/page.tsx`, `oauth2/callback/page.tsx`.
  - `(workspace)/`: Chứa `layout.tsx` (App Shell) và các trang chức năng `dashboard/`, `calendar/`, `study/` (kèm `courses/[courseId]/` và `review/`), `finance/`, `habits/`, `journal/`, `settings/`.
- `components/`:
  - `ui/`: Các primitive components tái sử dụng theo chuẩn shadcn/ui.
  - `layout/`: `AppSidebar.tsx`, `TopCommandHeader.tsx`, `CommandPaletteModal.tsx`, `AmbientAudioPlayerBar.tsx`.
  - `hud/`: `DocumentPipProvider.tsx`, `StickyMiniHudWindow.tsx`, `PomodoroMiniTimer.tsx`.
  - Các thư mục component theo nghiệp vụ: `productivity/`, `study/`, `finance/`, `habit/`, `journal/`.
- `hooks/`: Chứa custom hooks (`useDocumentPip.ts`, `usePomodoroTimer.ts`, `useAmbientAudioMixer.ts`, `useUrgencyLiveTick.ts`).
- `lib/`: Chứa `api-client.ts`, `urgency-calculator.ts`, `utils.ts`.
- `stores/`: Chứa Zustand stores (`authStore.ts`, `hudStore.ts`, `audioMixerStore.ts`).
- `types/`: Chứa các định nghĩa TypeScript interface/type (`productivity.ts`, `study.ts`, `finance.ts`, `habit.ts`, `journal.ts`).

### 3.3. Khu vực `database/`
- Lưu trữ mô hình MySQL Workbench `omnilife_erd_model.mwb` và các file kiểm thử hiệu năng SQL (`explain_queries_benchmark.sql`, `index_tuning_verification.sql`).

---

## 4. Chuẩn Coding Conventions & Kỹ Thuật Bắt Buộc

### 4.1. Quy tắc Backend (Java 21 & Spring Boot 3.3)
1. **Constructor Injection 100%:** Nghiêm cấm dùng `@Autowired` trên biến thành viên (Field Injection). Bắt buộc dùng `private final` fields kết hợp Constructor (hoặc `@RequiredArgsConstructor`).
2. **Kiểm soát Transaction & Chống Self-Invocation:**
   - Gắn `@Transactional(readOnly = true)` cho toàn bộ phương thức chỉ đọc dữ liệu ở tầng Service để tắt cơ chế Dirty Checking của Hibernate.
   - Gắn `@Transactional(rollbackFor = Exception.class)` cho các phương thức thay đổi dữ liệu.
   - Không gọi phương thức có `@Transactional` hoặc `@Async` thông qua `this.` trong cùng một class để tránh vô hiệu hóa CGLIB Proxy.
3. **Triệt tiêu lỗi N+1 Query:**
   - Mọi mối quan hệ `@ManyToOne` và `@OneToMany` bắt buộc khai báo `fetch = FetchType.LAZY`.
   - Khi cần lấy dữ liệu kèm bảng liên kết, bắt buộc sử dụng `@EntityGraph` hoặc Java Record DTO Projection.
4. **Kiểm soát đồng thời & Kiểu dữ liệu tiền tệ:**
   - Bảng `tasks` bắt buộc có `@Version private Long version;` (Optimistic Locking) để xử lý xung đột khi cập nhật đồng thời từ giao diện chính và cửa sổ Mini HUD.
   - Bảng `wallets` bắt buộc sử dụng khóa bi quan `@Lock(LockModeType.PESSIMISTIC_WRITE)` (`SELECT ... FOR UPDATE`) khi thực hiện giao dịch tài chính.
   - Mọi số liệu tiền tệ và điểm số chính xác bắt buộc dùng `BigDecimal` (`Scale = 2`, `RoundingMode.HALF_UP`), tuyệt đối không dùng `float` hoặc `double`.
5. **Bảo mật chống IDOR:**
   - Mọi truy vấn dữ liệu cá nhân phải lấy `userId` từ `SecurityContextHolder` (qua `@CurrentUser CustomUserDetails`), không bao giờ nhận `userId` trực tiếp từ tham số Request của Client.

### 4.2. Quy tắc Frontend (Next.js 14 & TypeScript)
1. **Strict TypeScript:** Không sử dụng kiểu `any`. Mọi payload gọi API và phản hồi phải được định nghĩa kiểu rõ ràng trong `src/types/`.
2. **Design System & Typography:**
   - Tuân thủ phong cách Dark-First Bento Grid (`--bg-canvas: #090A0F`, `--bg-surface-1: #11131A`, viền `1px` `--border-subtle: #222634`).
   - Bắt buộc áp dụng font `JetBrains Mono` kèm thuộc tính `tabular-nums` cho toàn bộ đồng hồ Pomodoro, điểm khẩn cấp (`urgencyScore`), thời gian đếm ngược và số tiền tài chính.
   - Sử dụng Shimmer Skeleton cho trạng thái đang tải, không dùng Spinner chắn toàn màn hình.
3. **Cross-Window React Portal cho Mini HUD:**
   - Khi mở cửa sổ `window.documentPictureInPicture`, phải sao chép toàn bộ CSS stylesheets sang `pipWindow.document.head` và render `<StickyMiniHudWindow />` bằng `ReactDOM.createPortal` để dùng chung Zustand Store với cửa sổ chính; đồng thời cung cấp chế độ Floating Mini Panel dự phòng (Fallback) nếu trình duyệt không hỗ trợ Document PiP.

### 4.3. Quy tắc Văn Phong & Git
- Không sử dụng icon hoặc emoji trong mã nguồn, log, tài liệu kỹ thuật và câu trả lời.
- Chỉ thực hiện `git commit` và `git push` khi có sự xác nhận trực tiếp từ người dùng.
- Mọi Commit Message phải tuân thủ chuẩn Conventional Commits được quy định trong `CONTRIBUTING.md`.
