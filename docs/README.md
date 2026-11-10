# OmniLife - All-in-One Personal Productivity & Life Operating System

## 1. Tổng Quan Dự Án

**OmniLife** là nền tảng Web Application cá nhân hóa toàn diện (Personal Life Operating System), kết hợp quản lý lịch biểu thông minh, danh sách công việc tự động xếp hạng theo mức độ khẩn cấp động (Dynamic Urgency Scoring), cửa sổ nhắc việc luôn nổi trên màn hình máy tính (Always-on-top Mini HUD qua Document Picture-in-Picture API), không gian học tập chuyên biệt (Study Workspace với thuật toán lặp lại ngắt quãng Spaced Repetition), quản lý tài chính cá nhân (Finance Ledger), theo dõi thói quen và thể thao (Habit & Fitness Tracker), viết nhật ký cảm xúc (Daily Journal & Mood) và trình phát âm thanh tập trung (Ambient Lo-Fi Focus Player).

Dự án được thiết kế với hai mục tiêu song hành:
1. **Mục tiêu Sản phẩm (Product Goal):** Tạo ra một công cụ quản trị cuộc sống và học tập thực thụ dùng hàng ngày với trải nghiệm UI/UX tinh tế theo phong cách Bento Grid / Minimalist hiện đại.
2. **Mục tiêu Kỹ thuật (Engineering Goal):** Nắm vững bản chất cốt lõi của **Java Spring Boot 3** (IoC/DI, Spring Security 6 với OAuth2 + JWT, JPA/Hibernate Optimization, Transaction Isolation, Optimistic/Pessimistic Locking, Spring AOP, Domain Events, Task Scheduling) và thiết kế, tối ưu hóa cơ sở dữ liệu quan hệ trên **MySQL 8 (MySQL Workbench)**.

---

## 2. Các Phân Hệ Chức Năng Chính (Core Modules)

1. **Identity & Access Management (Auth Module):**
   - Đăng nhập và đăng ký bằng Email/Password truyền thống (BCrypt hashing).
   - Đăng nhập một chạm qua Google OAuth2 (OpenID Connect) và cơ chế tự động liên kết tài khoản (Account Linking) khi trùng Email.
   - Cơ chế bảo mật Stateless sử dụng cặp Access Token (JWT) và Refresh Token (Rotation & Revocation).

2. **Smart Calendar & Dynamic Urgency Task Engine (Productivity Module):**
   - Lịch biểu trực quan theo Ngày / Tuần / Tháng, hỗ trợ kéo thả thời gian (Time-blocking) và đồng bộ sự kiện từ Google Calendar.
   - Động cơ tính điểm khẩn cấp động (Dynamic Urgency Score): Tự động tính toán lại điểm số ưu tiên của từng công việc dựa trên độ ưu tiên gốc, số giờ còn lại tới Deadline (hàm suy giảm phi tuyến tính) và thời lượng ước tính thực hiện.
   - Danh sách công việc tự động đẩy các đầu việc nguy cơ trễ hạn lên vị trí đầu bảng kèm cảnh báo màu sắc trực quan.

3. **Always-on-Top Mini HUD (Document PiP Module):**
   - Sử dụng chuẩn Web API hiện đại (`window.documentPictureInPicture`) để tách một cửa sổ nhỏ luôn nổi trên toàn bộ các ứng dụng khác của máy tính khi người dùng rời trình duyệt.
   - Hiển thị Top 3 công việc có điểm khẩn cấp cao nhất, đồng hồ đếm ngược Pomodoro, nút đánh dấu hoàn thành nhanh và điều khiển nhạc nền.

4. **Dedicated Study Workspace (Study Module):**
   - Quản lý danh sách môn học theo học kỳ, mục tiêu điểm số và đề cương chi tiết (Syllabus/Topics).
   - Lưu trữ và phân loại tài liệu học tập, trích xuất danh sách các mục kiến thức cần hoàn thành trong ngày.
   - Kiểm soát việc ôn bài cũ bằng thuật toán Lặp lại ngắt quãng (Spaced Repetition: SM-2 tinh chỉnh với các chu kỳ 1 - 3 - 7 - 14 - 30 ngày).
   - Bảng đếm ngược kỳ thi (Exam Countdown) tự động chia khối lượng bài học còn lại cho số ngày trước kỳ thi và thông báo lịch học trên trường.

5. **Personal Finance Ledger (Finance Module):**
   - Quản lý các ví tiền (Tiền mặt, Tài khoản ngân hàng, Ví điện tử) và ghi chép giao dịch Thu/Chi theo danh mục.
   - Thiết lập hạn mức ngân sách tháng (Monthly Budget) cho từng danh mục kèm cơ chế cảnh báo khi chạm ngưỡng 80% và 100%.
   - Đảm bảo tính toàn vẹn số dư bằng Transaction và Pessimistic Write Lock trên MySQL.

6. **Habit & Fitness Tracker (Habit Module):**
   - Theo dõi các thói quen tốt và lịch tập thể thao hàng ngày (chạy bộ, tập gym, đọc sách, uống nước).
   - Tính toán chuỗi ngày duy trì liên tục (Current Streak, Longest Streak) và hiển thị biểu đồ nhiệt (Contribution Heatmap 365 ngày).

7. **Daily Journal & Mood Analytics (Journal Module):**
   - Soạn thảo nhật ký hàng ngày bằng định dạng Markdown.
   - Ghi nhận chỉ số tâm trạng (Mood Score từ 1 đến 5) và các nhãn cảm xúc, liên kết trực tiếp với các công việc và buổi học đã hoàn thành trong ngày đó.

8. **Ambient Focus Soundscape (Audio Module):**
   - Trình phát nhạc nền Lo-Fi và âm thanh trắng (Tiếng mưa, Tiếng suối, Quán cà phê, Sóng biển) tích hợp trực tiếp ở thanh công cụ dưới cùng, hỗ trợ trộn nhiều luồng âm thanh (Multi-track Audio Mixer) giúp tăng cường sự tập trung.

---

## 3. Công Nghệ Sử Dụng (Tech Stack - 100% Free-tier)

### Backend
- **Ngôn ngữ:** Java 21 LTS
- **Framework:** Spring Boot 3.3.x
- **Modules:**
  - `spring-boot-starter-web` (RESTful API, Server-Sent Events)
  - `spring-boot-starter-data-jpa` (Hibernate 6, JPA Specification, EntityGraph)
  - `spring-boot-starter-security` & `spring-boot-starter-oauth2-client` (JWT + Google OAuth2)
  - `spring-boot-starter-validation` (Jakarta Bean Validation)
  - `spring-boot-starter-aop` (Audit Logging, Performance Profiling)
  - `spring-boot-starter-mail` (Email Reminder Notification qua Gmail SMTP)
  - `spring-boot-starter-cache` + Caffeine Cache (In-memory Caching)
- **Database Migration:** Flyway
- **Tài liệu API:** SpringDoc OpenAPI 3 (Swagger UI)
- **Xử lý tài liệu PDF/Word:** Apache PDFBox / Apache POI
- **Kiểm thử:** JUnit 5, Mockito, AssertJ

### Database & Thiết Kế
- **Hệ quản trị CSDL:** MySQL 8.0+
- **Công cụ thiết kế & tối ưu:** MySQL Workbench 8.0 (ERD Modeling, Forward/Reverse Engineering, Visual Explain Plan, Index Tuning)
- **Cloud Database (Free-tier):** Aiven for MySQL hoặc TiDB Cloud Serverless

### Frontend
- **Framework:** Next.js 14 (App Router) + React 18 + TypeScript
- **Styling & UI System:** TailwindCSS + shadcn/ui (Radix UI Primitives) + Lucide Icons
- **State Management & Data Fetching:** TanStack Query (React Query v5) + Zustand
- **Calendar & Drag-Drop:** FullCalendar / dnd-kit
- **Biểu đồ & Thống kê:** Recharts
- **Trình soạn thảo:** TipTap hoặc React-Markdown
- **Web API đặc biệt:** Document Picture-in-Picture API, Web Audio API, Notification API

### DevOps & Triển Khai
- **Containerization:** Docker & Docker Compose
- **CI/CD:** GitHub Actions
- **Hosting (Free-tier):** Render (Backend Docker) & Vercel (Frontend Next.js)

---

## 4. Yêu Cầu Môi Trường (Prerequisites)

- **JDK:** OpenJDK 21 trở lên
- **Maven:** 3.9+ (hoặc sử dụng Maven Wrapper `./mvnw` đi kèm)
- **MySQL Server:** 8.0+ và **MySQL Workbench** 8.0+
- **Node.js:** v20 LTS trở lên và `npm` hoặc `pnpm`
- **Trình duyệt khuyến nghị:** Google Chrome 116+ hoặc Microsoft Edge 116+ (để hỗ trợ đầy đủ tính năng Document Picture-in-Picture API cho cửa sổ Mini HUD)

---

## 5. Hướng Dẫn Khởi Chạy Nhanh (Quick Start)

### Bước 1: Khởi tạo Cơ sở dữ liệu trên MySQL Workbench
1. Mở **MySQL Workbench** và kết nối tới Local MySQL Instance (`localhost:3306`).
2. Thực thi câu lệnh SQL sau để tạo schema:
   ```sql
   CREATE DATABASE IF NOT EXISTS omnilife_db
   CHARACTER SET utf8mb4
   COLLATE utf8mb4_unicode_ci;
   ```

### Bước 2: Cấu hình và chạy Backend (Spring Boot)
1. Di chuyển vào thư mục backend:
   ```bash
   cd backend
   ```
2. Sao chép file biến môi trường mẫu:
   ```bash
   cp .env.example .env
   ```
3. Cập nhật thông tin kết nối MySQL và Google OAuth2 Client ID trong file `.env` hoặc `src/main/resources/application-local.yml`.
4. Chạy ứng dụng (Flyway sẽ tự động khởi tạo toàn bộ bảng và dữ liệu mẫu):
   ```bash
   ./mvnw spring-boot:run
   ```
5. Truy cập Swagger UI để kiểm tra API tại: `http://localhost:8080/swagger-ui.html`

### Bước 3: Cấu hình và chạy Frontend (Next.js)
1. Di chuyển vào thư mục frontend:
   ```bash
   cd frontend
   ```
2. Cài đặt các gói phụ thuộc:
   ```bash
   npm install
   ```
3. Khởi tạo biến môi trường:
   ```bash
   cp .env.example .env.local
   ```
4. Chạy máy chủ phát triển:
   ```bash
   npm run dev
   ```
5. Mở trình duyệt tại địa chỉ: `http://localhost:3000`

---

## 6. Cấu Trúc Tài Liệu Đặc Tả (Documentation Index)

Toàn bộ tài liệu thiết kế kiến trúc và đặc tả kỹ thuật được lưu trữ trong thư mục `docs/`:
1. [README.md](./README.md) - Tổng quan dự án và hướng dẫn khởi chạy.
2. [product.md](./product.md) - Tầm nhìn sản phẩm, User Personas, USP và phạm vi hệ thống.
3. [spec.md](./spec.md) - Đặc tả yêu cầu chức năng, phi chức năng, luồng nghiệp vụ và công thức tính toán.
4. [design-system.md](./design-system.md) - Quy chuẩn UI/UX, bảng màu, Typography và danh mục Component.
5. [assets.md](./assets.md) - Danh sách tài nguyên tĩnh và bộ dữ liệu mẫu (Seed Data).
6. [folder-structure.md](./folder-structure.md) - Cấu trúc cây thư mục chi tiết của Backend và Frontend.
7. [architecture.md](./architecture.md) - Kiến trúc hệ thống, luồng dữ liệu, bảo mật và cơ chế Spring Boot cốt lõi.
8. [database.md](./database.md) - Thiết kế ERD, chi tiết bảng, ràng buộc và chiến lược đánh Index trên MySQL.
9. [api.md](./api.md) - Đặc tả toàn bộ RESTful API Endpoints, Request/Response JSON và mã lỗi.
10. [git-workflow.md](./git-workflow.md) - Quy chuẩn phân nhánh và Conventional Commits.
11. [github-setup.md](./github-setup.md) - Cấu hình `.env.example`, `.gitignore` và GitHub Actions CI/CD.
12. [deploy.md](./deploy.md) - Hướng dẫn triển khai thực tế lên hạ tầng miễn phí.
13. [roadmap.md](./roadmap.md) - Lộ trình phát triển chia theo các Phase và danh sách Task kỹ thuật.
14. [worklog.md](./worklog.md) - Bảng theo dõi tiến độ thực thi từng Task.
