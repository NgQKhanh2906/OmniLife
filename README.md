# OmniLife - Hệ Điều Hành Năng Suất & Quản Trị Đời Sống Cá Nhân Toàn Diện

[![Java](https://img.shields.io/badge/Java-21_LTS-ED8B00?style=flat-square&logo=openjdk&logoColor=white)](https://openjdk.org/projects/jdk/21/)
[![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.3.x-6DB33F?style=flat-square&logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![MySQL](https://img.shields.io/badge/MySQL-8.0_InnoDB-4479A1?style=flat-square&logo=mysql&logoColor=white)](https://www.mysql.com/products/workbench/)
[![Next.js](https://img.shields.io/badge/Next.js-14_App_Router-000000?style=flat-square&logo=next.js&logoColor=white)](https://nextjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?style=flat-square&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![TailwindCSS](https://img.shields.io/badge/Tailwind_CSS-3.4-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)](https://tailwindcss.com/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-4F46E5?style=flat-square)](./LICENSE)

---

## 1. Tổng Quan Dự Án (Overview)

**OmniLife** là nền tảng ứng dụng Web quản trị năng suất và đời sống cá nhân toàn diện (**Personal Life Operating System**), được thiết kế chuyên biệt cho sinh viên đại học ngành Công nghệ Thông tin / Kỹ thuật, lập trình viên và người làm việc trí óc.

Thay vì phải sử dụng rời rạc nhiều ứng dụng khác nhau (Google Calendar để xem lịch, Todoist để liệt kê công việc, Anki/Notion để theo dõi ôn thi, MoneyLover để ghi chép chi tiêu, Strava/Habitica để theo dõi tập luyện, DayOne để viết nhật ký), **OmniLife** hợp nhất toàn bộ vào một không gian làm việc duy nhất theo phong cách **Dark-First Bento Grid** tinh gọn, có tính liên kết dữ liệu chặt chẽ giữa các phân hệ.

### Giá Trị Cốt Lõi & Điểm Khác Biệt (Core Value Propositions)
1. **Động cơ tính điểm khẩn cấp động (Dynamic Urgency Scoring Engine):** Thay thế hoàn toàn cơ chế nhãn ưu tiên tĩnh truyền thống. Điểm số khẩn cấp $U \in [0, 100]$ của mỗi công việc biến thiên liên tục theo thời gian thực dựa trên độ ưu tiên gốc, hàm suy giảm phi tuyến tính theo số giờ còn lại tới hạn chót, tỷ lệ khối lượng công việc và lịch trình trong ngày.
2. **Cửa sổ nhắc việc luôn nổi trên màn hình (Always-on-Top Sticky Mini HUD):** Ứng dụng chuẩn `window.documentPictureInPicture` kết hợp **Cross-Window React Portal** để tách một cửa sổ điều khiển nhỏ luôn ghim trên mọi phần mềm khác (IDE, Word, MySQL Workbench, trình đọc PDF) ngay từ trình duyệt mà không cần cài đặt ứng dụng Desktop.
3. **Không gian học tập tích hợp thuật toán SM-2 (Dedicated Study Workspace):** Tự động bóc tách đề cương môn học từ tài liệu tải lên thành các đơn vị kiến thức, kiểm soát việc ôn bài cũ bằng thuật toán Lặp lại ngắt quãng (**Spaced Repetition SM-2**) và tính toán chỉ số sẵn sàng trước kỳ thi.
4. **Liên kết ngữ cảnh đa phân hệ (Cross-Module Context Linking):** Trang nhật ký cuối ngày tự động tổng hợp bản chụp hoạt động (**Daily Activity Snapshot**) gồm các công việc đã hoàn thành, số phút tập trung Pomodoro, số chủ đề đã ôn tập, các thói quen đã điểm danh và tổng chi tiêu tài chính trong ngày.

---

## 2. Tính Năng Chính Theo Phân Hệ (Key Features)

Hệ thống bao gồm **8 phân hệ chức năng cốt lõi** hoạt động đồng bộ:

### 2.1. Xác Thực Kép & Quản Lý Hồ Sơ (Identity & Access Management)
- Đăng ký và đăng nhập truyền thống bằng Email/Mật khẩu được băm bằng thuật toán `BCrypt` (strength = 12).
- Đăng nhập một chạm qua **Google OAuth2 (OpenID Connect)** kèm cơ chế tự động liên kết tài khoản (**Account Linking**) khi trùng địa chỉ Email mà không tạo bản ghi người dùng trùng lặp.
- Cơ chế bảo mật Stateless sử dụng cặp `Access Token` (JWT, hạn 15 phút) và `Refresh Token` (lưu trong cơ sở dữ liệu và HttpOnly Cookie, hạn 7 ngày) kèm thuật toán xoay vòng (**Refresh Token Rotation**) và tự động thu hồi toàn bộ phiên nếu phát hiện rò rỉ token.
- Cá nhân hóa giao diện (Dark/Light Mode), múi giờ, thời lượng Pomodoro và bộ hệ số trọng số tính điểm khẩn cấp.

### 2.2. Lịch Biểu Hợp Nhất & Động Cơ Công Việc Ưu Tiên Động (Productivity Module)
- Quản lý công việc (CRUD, xóa mềm Soft Delete), chia nhỏ các bước thực hiện (**Subtasks**) với thanh tiến độ tự động và gắn nhãn phân loại.
- Tính toán tự động điểm số khẩn cấp $U \in [0, 100]$ và phân loại thành 5 cấp độ màu sắc cảnh báo: `OVERDUE`, `EXTREME`, `HIGH`, `MODERATE`, `LOW`.
- Ngăn kéo chi tiết công việc (**Task Detail Slide-over Drawer**) hiển thị mô tả Markdown, danh sách Subtasks, lịch sử các phiên Pomodoro và bảng giải thích chi tiết điểm số khẩn cấp.
- Lịch biểu hợp nhất (**Unified Calendar**) theo Ngày / Tuần / Tháng gộp chung 3 luồng: Sự kiện cá nhân & Google Calendar, Thời khóa biểu trên trường và các công việc đã gán khung giờ (**Time-blocking** qua thao tác kéo thả).

### 2.3. Cửa Sổ Nổi Nhắc Việc (Always-on-Top Mini HUD)
- Khởi chạy cửa sổ nổi kích thước chuẩn `340x460px` ghim trên cùng ở tầng hệ điều hành thông qua **Document Picture-in-Picture API** (kèm chế độ Floating Mini Panel dự phòng cho trình duyệt chưa hỗ trợ).
- Hiển thị trực quan Top 3 công việc có điểm khẩn cấp cao nhất kèm thời gian đếm ngược tới hạn chót và thông tin buổi học sắp diễn ra.
- Đồng bộ trạng thái hai chiều tức thì (`0ms`) với ứng dụng chính qua Zustand Store: bấm chạy đồng hồ Pomodoro hoặc đánh dấu hoàn thành Task/Subtask ngay trên cửa sổ nổi sẽ cập nhật trực tiếp xuống cơ sở dữ liệu và đẩy công việc khẩn cấp kế tiếp lên thay thế.

### 2.4. Không Gian Học Tập Chuyên Sâu (Dedicated Study Workspace)
- Quản lý Học kỳ (`semesters`), Môn học (`courses`), mục tiêu điểm số và Thời khóa biểu cố định hàng tuần (`class_schedules`) kèm tiến trình tự động gửi thông báo nhắc lịch học trước 30 phút.
- Hỗ trợ tải lên giáo trình/đề cương (`.pdf`, `.docx`, `.txt`, `.md`), sử dụng **Apache PDFBox / Apache POI** để bóc tách tự động các tiêu đề chương mục thành danh sách chủ đề học tập (`study_topics`).
- Lập lịch ôn tập bài cũ bằng thuật toán **Spaced Repetition SM-2**: dựa trên điểm tự đánh giá mức độ nhớ $q \in [0, 5]$, hệ thống tự động tính toán hệ số dễ nhớ (`easiness_factor` $\ge 1.30$), chu kỳ lặp lại (`interval_days`) và ngày ôn tập tiếp theo (`next_review_date`).
- Bảng đếm ngược kỳ thi (`exams`) tính toán tỷ lệ phần trăm kiến thức đã sẵn sàng (`readinessPercentage`) và tốc độ học khuyến nghị mỗi ngày.

### 2.5. Sổ Cái Tài Chính Cá Nhân (Personal Finance Ledger)
- Quản lý đa nguồn tiền (`wallets`: Tiền mặt, Tài khoản ngân hàng, Ví điện tử, Tiết kiệm) và danh mục thu/chi (`finance_categories`).
- Ghi nhận giao dịch Thu (`INCOME`), Chi (`EXPENSE`) và Chuyển khoản nội bộ giữa 2 ví (`TRANSFER`) đảm bảo tính nguyên tử ACID và kiểm soát truy cập đồng thời bằng khóa bi quan (**Pessimistic Write Lock - `SELECT ... FOR UPDATE`**) cùng kiểu dữ liệu `BigDecimal` chính xác tuyệt đối.
- Thiết lập hạn mức ngân sách tháng (`budgets`) cho từng danh mục với cơ chế cảnh báo trực quan khi chạm ngưỡng 80% và vượt 100%.
- Báo cáo thống kê dòng tiền ròng, tỷ lệ tiết kiệm và biểu đồ cơ cấu chi tiêu theo tháng.

### 2.6. Theo Dõi Thói Quen & Thể Thao (Habit & Fitness Tracker)
- Theo dõi cả thói quen đánh dấu hoàn thành (`BOOLEAN`, ví dụ: Ngủ trước 23h30) và mục tiêu định lượng (`NUMERIC`, ví dụ: Chạy bộ 5 km, Uống 2000 ml nước).
- Điểm danh hàng ngày (`habit_logs`), tự động tính toán chuỗi ngày duy trì liên tiếp hiện tại (`current_streak`) và kỷ lục chuỗi dài nhất (`longest_streak`).
- Hiển thị biểu đồ ma trận nhiệt hoạt động 365 ngày (**Contribution Heatmap** 52 tuần x 7 ngày).

### 2.7. Nhật Ký Hàng Ngày & Phân Tích Cảm Xúc (Daily Journal & Mood Analytics)
- Soạn thảo nhật ký theo ngày bằng trình biên tập Markdown, gắn nhãn tâm trạng và đánh dấu những ngày đáng nhớ.
- Ghi nhận chỉ số tâm trạng (`mood_score` từ 1 đến 5) và mức năng lượng (`energy_level` từ 1 đến 5).
- Tự động đính kèm khung **Daily Activity Snapshot** tổng hợp toàn bộ thành quả làm việc, học tập, tập luyện và chi tiêu trong ngày thông qua cơ chế Spring AOP (`@TrackActivity`) và Spring Domain Events.

### 2.8. Trình Phát Âm Thanh Tập Trung Đa Luồng (Ambient Focus Soundscape)
- Thanh điều khiển âm thanh cố định ở đáy màn hình, hoạt động xuyên suốt khi chuyển đổi giữa các trang mà không bị ngắt quãng.
- Kết hợp phát nhạc nền Lo-Fi (`midnight-coding`, `library-rain-study`, `deep-focus-synth`) với bộ trộn 5 kênh âm thanh trắng phát lặp liền mạch (Tiếng mưa, Sóng biển, Quán cà phê, Lửa trại, Bàn phím cơ) và lưu thành các **Audio Presets** cá nhân.

---

## 3. Kiến Trúc Hệ Thống & Công Nghệ Sử Dụng (Architecture & Tech Stack)

### 3.1. Kiến Trúc Tổng Thể
OmniLife được xây dựng theo kiến trúc **Modular Monolith ở phía Backend** kết hợp với **Hybrid Single-Page Application ở phía Frontend**:
- **High Cohesion, Low Coupling:** Mã nguồn Backend được phân tách thành các phân hệ nghiệp vụ độc lập (`user`, `productivity`, `study`, `finance`, `habit`, `journal`, `audio`) đặt dưới các tầng hạ tầng dùng chung (`common`, `security`). Các phân hệ giao tiếp qua Public Service Interface hoặc **Spring ApplicationEventPublisher** (`@TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)`).
- **Hiệu năng & Toàn vẹn dữ liệu:** Tận dụng tối đa sức mạnh của **MySQL 8.0 InnoDB** với 24 bảng chuẩn hóa 3NF, 8 Composite & Covering Indexes tuân thủ Leftmost Prefix Rule, kết hợp **Optimistic Locking (`@Version`)** cho công việc và **Pessimistic Write Locking** cho ví tài chính.

### 3.2. Bảng Tổng Hợp Công Nghệ (Tech Stack)

| Tầng (Layer) | Thành Phần / Thư Viện | Phiên Bản / Chi Tiết Kỹ Thuật | Vai Trò Trong Hệ Thống |
| :--- | :--- | :--- | :--- |
| **Backend Core** | Java (OpenJDK) | Java 21 LTS | Ngôn ngữ lập trình chính phía máy chủ |
| **Backend Framework** | Spring Boot | 3.3.x | Khung ứng dụng Modular Monolith, IoC/DI, Scheduling, Async |
| **Security & Auth** | Spring Security 6 + OAuth2 Client | 6.3.x | Xác thực JWT Stateless, BCrypt (12), Google OIDC & Account Linking |
| **Data Access (ORM)** | Spring Data JPA / Hibernate | Hibernate 6.x | Ánh xạ thực thể, `@EntityGraph`, DTO Projection, Locking |
| **Cross-Cutting** | Spring AOP + Caffeine Cache | 3.3.x | Tự động ghi nhận `@TrackActivity`, đo hiệu năng SQL, bộ nhớ đệm |
| **Document & Mail** | Apache PDFBox / POI + Spring Mail | 3.x / 5.x | Bóc tách mục lục đề cương PDF/DOCX và gửi Email nhắc lịch học |
| **API Documentation** | SpringDoc OpenAPI | 2.x (OpenAPI 3) | Tự động sinh tài liệu và giao diện thử nghiệm Swagger UI |
| **Database & Migration** | MySQL Server + Flyway | MySQL 8.0+ | Hệ quản trị CSDL quan hệ InnoDB và quản lý phiên bản lược đồ |
| **Database Modeling** | MySQL Workbench | 8.0+ | Thiết kế sơ đồ EER (`.mwb`), Visual Explain và tối ưu hóa Index |
| **Frontend Framework** | Next.js + React + TypeScript | Next.js 14 (App Router) | Xây dựng giao diện Web phản hồi nhanh, định tuyến phân nhóm |
| **UI & Styling** | TailwindCSS + shadcn/ui + Lucide | Tailwind 3.4 / Radix UI | Hệ thống giao diện Dark Bento Grid, Command Palette (`Ctrl + K`) |
| **State & Fetching** | TanStack Query + Zustand | v5 / v4 | Quản lý trạng thái đồng bộ Server/Client và chia sẻ State với Mini HUD |
| **Widgets & Charts** | FullCalendar, dnd-kit, Recharts, TipTap | Latest Stable | Lịch biểu kéo thả Time-blocking, biểu đồ dòng tiền, soạn thảo Markdown |
| **Browser Web APIs** | Document PiP, Web Audio, Notification | HTML5 / Chrome 116+ | Cửa sổ nổi Always-on-Top Mini HUD, bộ trộn âm thanh đa luồng |
| **Testing** | JUnit 5, Mockito, AssertJ, TypeScript | JUnit Jupiter | Kiểm thử đơn vị, kiểm thử tích hợp và kiểm thử đồng thời (Concurrency) |
| **DevOps & Cloud** | Docker, GitHub Actions, Render, Vercel | Multi-stage Alpine | Đóng gói container tối ưu bộ nhớ (< 350MB RAM) và CI/CD tự động |

---

## 4. Cấu Trúc Dự Án (Project Structure)

Sơ đồ dưới đây phản ánh chính xác 100% cấu trúc thư mục thực tế của kho lưu trữ Monorepo **OmniLife**:

```text
omnilife/
├── .github/                                        # Cấu hình CI/CD và mẫu quản lý dự án trên GitHub
│   ├── ISSUE_TEMPLATE/                             # Mẫu tạo Task Implementation và Bug Report
│   └── workflows/                                  # Pipeline GitHub Actions kiểm thử tự động Backend & Frontend
├── backend/                                        # Máy chủ Backend (Java 21 + Spring Boot 3.3 Modular Monolith)
│   ├── README.md                                   # Tài liệu hướng dẫn cấu trúc và vận hành riêng cho Backend
│   ├── .env.example                                # Mẫu biến môi trường dành riêng cho Spring Boot
│   └── src/
│       ├── main/
│       │   ├── java/com/omnilife/                  # Gói mã nguồn gốc của ứng dụng
│       │   │   ├── common/                         # Hạ tầng dùng chung toàn hệ thống
│       │   │   │   ├── annotation/                 # Custom Annotations (@TrackActivity)
│       │   │   │   ├── aspect/                     # Spring AOP Aspects (ActivityAuditAspect, SqlPerformanceAspect)
│       │   │   │   ├── config/                     # Cấu hình Async, Cache, CORS, Jackson, OpenAPI, Scheduling
│       │   │   │   ├── entity/                     # Thực thể cơ sở BaseAuditEntity (created_at, updated_at)
│       │   │   │   ├── exception/                  # Xử lý ngoại lệ tập trung GlobalExceptionHandler & ErrorCode
│       │   │   │   └── response/                   # Cấu trúc phản hồi chuẩn ApiResponse & PageResponse
│       │   │   ├── security/                       # Cấu hình bảo mật Spring Security 6 Filter Chain
│       │   │   │   ├── jwt/                        # Bộ lọc và nhà cung cấp JWT (JwtTokenProvider, Filter)
│       │   │   │   └── oauth2/                     # Xử lý đăng nhập Google OAuth2 và liên kết tài khoản tự động
│       │   │   ├── user/                           # Phân hệ Người dùng, Xác thực Local & Cấu hình cá nhân
│       │   │   │   ├── controller/                 # REST Controllers cho Auth và User Preferences
│       │   │   │   ├── dto/                        # Data Transfer Objects cho đăng nhập, đăng ký, hồ sơ
│       │   │   │   ├── entity/                     # Thực thể User, OAuthAccount, RefreshToken, UserPreference
│       │   │   │   ├── repository/                 # Giao diện truy vấn JPA cho phân hệ User
│       │   │   │   └── service/                    # Nghiệp vụ xác thực, xoay vòng Refresh Token và hồ sơ
│       │   │   ├── productivity/                   # Phân hệ Lịch biểu, Công việc, Pomodoro & Điểm khẩn cấp động
│       │   │   │   ├── controller/                 # REST Controllers cho Task, Calendar, FocusSession & Mini HUD
│       │   │   │   ├── dto/                        # DTOs cho Task, Subtask, Lịch hợp nhất và gói dữ liệu Mini HUD
│       │   │   │   ├── entity/                     # Thực thể Task (@Version), Subtask, CalendarEvent, FocusSession
│       │   │   │   ├── event/                      # Sự kiện nội bộ khi hoàn thành công việc (TaskCompletedEvent)
│       │   │   │   ├── repository/                 # Repositories kèm @EntityGraph và JPA Specification
│       │   │   │   ├── scheduler/                  # Tiến trình ngầm cập nhật điểm khẩn cấp định kỳ mỗi 15 phút
│       │   │   │   └── service/                    # Động cơ UrgencyScoringEngine, đồng bộ lịch và quản lý Task
│       │   │   ├── study/                          # Phân hệ Không gian học tập, Đề cương & Ôn tập SM-2
│       │   │   │   ├── controller/                 # REST Controllers cho Môn học, Tài liệu, Ôn tập và Kỳ thi
│       │   │   │   ├── dto/                        # DTOs cho Thời khóa biểu, Bóc tách đề cương, Kết quả SM-2
│       │   │   │   ├── entity/                     # Thực thể Semester, Course, ClassSchedule, StudyTopic, Exam
│       │   │   │   ├── repository/                 # Repositories truy vấn hàng đợi ôn bài cũ và môn học
│       │   │   │   ├── scheduler/                  # Tiến trình ngầm gửi thông báo nhắc lịch học trước 30 phút
│       │   │   │   └── service/                    # Thuật toán SpacedRepetitionSm2Service & PDF Syllabus Extractor
│       │   │   ├── finance/                        # Phân hệ Sổ cái tài chính cá nhân & Quản lý ngân sách tháng
│       │   │   │   ├── controller/                 # REST Controllers cho Ví, Giao dịch, Ngân sách và Thống kê
│       │   │   │   ├── dto/                        # DTOs cho tạo giao dịch, cảnh báo ngân sách và báo cáo tháng
│       │   │   │   ├── entity/                     # Thực thể Wallet, FinanceCategory, Transaction, Budget
│       │   │   │   ├── repository/                 # Repositories hỗ trợ khóa bi quan PESSIMISTIC_WRITE trên Wallet
│       │   │   │   └── service/                    # Nghiệp vụ sổ cái FinanceLedgerService & kiểm soát hạn mức
│       │   │   ├── habit/                          # Phân hệ Theo dõi thói quen, Thể thao & Biểu đồ nhiệt
│       │   │   │   ├── controller/                 # REST Controller cho quản lý thói quen, check-in và Heatmap
│       │   │   │   ├── dto/                        # DTOs cho điểm danh hàng ngày và dữ liệu ma trận 365 ngày
│       │   │   │   ├── entity/                     # Thực thể Habit và HabitLog
│       │   │   │   ├── repository/                 # Repositories tối ưu hóa bằng Covering Index cho Heatmap
│       │   │   │   └── service/                    # Thuật toán tính chuỗi ngày liên tiếp StreakCalculationService
│       │   │   ├── journal/                        # Phân hệ Nhật ký Markdown, Tâm trạng & Daily Activity Snapshot
│       │   │   │   ├── controller/                 # REST Controller cho truy vấn và lưu nhật ký theo ngày
│       │   │   │   ├── dto/                        # DTOs cho trang nhật ký, xu hướng cảm xúc và bản chụp hoạt động
│       │   │   │   ├── entity/                     # Thực thể JournalEntry
│       │   │   │   ├── repository/                 # Repository truy vấn nhật ký theo ngày và người dùng
│       │   │   │   └── service/                    # Dịch vụ tổng hợp hoạt động liên phân hệ DailySnapshotAggregator
│       │   │   └── audio/                          # Phân hệ Quản lý cấu hình âm thanh tập trung (Audio Presets)
│       │   │       ├── controller/                 # REST Controller cho lưu và lấy danh sách Preset âm thanh
│       │   │       ├── dto/                        # DTO cho cấu hình trộn nhạc Lo-Fi và 5 kênh âm thanh trắng
│       │   │       ├── entity/                     # Thực thể AudioPreset
│       │   │       ├── repository/                 # Repository quản lý AudioPreset theo người dùng
│       │   │       └── service/                    # Dịch vụ nghiệp vụ AudioPresetService
│       │   └── resources/                          # Tài nguyên cấu hình ứng dụng và kịch bản cơ sở dữ liệu
│       │       └── db/migration/                   # Chứa các file SQL Flyway Migration (V1 Schema, V2 Seed Data)
│       └── test/
│           └── java/com/omnilife/                  # Bộ kiểm thử tự động JUnit 5 & Integration Tests
│               ├── audio/                          # Kiểm thử phân hệ âm thanh
│               ├── finance/                        # Kiểm thử đồng thời Pessimistic Lock sổ cái tài chính
│               ├── habit/                          # Kiểm thử thuật toán tính chuỗi Streak thói quen
│               ├── journal/                        # Kiểm thử tổng hợp Daily Activity Snapshot
│               ├── productivity/                   # Kiểm thử công thức UrgencyScoringEngine & Optimistic Locking
│               ├── study/                          # Kiểm thử thuật toán lặp lại ngắt quãng Spaced Repetition SM-2
│               └── user/                           # Kiểm thử xác thực và xoay vòng Refresh Token
├── database/                                       # Khu vực lưu trữ thiết kế và kiểm nghiệm hiệu năng MySQL Workbench
│   └── README.md                                   # Hướng dẫn sử dụng mô hình .mwb và chạy kịch bản EXPLAIN
├── docs/                                           # Bộ 14 tài liệu đặc tả kiến trúc, nghiệp vụ và lộ trình dự án
│   ├── README.md                                   # Mục lục tổng quan bộ tài liệu thiết kế
│   ├── api.md                                      # Đặc tả chi tiết RESTful API Endpoints, JSON Payload & Mã lỗi
│   ├── architecture.md                             # Kiến trúc hệ thống, luồng dữ liệu và giải phẫu Spring Boot 3
│   ├── assets.md                                   # Danh mục tài nguyên âm thanh, biểu tượng và bộ dữ liệu mẫu
│   ├── database.md                                 # Thiết kế ERD 24 bảng, 8 Composite Indexes và kịch bản DDL
│   ├── deploy.md                                   # Chiến lược triển khai đám mây miễn phí 100% và tối ưu Docker
│   ├── design-system.md                            # Quy chuẩn giao diện Bento Grid, bảng màu, kiểu chữ và Component
│   ├── folder-structure.md                         # Đặc tả cấu trúc cây thư mục chi tiết của dự án
│   ├── git-workflow.md                             # Quy trình phân nhánh Git và quy chuẩn Conventional Commits
│   ├── github-setup.md                             # Hướng dẫn thiết lập kho lưu trữ GitHub và CI/CD Pipeline
│   ├── product.md                                  # Tầm nhìn sản phẩm, chân dung người dùng, USP và phạm vi
│   ├── roadmap.md                                  # Lộ trình thực thi chia thành 6 Phase (TASK-01 đến TASK-21)
│   ├── spec.md                                     # Đặc tả yêu cầu chức năng, phi chức năng và công thức toán học
│   └── worklog.md                                  # Bảng theo dõi tiến độ thực hiện từng Task kỹ thuật
├── frontend/                                       # Ứng dụng giao diện người dùng (Next.js 14 App Router + TypeScript)
│   ├── README.md                                   # Tài liệu hướng dẫn cấu trúc và vận hành riêng cho Frontend
│   ├── .env.example                                # Mẫu biến môi trường dành riêng cho Next.js
│   ├── public/                                     # Tài nguyên tĩnh phục vụ trực tiếp trên trình duyệt
│   │   ├── audio/                                  # Kho tệp âm thanh phục vụ trình phát tập trung
│   │   │   ├── ambient/                            # Các tệp âm thanh môi trường lặp liền mạch (Mưa, Sóng, Cà phê...)
│   │   │   ├── lofi/                               # Các bản nhạc nền Lo-Fi giúp tập trung học tập và lập trình
│   │   │   └── sfx/                                # Hiệu ứng âm thanh tương tác ngắn (Hoàn thành việc, Chuông Pomodoro)
│   │   └── images/                                 # Logo vector SVG, hình minh họa Empty State và ảnh đại diện mặc định
│   └── src/                                        # Mã nguồn chính của ứng dụng Frontend
│       ├── app/                                    # Định tuyến theo quy chuẩn Next.js 14 App Router
│       │   ├── (auth)/                             # Nhóm trang xác thực không dùng khung Sidebar chính
│       │   │   ├── login/                          # Trang đăng nhập Email/Password và Google OAuth2
│       │   │   ├── oauth2/callback/                # Trang nhận và xử lý JWT Token sau khi xác thực Google
│       │   │   └── register/                       # Trang đăng ký tài khoản mới
│       │   └── (workspace)/                        # Nhóm không gian làm việc chính sử dụng Bento Shell Layout
│       │       ├── calendar/                       # Trang lịch biểu hợp nhất và kéo thả Time-blocking
│       │       ├── dashboard/                      # Trung tâm điều khiển tổng quan hàng ngày
│       │       ├── finance/                        # Trang quản lý ví, sổ cái thu chi và ngân sách tháng
│       │       ├── habits/                         # Trang điểm danh thói quen, thể thao và biểu đồ nhiệt 365 ngày
│       │       ├── journal/                        # Trang viết nhật ký Markdown và theo dõi tâm trạng
│       │       ├── settings/                       # Trang cài đặt giao diện, múi giờ và trọng số khẩn cấp
│       │       └── study/                          # Không gian học tập chuyên sâu
│       │           ├── courses/[courseId]/         # Trang chi tiết từng môn học, đề cương và tài liệu
│       │           └── review/                     # Trang ôn tập bài cũ theo thuật toán Spaced Repetition SM-2
│       ├── components/                             # Hệ thống React Components phân tách theo chức năng
│       │   ├── finance/                            # Các thành phần giao diện Ví, Ngân sách, Biểu đồ dòng tiền
│       │   ├── habit/                              # Lưới check-in thói quen, huy hiệu Streak và Heatmap
│       │   ├── hud/                                # Cửa sổ nổi DocumentPipProvider & StickyMiniHudWindow
│       │   ├── journal/                            # Trình soạn thảo Markdown, bộ chọn Mood và thẻ Daily Snapshot
│       │   ├── layout/                             # Khung giao diện AppSidebar, Header, CommandPalette, AudioBar
│       │   ├── productivity/                       # Bảng xếp hạng công việc động, thẻ Task, Drawer và Lịch tuần
│       │   ├── study/                              # Thẻ môn học Bento, Modal tải đề cương, bộ thẻ ôn tập SM-2
│       │   └── ui/                                 # Các thành phần giao diện cơ sở chuẩn shadcn/ui (Button, Dialog...)
│       ├── hooks/                                  # Custom React Hooks (useDocumentPip, usePomodoroTimer, AudioMixer)
│       ├── lib/                                    # Cấu hình Axios Interceptor, hàm tính điểm khẩn cấp và tiện ích
│       ├── stores/                                 # Quản lý trạng thái toàn cục với Zustand (auth, hud, audioMixer)
│       └── types/                                  # Định nghĩa kiểu dữ liệu TypeScript cho toàn bộ phân hệ
├── .env.example                                    # Tệp tổng hợp biến môi trường mẫu ở cấp thư mục gốc
├── .gitignore                                      # Cấu hình loại trừ tệp tin rác, thư mục biên dịch và khóa bí mật
├── AGENTS.md                                       # Quy tắc kiến trúc và chuẩn lập trình dành cho AI Agent (Antigravity)
├── CONTRIBUTING.md                                 # Hướng dẫn quy trình phân nhánh, viết Commit và tạo Pull Request
├── LICENSE                                         # Giấy phép mã nguồn mở MIT License
└── README.md                                       # Tài liệu giới thiệu tổng quan và hướng dẫn sử dụng toàn dự án
```

---

## 5. Danh Mục Tài Liệu Thiết Kế Chi Tiết (Documentation Index)

Toàn bộ 14 tài liệu đặc tả kỹ thuật chuyên sâu nằm trong thư mục `./docs/`. Bạn có thể nhấp trực tiếp vào từng liên kết dưới đây để tra cứu:

| STT | Tệp Tài Liệu | Nội Dung Trọng Tâm |
| :---: | :--- | :--- |
| 01 | [docs/README.md](./docs/README.md) | Tổng quan dự án, danh mục công nghệ và hướng dẫn khởi chạy nhanh |
| 02 | [docs/product.md](./docs/product.md) | Tầm nhìn sản phẩm, phân tích User Personas, điểm khác biệt độc quyền (USP) và phạm vi hệ thống |
| 03 | [docs/spec.md](./docs/spec.md) | Đặc tả yêu cầu chức năng (FR), phi chức năng (NFR), công thức toán học tính điểm khẩn cấp và thuật toán SM-2 |
| 04 | [docs/design-system.md](./docs/design-system.md) | Quy chuẩn thiết kế giao diện Dark Bento Grid, bảng màu Hex, hệ thống chữ và cấu trúc Component |
| 05 | [docs/assets.md](./docs/assets.md) | Danh mục biểu tượng Lucide, tài nguyên âm thanh Lo-Fi/White Noise và bộ dữ liệu mẫu (Seed Data) |
| 06 | [docs/folder-structure.md](./docs/folder-structure.md) | Sơ đồ cây thư mục chi tiết và giải thích trách nhiệm của từng khu vực mã nguồn |
| 07 | [docs/architecture.md](./docs/architecture.md) | Sơ đồ kiến trúc Modular Monolith, luồng dữ liệu Sequence Diagram và 7 cơ chế cốt lõi của Spring Boot 3 |
| 08 | [docs/database.md](./docs/database.md) | Sơ đồ thực thể quan hệ ERD, mô tả chi tiết 24 bảng, chiến lược 8 Composite Indexes và kịch bản DDL SQL |
| 09 | [docs/api.md](./docs/api.md) | Đặc tả đầy đủ các RESTful API Endpoints, cấu trúc JSON Request/Response và bảng mã lỗi chuẩn |
| 10 | [docs/git-workflow.md](./docs/git-workflow.md) | Chiến lược phân nhánh Git, quy chuẩn Conventional Commits và vòng đời thực thi một Task |
| 11 | [docs/github-setup.md](./docs/github-setup.md) | Cấu hình kho lưu trữ GitHub, biến môi trường, `.gitignore` và pipeline GitHub Actions CI/CD |
| 12 | [docs/deploy.md](./docs/deploy.md) | Kiến trúc triển khai đám mây miễn phí 100% (Vercel, Render, Aiven/TiDB), tối ưu hóa Dockerfile và Docker Compose |
| 13 | [docs/roadmap.md](./docs/roadmap.md) | Lộ trình phát triển chia làm 6 Phase từ `TASK-01` đến `TASK-21` kèm tiêu chí nghiệm thu cụ thể |
| 14 | [docs/worklog.md](./docs/worklog.md) | Bảng theo dõi tiến độ thực thi từng Task và nhật ký quyết định kiến trúc |

---

## 6. Hướng Dẫn Bắt Đầu (Getting Started)

### 6.1. Yêu Cầu Tiên Quyết (Prerequisites)
Để thiết lập và chạy môi trường phát triển cục bộ, máy tính của bạn cần cài đặt sẵn:
- **JDK:** OpenJDK 21 LTS trở lên (`java -version`).
- **Maven:** Apache Maven 3.9+ (hoặc sử dụng Maven Wrapper `./mvnw` trong thư mục `backend/`).
- **Cơ sở dữ liệu:** MySQL Server 8.0+ và công cụ **MySQL Workbench 8.0+**.
- **Node.js:** Phiên bản v20 LTS trở lên kèm trình quản lý gói `npm` (`node -v`, `npm -v`).
- **Docker (Tùy chọn):** Docker Desktop nếu muốn khởi chạy nhanh cơ sở dữ liệu và Backend qua `docker compose`.
- **Trình duyệt khuyến nghị:** Google Chrome 116+ hoặc Microsoft Edge 116+ (bắt buộc để hỗ trợ đầy đủ chuẩn `window.documentPictureInPicture` cho cửa sổ nổi Mini HUD).

### 6.2. Bước 1: Sao Chép Kho Lưu Trữ (Clone Repository)
```bash
git clone https://github.com/NgQKhanh2906/OmniLife.git
cd OmniLife
```

### 6.3. Bước 2: Khởi Tạo Cơ Sở Dữ Liệu Trên MySQL Workbench
1. Mở **MySQL Workbench** và kết nối tới máy chủ MySQL cục bộ (`localhost:3306`).
2. Tạo cơ sở dữ liệu `omnilife_db` với bảng mã hỗ trợ đầy đủ tiếng Việt:
   ```sql
   CREATE DATABASE IF NOT EXISTS omnilife_db
       CHARACTER SET utf8mb4
       COLLATE utf8mb4_unicode_ci;
   ```
3. Khi ứng dụng Backend khởi chạy ở bước tiếp theo, **Flyway** sẽ tự động thực thi các kịch bản trong `backend/src/main/resources/db/migration/` để tạo toàn bộ 24 bảng, 8 Composite Indexes và nạp bộ dữ liệu mẫu.

### 6.4. Bước 3: Thiết Lập Biến Môi Trường & Chạy Backend (Spring Boot 3.3)
1. Sao chép file cấu hình biến môi trường mẫu:
   ```bash
   cp .env.example backend/.env
   ```
2. Mở file `backend/.env` và cập nhật thông tin `DB_USERNAME`, `DB_PASSWORD` khớp với MySQL trên máy của bạn (cùng với `GOOGLE_CLIENT_ID` và `GOOGLE_CLIENT_SECRET` nếu thử nghiệm đăng nhập Google OAuth2).
3. Di chuyển vào thư mục `backend/` và khởi chạy máy chủ:
   ```bash
   cd backend
   ./mvnw spring-boot:run
   ```
4. Dịch vụ Backend sẽ lắng nghe tại địa chỉ `http://localhost:8080`.
5. Truy cập tài liệu OpenAPI (Swagger UI) tại: `http://localhost:8080/swagger-ui.html`.

### 6.5. Bước 4: Thiết Lập Biến Môi Trường & Chạy Frontend (Next.js 14)
1. Mở một cửa sổ dòng lệnh (Terminal) mới tại thư mục gốc của dự án và di chuyển vào thư mục `frontend/`:
   ```bash
   cd frontend
   ```
2. Sao chép file biến môi trường mẫu cho Next.js:
   ```bash
   cp .env.example .env.local
   ```
3. Cài đặt các gói thư viện phụ thuộc và khởi chạy máy chủ phát triển:
   ```bash
   npm install
   npm run dev
   ```
4. Mở trình duyệt Chrome hoặc Edge tại địa chỉ: `http://localhost:3000`.
5. Bạn có thể đăng nhập ngay bằng tài khoản dữ liệu mẫu được mô tả trong [docs/assets.md](./docs/assets.md):
   - **Email:** `khanh.student@omnilife.local`
   - **Mật khẩu:** `OmniLife@2026`

---

## 7. Đóng Góp & Giấy Phép (Contributing & License)

- **Hướng dẫn đóng góp:** Mọi quy định về quy trình phân nhánh (`main`, `develop`, `feature/TASK-*`), chuẩn viết Commit Message (Conventional Commits) và quy trình kiểm tra trước khi tạo Pull Request được trình bày chi tiết trong tệp [CONTRIBUTING.md](./CONTRIBUTING.md).
- **Quy tắc dành cho AI Agent:** Các quy định về ranh giới kiến trúc Modular Monolith, quy tắc chống lỗi N+1 Query và bảo vệ tài liệu gốc được quy định trong tệp [AGENTS.md](./AGENTS.md).
- **Giấy phép:** Dự án được phân phối dưới giấy phép **MIT License**. Xem toàn văn giấy phép tại tệp [LICENSE](./LICENSE).
